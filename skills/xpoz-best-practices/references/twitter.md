# Twitter/X Tools Reference

## Table of Contents

- [User Tools](#user-tools)
  - [getTwitterUser](#getTwitterUser)
  - [getTwitterUsers](#getTwitterUsers)
  - [searchTwitterUsers](#searchTwitterUsers)
  - [getTwitterUserConnections](#getTwitterUserConnections)
  - [getTwitterUsersByKeywords](#getTwitterUsersByKeywords)
- [Post Tools](#post-tools)
  - [getTwitterPostsByIds](#getTwitterPostsByIds)
  - [getTwitterPostsByAuthor](#getTwitterPostsByAuthor)
  - [getTwitterPostsByKeywords](#getTwitterPostsByKeywords)
  - [getTwitterPostRetweets](#getTwitterPostRetweets)
  - [getTwitterPostQuotes](#getTwitterPostQuotes)
  - [getTwitterPostComments](#getTwitterPostComments)
  - [getTwitterPostInteractingUsers](#getTwitterPostInteractingUsers)
  - [countTweets](#countTweets)

---

## User Fields

These fields are available on all user-returning tools:

`id`, `username`, `name`, `description`, `location`, `verified`, `followersCount`, `followingCount`, `tweetCount`, `profileImageUrl`, `createdAt`

## Post Fields

These fields are available on all post-returning tools:

`id`, `text`, `authorId`, `authorUsername`, `createdAt`, `createdAtDate`, `likeCount`, `retweetCount`, `replyCount`, `quoteCount`, `impressionCount`, `bookmarkCount`, `lang`, `isRetweet`, `isReply`, `hashtags`, `mentions`, `mediaUrls`, `country`, `region`, `city`

---

## User Tools

### getTwitterUser

Get a single Twitter user by ID or username.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `identifier` | string | Yes | The user ID or username to look up |
| `identifierType` | `"id"` \| `"username"` | Yes | Whether `identifier` is an ID or username |
| `fields` | string[] | No | Fields to return (see [User Fields](#user-fields)) |

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterUser",
  "arguments": {
    "identifier": "elonmusk",
    "identifierType": "username",
    "fields": ["id", "username", "name", "followersCount", "verified"]
  }
}
```

**Python SDK:**
```python
user = client.twitter.get_user(
    "elonmusk",
    identifier_type="username",
    fields=["id", "username", "name", "followers_count", "verified"]
)
```

**TypeScript SDK:**
```typescript
const user = await client.twitter.getUser("elonmusk", {
  identifierType: "username",
  fields: ["id", "username", "name", "followersCount", "verified"],
});
```

**CLI:**
```bash
xpoz-cli twitter get_user --identifier elonmusk --identifier-type username --fields id username name followers_count verified
```

---

### getTwitterUsers

Get 1-100 Twitter users by IDs or usernames in a single call.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `identifiers` | string[] | Yes | 1-100 user IDs or usernames |
| `identifierType` | `"id"` \| `"username"` | Yes | Whether identifiers are IDs or usernames |
| `fields` | string[] | No | Fields to return (see [User Fields](#user-fields)) |
| `forceLatest` | boolean | No | Bypass cache and fetch fresh data from API |

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterUsers",
  "arguments": {
    "identifiers": ["elonmusk", "sama", "kaborofficial"],
    "identifierType": "username",
    "fields": ["id", "username", "name", "followersCount"]
  }
}
```

**Python SDK:**
```python
users = client.twitter.get_users(
    ["elonmusk", "sama", "kaborofficial"],
    identifier_type="username",
    fields=["id", "username", "name", "followers_count"]
)
```

**TypeScript SDK:**
```typescript
const users = await client.twitter.getUsers(
  ["elonmusk", "sama", "kaborofficial"],
  {
    identifierType: "username",
    fields: ["id", "username", "name", "followersCount"],
  }
);
```

**CLI:**
```bash
xpoz-cli twitter get_users --identifiers elonmusk sama kaborofficial --identifier-type username --fields id username name followers_count
```

---

### searchTwitterUsers

Search for Twitter users by name or username via fuzzy matching.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Name or username to search for |
| `limit` | number | No | Max results to return (default 10, max 10) |
| `fields` | string[] | No | Fields to return (see [User Fields](#user-fields)) |

#### Examples

**MCP:**
```json
{
  "tool": "searchTwitterUsers",
  "arguments": {
    "name": "Elon",
    "limit": 5,
    "fields": ["id", "username", "name", "followersCount"]
  }
}
```

**Python SDK:**
```python
users = client.twitter.search_users(
    "Elon",
    limit=5,
    fields=["id", "username", "name", "followers_count"]
)
```

**TypeScript SDK:**
```typescript
const users = await client.twitter.searchUsers("Elon", {
  limit: 5,
  fields: ["id", "username", "name", "followersCount"],
});
```

**CLI:**
```bash
xpoz-cli twitter search_users --name Elon --limit 5 --fields id username name followers_count
```

---

### getTwitterUserConnections

Get followers or following list for a Twitter user.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `username` | string | Yes | Twitter username (without @) |
| `connectionType` | `"followers"` \| `"following"` | Yes | Type of connection to retrieve |
| `fields` | string[] | No | Fields to return (see [User Fields](#user-fields)) |
| `forceLatest` | boolean | No | Bypass cache and fetch fresh data from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | Response mode (default `"fast"`) |
| `limit` | number | No | Max results to return |
| `pageNumber` | number | No | Page number for paging mode |
| `pageNumberEnd` | number | No | End page for paging mode |
| `tableName` | string | No | Table name for paging mode (from operation result) |

#### Response Modes

| Mode | Behavior |
|------|----------|
| `"fast"` | Returns up to 300 results immediately |
| `"paging"` | Async operation -- returns `operationId`, poll with `checkOperationStatus` |
| `"csv"` | Async CSV export -- returns download URL when complete |

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterUserConnections",
  "arguments": {
    "username": "elonmusk",
    "connectionType": "followers",
    "fields": ["id", "username", "name", "followersCount"],
    "responseType": "fast"
  }
}
```

**Python SDK:**
```python
followers = client.twitter.get_user_connections(
    "elonmusk",
    connection_type="followers",
    fields=["id", "username", "name", "followers_count"]
)
```

**TypeScript SDK:**
```typescript
const followers = await client.twitter.getUserConnections(
  "elonmusk",
  "followers",
  {
    fields: ["id", "username", "name", "followersCount"],
  }
);
```

**CLI:**
```bash
xpoz-cli twitter get_user_connections --username elonmusk --connection-type followers --fields id username name followers_count --response-type fast
```

---

### getTwitterUsersByKeywords

Find Twitter users who posted content matching keywords. Returns users aggregated from matching posts.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Keyword query (supports boolean operators) |
| `fields` | string[] | No | Fields to return (see [User Fields](#user-fields) plus aggregate fields below) |
| `startDate` | string | No | Start date in YYYY-MM-DD format |
| `endDate` | string | No | End date in YYYY-MM-DD format |
| `language` | string | No | ISO language code (e.g., `"en"`) |
| `forceLatest` | boolean | No | Bypass cache and fetch fresh data from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | Response mode (default `"fast"`) |
| `limit` | number | No | Max results to return |
| `pageNumber` | number | No | Page number for paging mode |
| `pageNumberEnd` | number | No | End page for paging mode |
| `tableName` | string | No | Table name for paging mode (from operation result) |

#### Aggregate Fields

These fields must be explicitly requested in the `fields` array:

`aggRelevance`, `relevantTweetsCount`, `relevantTweetsImpressionsSum`, `relevantTweetsLikesSum`, `relevantTweetsQuotesSum`, `relevantTweetsRepliesSum`, `relevantTweetsRetweetsSum`

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterUsersByKeywords",
  "arguments": {
    "query": "\"artificial intelligence\" AND safety",
    "fields": ["id", "username", "name", "followersCount", "aggRelevance", "relevantTweetsCount"],
    "startDate": "2026-01-01",
    "endDate": "2026-06-10",
    "language": "en"
  }
}
```

**Python SDK:**
```python
users = client.twitter.get_users_by_keywords(
    "\"artificial intelligence\" AND safety",
    fields=["id", "username", "name", "followers_count", "agg_relevance", "relevant_tweets_count"],
    start_date="2026-01-01",
    end_date="2026-06-10",
    language="en"
)
```

**TypeScript SDK:**
```typescript
const users = await client.twitter.getUsersByKeywords(
  '"artificial intelligence" AND safety',
  {
    fields: ["id", "username", "name", "followersCount", "aggRelevance", "relevantTweetsCount"],
    startDate: "2026-01-01",
    endDate: "2026-06-10",
    language: "en",
  }
);
```

**CLI:**
```bash
xpoz-cli twitter get_users_by_keywords --query '"artificial intelligence" AND safety' --fields id username name followers_count agg_relevance relevant_tweets_count --start-date 2026-01-01 --end-date 2026-06-10 --language en
```

---

## Post Tools

### getTwitterPostsByIds

Get 1-100 Twitter posts by their numeric IDs.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `postIds` | string[] | Yes | 1-100 numeric post IDs |
| `fields` | string[] | No | Fields to return (see [Post Fields](#post-fields)) |
| `forceLatest` | boolean | No | Bypass cache and fetch fresh data from API |

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterPostsByIds",
  "arguments": {
    "postIds": ["1234567890123456789", "9876543210987654321"],
    "fields": ["id", "text", "authorUsername", "retweetCount", "impressionCount"]
  }
}
```

**Python SDK:**
```python
posts = client.twitter.get_posts_by_ids(
    ["1234567890123456789", "9876543210987654321"],
    fields=["id", "text", "author_username", "retweet_count", "impression_count"]
)
```

**TypeScript SDK:**
```typescript
const posts = await client.twitter.getPostsByIds(
  ["1234567890123456789", "9876543210987654321"],
  {
    fields: ["id", "text", "authorUsername", "retweetCount", "impressionCount"],
  }
);
```

**CLI:**
```bash
xpoz-cli twitter get_posts_by_ids --post-ids 1234567890123456789 9876543210987654321 --fields id text author_username retweet_count impression_count
```

---

### getTwitterPostsByAuthor

Get posts from a Twitter user by their username.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `username` | string | Yes | Twitter username (without @ symbol) |
| `fields` | string[] | No | Fields to return (see [Post Fields](#post-fields); default: `id`, `text`, `authorUsername`, `createdAtDate`) |
| `startDate` | string | No | Start date in YYYY-MM-DD format |
| `endDate` | string | No | End date in YYYY-MM-DD format |
| `forceLatest` | boolean | No | Bypass cache and fetch fresh data from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | Response mode (default `"fast"`) |
| `limit` | number | No | Max results to return |
| `pageNumber` | number | No | Page number for paging mode |
| `pageNumberEnd` | number | No | End page for paging mode |
| `tableName` | string | No | Table name for paging mode (from operation result) |

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterPostsByAuthor",
  "arguments": {
    "username": "sama",
    "fields": ["id", "text", "createdAtDate", "retweetCount", "impressionCount"],
    "startDate": "2026-01-01",
    "endDate": "2026-06-10"
  }
}
```

**Python SDK:**
```python
posts = client.twitter.get_posts_by_author(
    "sama",
    fields=["id", "text", "created_at_date", "retweet_count", "impression_count"],
    start_date="2026-01-01",
    end_date="2026-06-10"
)
```

**TypeScript SDK:**
```typescript
const posts = await client.twitter.getPostsByAuthor("sama", {
  fields: ["id", "text", "createdAtDate", "retweetCount", "impressionCount"],
  startDate: "2026-01-01",
  endDate: "2026-06-10",
});
```

**CLI:**
```bash
xpoz-cli twitter get_posts_by_author --username sama --fields id text created_at_date retweet_count impression_count --start-date 2026-01-01 --end-date 2026-06-10
```

---

### getTwitterPostsByKeywords

Search Twitter posts by keywords with boolean query support.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Keyword query (supports boolean operators: AND, OR, NOT, exact phrases, grouping) |
| `fields` | string[] | No | Fields to return (see [Post Fields](#post-fields); default: `id`, `text`, `authorUsername`, `createdAtDate`) |
| `startDate` | string | No | Start date in YYYY-MM-DD format |
| `endDate` | string | No | End date in YYYY-MM-DD format |
| `authorUsername` | string | No | Filter to posts by this username |
| `authorId` | string | No | Filter to posts by this user ID |
| `language` | string | No | ISO language code (e.g., `"en"`) |
| `filterOutRetweets` | boolean | No | Exclude retweets from results |
| `forceLatest` | boolean | No | Bypass cache and fetch fresh data from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | Response mode (default `"fast"`) |
| `limit` | number | No | Max results to return |
| `pageNumber` | number | No | Page number for paging mode |
| `pageNumberEnd` | number | No | End page for paging mode |
| `tableName` | string | No | Table name for paging mode (from operation result) |

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterPostsByKeywords",
  "arguments": {
    "query": "(\"machine learning\" OR \"deep learning\") AND python",
    "fields": ["id", "text", "authorUsername", "createdAtDate", "retweetCount", "lang"],
    "startDate": "2026-05-01",
    "endDate": "2026-06-10",
    "language": "en",
    "filterOutRetweets": true
  }
}
```

**Python SDK:**
```python
posts = client.twitter.search_posts(
    "(\"machine learning\" OR \"deep learning\") AND python",
    fields=["id", "text", "author_username", "created_at_date", "retweet_count", "lang"],
    start_date="2026-05-01",
    end_date="2026-06-10",
    language="en",
    filter_out_retweets=True
)
```

**TypeScript SDK:**
```typescript
const posts = await client.twitter.searchPosts(
  '("machine learning" OR "deep learning") AND python',
  {
    fields: ["id", "text", "authorUsername", "createdAtDate", "retweetCount", "lang"],
    startDate: "2026-05-01",
    endDate: "2026-06-10",
    language: "en",
    filterOutRetweets: true,
  }
);
```

**CLI:**
```bash
xpoz-cli twitter search_posts --query '("machine learning" OR "deep learning") AND python' --fields id text author_username created_at_date retweet_count lang --start-date 2026-05-01 --end-date 2026-06-10 --language en --filter-out-retweets
```

---

### getTwitterPostRetweets

Get retweets of a specific post. Database-only lookup with no API fallback.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `postId` | string | Yes | Numeric ID of the original post |
| `fields` | string[] | No | Fields to return (see [Post Fields](#post-fields)) |
| `startDate` | string | No | Start date in YYYY-MM-DD format |
| `endDate` | string | No | End date in YYYY-MM-DD format |
| `responseType` | `"fast"` \| `"paging"` | No | Response mode (default `"fast"`; CSV not supported) |
| `limit` | number | No | Max results to return |
| `pageNumber` | number | No | Page number for paging mode |
| `pageNumberEnd` | number | No | End page for paging mode |
| `tableName` | string | No | Table name for paging mode (from operation result) |

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterPostRetweets",
  "arguments": {
    "postId": "1234567890123456789",
    "fields": ["id", "text", "authorUsername", "createdAtDate"]
  }
}
```

**Python SDK:**
```python
retweets = client.twitter.get_retweets(
    "1234567890123456789",
    fields=["id", "text", "author_username", "created_at_date"]
)
```

**TypeScript SDK:**
```typescript
const retweets = await client.twitter.getRetweets("1234567890123456789", {
  fields: ["id", "text", "authorUsername", "createdAtDate"],
});
```

**CLI:**
```bash
xpoz-cli twitter get_retweets --post-id 1234567890123456789 --fields id text author_username created_at_date
```

---

### getTwitterPostQuotes

Get quote tweets of a specific post. Refreshes from API if data is stale (>10 days).

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `postId` | string | Yes | Numeric ID of the original post |
| `fields` | string[] | No | Fields to return (see [Post Fields](#post-fields)) |
| `startDate` | string | No | Start date in YYYY-MM-DD format |
| `forceLatest` | boolean | No | Bypass cache and fetch fresh data from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | Response mode (default `"fast"`) |
| `limit` | number | No | Max results to return |
| `pageNumber` | number | No | Page number for paging mode |
| `pageNumberEnd` | number | No | End page for paging mode |
| `tableName` | string | No | Table name for paging mode (from operation result) |

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterPostQuotes",
  "arguments": {
    "postId": "1234567890123456789",
    "fields": ["id", "text", "authorUsername", "createdAtDate", "impressionCount"],
    "forceLatest": true
  }
}
```

**Python SDK:**
```python
quotes = client.twitter.get_quotes(
    "1234567890123456789",
    fields=["id", "text", "author_username", "created_at_date", "impression_count"],
    force_latest=True
)
```

**TypeScript SDK:**
```typescript
const quotes = await client.twitter.getQuotes("1234567890123456789", {
  fields: ["id", "text", "authorUsername", "createdAtDate", "impressionCount"],
  forceLatest: true,
});
```

**CLI:**
```bash
xpoz-cli twitter get_quotes --post-id 1234567890123456789 --fields id text author_username created_at_date impression_count --force-latest
```

---

### getTwitterPostComments

Get replies to a specific post. Refreshes from API if data is stale (>10 days).

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `postId` | string | Yes | Numeric ID of the post |
| `fields` | string[] | No | Fields to return (see [Post Fields](#post-fields)) |
| `startDate` | string | No | Start date in YYYY-MM-DD format |
| `forceLatest` | boolean | No | Bypass cache and fetch fresh data from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | Response mode (default `"fast"`) |
| `limit` | number | No | Max results to return |
| `pageNumber` | number | No | Page number for paging mode |
| `pageNumberEnd` | number | No | End page for paging mode |
| `tableName` | string | No | Table name for paging mode (from operation result) |

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterPostComments",
  "arguments": {
    "postId": "1234567890123456789",
    "fields": ["id", "text", "authorUsername", "createdAtDate"],
    "forceLatest": true
  }
}
```

**Python SDK:**
```python
comments = client.twitter.get_comments(
    "1234567890123456789",
    fields=["id", "text", "author_username", "created_at_date"],
    force_latest=True
)
```

**TypeScript SDK:**
```typescript
const comments = await client.twitter.getComments("1234567890123456789", {
  fields: ["id", "text", "authorUsername", "createdAtDate"],
  forceLatest: true,
});
```

**CLI:**
```bash
xpoz-cli twitter get_comments --post-id 1234567890123456789 --fields id text author_username created_at_date --force-latest
```

---

### getTwitterPostInteractingUsers

Get users who interacted with a specific post by commenting, quoting, or retweeting.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `postId` | string | Yes | Numeric ID of the post |
| `interactionType` | `"commenters"` \| `"quoters"` \| `"retweeters"` | Yes | Type of interaction to retrieve |
| `fields` | string[] | No | Fields to return (see [User Fields](#user-fields)) |
| `startDate` | string | No | Start date in YYYY-MM-DD format |
| `endDate` | string | No | End date in YYYY-MM-DD format |
| `forceLatest` | boolean | No | Bypass cache and fetch fresh data from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | Response mode (default `"fast"`) |
| `limit` | number | No | Max results to return |
| `pageNumber` | number | No | Page number for paging mode |
| `pageNumberEnd` | number | No | End page for paging mode |
| `tableName` | string | No | Table name for paging mode (from operation result) |

#### Examples

**MCP:**
```json
{
  "tool": "getTwitterPostInteractingUsers",
  "arguments": {
    "postId": "1234567890123456789",
    "interactionType": "commenters",
    "fields": ["id", "username", "name", "followersCount"]
  }
}
```

**Python SDK:**
```python
users = client.twitter.get_post_interacting_users(
    "1234567890123456789",
    interaction_type="commenters",
    fields=["id", "username", "name", "followers_count"]
)
```

**TypeScript SDK:**
```typescript
const users = await client.twitter.getPostInteractingUsers(
  "1234567890123456789",
  "commenters",
  {
    fields: ["id", "username", "name", "followersCount"],
  }
);
```

**CLI:**
```bash
xpoz-cli twitter get_post_interacting_users --post-id 1234567890123456789 --interaction-type commenters --fields id username name followers_count
```

---

### countTweets

Count tweets matching a phrase over a date range. Returns a single number.

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `phrase` | string | Yes | Phrase to count tweets for |
| `startDate` | string | No | Start date in YYYY-MM-DD format (default: 6 months ago) |
| `endDate` | string | No | End date in YYYY-MM-DD format (default: today) |

#### Examples

**MCP:**
```json
{
  "tool": "countTweets",
  "arguments": {
    "phrase": "artificial intelligence",
    "startDate": "2026-01-01",
    "endDate": "2026-06-10"
  }
}
```

**Python SDK:**
```python
count = client.twitter.count_posts(
    "artificial intelligence",
    start_date="2026-01-01",
    end_date="2026-06-10"
)
# count is an int
print(f"Found {count:,} tweets")
```

**TypeScript SDK:**
```typescript
const count = await client.twitter.countPosts("artificial intelligence", {
  startDate: "2026-01-01",
  endDate: "2026-06-10",
});
// count is a number
console.log(`Found ${count.toLocaleString()} tweets`);
```

**CLI:**
```bash
xpoz-cli twitter count_posts --phrase "artificial intelligence" --start-date 2026-01-01 --end-date 2026-06-10
```
