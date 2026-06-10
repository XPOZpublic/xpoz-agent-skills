# Pagination and Export

## Table of Contents

- [Response Modes](#response-modes)
  - [Fast Mode (default)](#fast-mode-default)
  - [Paging Mode](#paging-mode)
  - [CSV Export Mode](#csv-export-mode)
- [The operationId Polling Pattern](#the-operationid-polling-pattern)
- [Pagination Parameters](#pagination-parameters)
- [CSV Export](#csv-export)
- [Cancel Operations](#cancel-operations)
- [Field Selection](#field-selection)

---

## Response Modes

All paginated tools support three response modes via the `responseType` parameter:


| Mode   | Value              | Behavior                                                                     | Best For                                    |
| ------ | ------------------ | ---------------------------------------------------------------------------- | ------------------------------------------- |
| Fast   | `"fast"` (default) | Returns up to 300 results immediately in a single response                   | Quick lookups, exploration, small datasets  |
| Paging | `"paging"`         | Async operation — returns an `operationId`, poll with `checkOperationStatus` | Large datasets, page-by-page iteration      |
| CSV    | `"csv"`            | Async S3 export — returns a download URL when complete                       | Bulk export, offline analysis, spreadsheets |


### Fast Mode (default)

Returns up to 300 results synchronously. No polling needed.

**MCP:**

```json
{
  "tool": "getTwitterPostsByKeywords",
  "arguments": {
    "query": "artificial intelligence",
    "limit": 100
  }
}
```

**Python SDK:**

```python
results = client.twitter.search_posts("artificial intelligence", limit=100)
for post in results.data:
    print(post["text"])
```

**TypeScript SDK:**

```typescript
const results = await client.twitter.searchPosts("artificial intelligence", {
  limit: 100,
});
for (const post of results.data) {
  console.log(post.text);
}
```

**CLI:**

```bash
xpoz-cli twitter search_posts --query "artificial intelligence" --limit 100
```

### Paging Mode

Returns an `operationId` immediately. Poll `checkOperationStatus` to get results page by page.

**MCP:**

```json
{
  "tool": "getTwitterPostsByKeywords",
  "arguments": {
    "query": "artificial intelligence",
    "responseType": "paging"
  }
}
```

Response includes `operationId` — use it to poll for results (see [polling pattern](#the-operationid-polling-pattern)).

**Python SDK:**

```python
results = client.twitter.search_posts(
    "artificial intelligence",
    response_type="paging"
)
# PaginatedResult handles polling automatically
print(f"Page 1: {len(results.data)} results")
if results.has_next_page():
    page2 = results.next_page()
```

**TypeScript SDK:**

```typescript
const results = await client.twitter.searchPosts("artificial intelligence", {
  responseType: "paging",
});
console.log(`Page 1: ${results.data.length} results`);
if (results.hasNextPage()) {
  const page2 = await results.nextPage();
}
```

**CLI:**

```bash
# Walk through all pages automatically (stops after 5 pages)
xpoz-cli twitter search_posts --query "artificial intelligence" --all-pages --max-pages 5

# Jump to a specific page
xpoz-cli twitter search_posts --query "artificial intelligence" --page 3
```

### CSV Export Mode

Triggers an async export to S3. Poll `checkOperationStatus` to get the `downloadUrl` when ready.

**MCP:**

```json
{
  "tool": "getTwitterPostsByKeywords",
  "arguments": {
    "query": "artificial intelligence",
    "responseType": "csv"
  }
}
```

**Python SDK:**

```python
results = client.twitter.search_posts(
    "artificial intelligence",
    response_type="csv"
)
csv_url = results.export_csv()
print(f"Download CSV: {csv_url}")
```

**TypeScript SDK:**

```typescript
const results = await client.twitter.searchPosts("artificial intelligence", {
  responseType: "csv",
});
const csvUrl = await results.exportCsv();
console.log(`Download CSV: ${csvUrl}`);
```

**CLI:**

```bash
xpoz-cli twitter search_posts --query "artificial intelligence" --response-type csv
```

---

## The operationId Polling Pattern (MCP only)

When using `paging` or `csv` response modes via MCP, the initial response returns an `operationId`. You must poll `checkOperationStatus` to get the results.

**The Python SDK, TypeScript SDK, and CLI handle polling automatically — you never need to poll manually.**

### MCP Polling

1. Call the tool with `responseType: "paging"` or `responseType: "csv"`
2. Extract `operationId` from the response
3. Call `checkOperationStatus` with the `operationId`
4. If `status` is `"running"`, wait ~5 seconds and repeat step 3
5. Keep polling until `status` is `success`, `no_data`, `error`, or `cancelled` — do not stop while running

```
Call getTwitterPostsByKeywords:
  query: "bitcoin AND ethereum"
  responseType: "paging"

→ Response: { operationId: "op_abc123", status: "running" }

Call checkOperationStatus:
  operationId: "op_abc123"

→ If status: "running" → wait ~5 seconds, call again
→ If status: "success" → results are in the response (with tableName, pageNumber, totalRows)
```

### Status Values


| Status      | Meaning                                           |
| ----------- | ------------------------------------------------- |
| `running`   | Still processing — wait ~5 seconds and poll again |
| `success`   | Results ready                                     |
| `no_data`   | No matching results found                         |
| `error`     | Operation failed                                  |
| `cancelled` | Cancelled via `cancelOperation`                   |


---

## Pagination Parameters

After a paging operation completes, use these parameters to navigate through pages:


| Parameter       | Type   | Description                                                                         |
| --------------- | ------ | ----------------------------------------------------------------------------------- |
| `pageNumber`    | number | 1-indexed page number to fetch                                                      |
| `pageNumberEnd` | number | Fetch pages from `pageNumber` through `pageNumberEnd` (bulk fetch)                  |
| `tableName`     | string | Cached table name from the first request's response — required for subsequent pages |


### How Pagination Works

1. The first paging request processes the query and caches results in a temporary table
2. The response includes `tableName` — pass this on all subsequent page requests
3. Use `pageNumber` to jump to any page
4. Use `pageNumberEnd` to fetch a range of pages in one call

### MCP Pagination Example

**First request (page 1):**

```json
{
  "tool": "getTwitterPostsByKeywords",
  "arguments": {
    "query": "machine learning",
    "responseType": "paging"
  }
}
```

Response includes:

```json
{
  "pagination": {
    "tableName": "tmp_twitter_posts_abc123",
    "pageNumber": 1,
    "totalRows": 5000,
    "totalPages": 50
  }
}
```

**Subsequent request (page 2):**

```json
{
  "tool": "getTwitterPostsByKeywords",
  "arguments": {
    "query": "machine learning",
    "responseType": "paging",
    "tableName": "tmp_twitter_posts_abc123",
    "pageNumber": 2
  }
}
```

**Bulk fetch (pages 2-5):**

```json
{
  "tool": "getTwitterPostsByKeywords",
  "arguments": {
    "query": "machine learning",
    "responseType": "paging",
    "tableName": "tmp_twitter_posts_abc123",
    "pageNumber": 2,
    "pageNumberEnd": 5
  }
}
```

### Python SDK Pagination Example

```python
results = client.twitter.search_posts(
    "machine learning",
    response_type="paging"
)

# Automatic page navigation
print(f"Page 1: {len(results.data)} results")

if results.has_next_page():
    page2 = results.next_page()
    print(f"Page 2: {len(page2.data)} results")

# Jump to a specific page
page10 = results.get_page(10)
print(f"Page 10: {len(page10.data)} results")
```

### TypeScript SDK Pagination Example

```typescript
const results = await client.twitter.searchPosts("machine learning", {
  responseType: "paging",
});

console.log(`Page 1: ${results.data.length} results`);

if (results.hasNextPage()) {
  const page2 = await results.nextPage();
  console.log(`Page 2: ${page2.data.length} results`);
}

// Jump to a specific page
const page10 = await results.getPage(10);
console.log(`Page 10: ${page10.data.length} results`);
```

### CLI Pagination Example

```bash
# Walk through all pages automatically
xpoz-cli twitter search_posts --query "machine learning" --all-pages --max-pages 10

# Jump to a specific page
xpoz-cli twitter search_posts --query "machine learning" --page 10
```

---

## CSV Export

There are two ways to trigger a CSV export:

### Option 1: Set responseType to "csv"

Pass `responseType: "csv"` in the initial request. The operation exports directly to S3.

**MCP:**

```json
{
  "tool": "getTwitterPostsByKeywords",
  "arguments": {
    "query": "cryptocurrency",
    "responseType": "csv"
  }
}
```

Poll `checkOperationStatus` — when `status` is `"success"`, the response contains `downloadUrl`.

**Python SDK:**

```python
results = client.twitter.search_posts(
    "cryptocurrency",
    response_type="csv"
)
csv_url = results.export_csv()
print(f"Download: {csv_url}")
```

**TypeScript SDK:**

```typescript
const results = await client.twitter.searchPosts("cryptocurrency", {
  responseType: "csv",
});
const csvUrl = await results.exportCsv();
console.log(`Download: ${csvUrl}`);
```

**CLI:**

```bash
xpoz-cli twitter search_posts --query "cryptocurrency" --response-type csv
```

### Option 2: Export after paging with dataDumpExportOperationId

After a paging request completes, the response may include a `dataDumpExportOperationId`. Poll `checkOperationStatus` with this ID to get the CSV `downloadUrl`.

**MCP:**

```json
{
  "tool": "checkOperationStatus",
  "arguments": {
    "operationId": "the-dataDumpExportOperationId-value"
  }
}
```

When `status` is `"success"`, the response contains `downloadUrl` for the CSV file on S3.

**Python SDK:**

```python
results = client.twitter.search_posts(
    "cryptocurrency",
    response_type="paging"
)
# After viewing paged results, export the full dataset
csv_url = results.export_csv()
```

**TypeScript SDK:**

```typescript
const results = await client.twitter.searchPosts("cryptocurrency", {
  responseType: "paging",
});
// After viewing paged results, export the full dataset
const csvUrl = await results.exportCsv();
```

---

## Cancel Operations

Cancel a running operation using the `cancelOperation` MCP tool. This is only available via MCP — the SDKs and CLI do not expose a cancel method.

**MCP:**

```json
{
  "tool": "cancelOperation",
  "arguments": {
    "operationId": "op_abc123"
  }
}
```

After cancellation, `checkOperationStatus` returns `status: "cancelled"`.

---

## Field Selection

Pass a `fields` array to limit which fields are returned in the response. This reduces response size and improves performance.

### Key Rules

- Each platform has different available fields (see platform-specific references)
- MCP, TypeScript SDK, and CLI use **camelCase** field names: `likeCount`, `authorUsername`, `createdAt`
- Python SDK uses **snake_case** field names: `like_count`, `author_username`, `created_at`
- If `fields` is omitted, all available fields are returned

### MCP Field Selection

```json
{
  "tool": "getTwitterPostsByKeywords",
  "arguments": {
    "query": "AI startups",
    "fields": ["id", "text", "authorUsername", "likeCount", "retweetCount", "createdAt"]
  }
}
```

### Python SDK Field Selection

```python
results = client.twitter.search_posts(
    "AI startups",
    fields=["id", "text", "author_username", "like_count", "retweet_count", "created_at"]
)
```

### TypeScript SDK Field Selection

```typescript
const results = await client.twitter.searchPosts("AI startups", {
  fields: ["id", "text", "authorUsername", "likeCount", "retweetCount", "createdAt"],
});
```

### CLI Field Selection

```bash
xpoz-cli twitter search_posts --query "AI startups" --fields id text author_username like_count retweet_count created_at
```

CLI uses **snake_case** field names (same as Python SDK) and accepts them as space-separated values.

### Example Fields by Platform

**Twitter posts:** `id`, `text`, `authorId`, `authorUsername`, `createdAt`, `createdAtDate`, `likeCount`, `retweetCount`, `replyCount`, `quoteCount`, `impressionCount`, `bookmarkCount`, `lang`, `isRetweet`, `isReply`, `hashtags`, `mentions`, `mediaUrls`, `country`, `region`, `city`

**Instagram posts:** `id`, `strongId`, `authorId`, `authorUsername`, `caption`, `likeCount`, `commentCount`, `viewCount`, `mediaType`, `mediaUrls`, `hashtags`, `createdAt`

**Reddit posts:** `id`, `title`, `text`, `authorUsername`, `subreddit`, `score`, `upvoteRatio`, `commentCount`, `createdAt`, `url`, `permalink`

**TikTok posts:** `id`, `authorId`, `authorUsername`, `description`, `likeCount`, `commentCount`, `shareCount`, `viewCount`, `playCount`, `hashtags`, `createdAt`

For the complete field list per tool, see the platform-specific reference files.