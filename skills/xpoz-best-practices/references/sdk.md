# SDK Reference

## Table of Contents

- [Python SDK](#python-sdk)
  - [Installation](#installation)
  - [Client Setup](#client-setup)
  - [Async Client](#async-client)
  - [Context Manager](#context-manager)
- [TypeScript SDK](#typescript-sdk)
  - [Installation](#installation-1)
  - [Client Setup](#client-setup-1)
  - [Context Manager](#context-manager-1)
- [Namespace Pattern](#namespace-pattern)
- [PaginatedResult Helpers](#paginatedresult-helpers)
- [Field Naming Convention](#field-naming-convention)
- [Complete Method Reference](#complete-method-reference)
  - [Twitter (13 methods)](#twitter-13-methods)
  - [Instagram (9 methods)](#instagram-9-methods)
  - [Reddit (9 methods)](#reddit-9-methods)
  - [TikTok (9 methods)](#tiktok-9-methods)
  - [Tracking (3 methods)](#tracking-3-methods)

---

## Python SDK

### Installation

```bash
pip install xpoz
```

### Client Setup

```python
from xpoz import XpozClient

# Option 1: Reads XPOZ_API_KEY environment variable automatically
client = XpozClient()

# Option 2: Pass API key directly
client = XpozClient(api_key="your-api-key")

# Always close when done
client.close()
```

### Async Client

```python
from xpoz import AsyncXpozClient

async def main():
    client = AsyncXpozClient()
    results = await client.twitter.search_posts("artificial intelligence")
    print(f"Found {results.pagination.total_rows:,} tweets")
    await client.close()
```

### Context Manager

```python
from xpoz import XpozClient

# Sync context manager — auto-closes on exit
with XpozClient() as client:
    results = client.twitter.search_posts("artificial intelligence")
    print(f"Found {results.pagination.total_rows:,} tweets")
```

```python
from xpoz import AsyncXpozClient

# Async context manager
async with AsyncXpozClient() as client:
    results = await client.twitter.search_posts("artificial intelligence")
    print(f"Found {results.pagination.total_rows:,} tweets")
```

---

## TypeScript SDK

### Installation

```bash
npm install @xpoz/xpoz
```

### Client Setup

```typescript
import { XpozClient } from "@xpoz/xpoz";

// Option 1: Reads XPOZ_API_KEY environment variable automatically
const client = new XpozClient();
await client.connect(); // Required — must be called before any tool use

// Option 2: Pass API key directly
const client = new XpozClient({ apiKey: "your-api-key" });
await client.connect();

// Always close when done
await client.close();
```

### Context Manager

```typescript
import { XpozClient } from "@xpoz/xpoz";

// await using — auto-closes on scope exit
{
  await using client = new XpozClient();
  await client.connect();
  const results = await client.twitter.searchPosts("artificial intelligence");
  console.log(`Found ${results.pagination.totalRows.toLocaleString()} tweets`);
}
// client.close() called automatically
```

---

## Namespace Pattern

Both SDKs organize methods by platform namespace:

| Namespace | Accessor | Platforms |
|-----------|----------|-----------|
| Twitter | `client.twitter` | Twitter/X |
| Instagram | `client.instagram` | Instagram |
| Reddit | `client.reddit` | Reddit |
| TikTok | `client.tiktok` | TikTok |
| Tracking | `client.tracking` | Cross-platform tracking |
| Account | `client.account` | Account details & billing |

**Python:**
```python
client.twitter.search_posts("query")
client.instagram.get_user("cristiano")
client.reddit.search_posts("query")
client.tiktok.search_posts("query")
client.tracking.get_tracked_items()
client.account.get_account_details()
```

**TypeScript:**
```typescript
await client.twitter.searchPosts("query");
await client.instagram.getUser("cristiano");
await client.reddit.searchPosts("query");
await client.tiktok.searchPosts("query");
await client.tracking.getTrackedItems();
await client.account.getAccountDetails();
```

---

## PaginatedResult Helpers

Tools that return paginated data wrap results in a `PaginatedResult` object with navigation helpers.

### Python PaginatedResult

```python
results = client.twitter.search_posts("AI", response_type="paging")

# Check if more pages exist
results.has_next_page()  # → bool

# Fetch the next page
next_results = results.next_page()  # → PaginatedResult

# Jump to a specific page (1-indexed)
page5 = results.get_page(5)  # → PaginatedResult

# Export the full result set to CSV and get the download URL
csv_url = results.export_csv()  # → str (S3 download URL)
```

### TypeScript PaginatedResult

```typescript
const results = await client.twitter.searchPosts("AI", {
  responseType: "paging",
});

// Check if more pages exist
results.hasNextPage(); // → boolean

// Fetch the next page
const nextResults = await results.nextPage(); // → PaginatedResult

// Jump to a specific page (1-indexed)
const page5 = await results.getPage(5); // → PaginatedResult

// Export the full result set to CSV and get the download URL
const csvUrl = await results.exportCsv(); // → Promise<string> (S3 download URL)
```

### Full Iteration Example

**Python:**
```python
results = client.twitter.search_posts("AI agents", response_type="paging")
all_posts = list(results.data)

while results.has_next_page():
    results = results.next_page()
    all_posts.extend(results.data)

print(f"Collected {len(all_posts)} posts across all pages")
```

**TypeScript:**
```typescript
let results = await client.twitter.searchPosts("AI agents", {
  responseType: "paging",
});
const allPosts = [...results.data];

while (results.hasNextPage()) {
  results = await results.nextPage();
  allPosts.push(...results.data);
}

console.log(`Collected ${allPosts.length} posts across all pages`);
```

---

## Field Naming Convention

Python SDK uses **snake_case** for all field names. TypeScript SDK and MCP use **camelCase**.

| MCP / TypeScript | Python |
|-----------------|--------|
| `likeCount` | `like_count` |
| `authorUsername` | `author_username` |
| `createdAt` | `created_at` |
| `followersCount` | `followers_count` |
| `retweetCount` | `retweet_count` |
| `commentCount` | `comment_count` |
| `profileImageUrl` | `profile_image_url` |
| `mediaUrls` | `media_urls` |
| `isRetweet` | `is_retweet` |
| `upvoteRatio` | `upvote_ratio` |

This applies to both the `fields` parameter (input) and the returned data (output).

**Python:**
```python
results = client.twitter.search_posts(
    "AI",
    fields=["id", "text", "like_count", "author_username"]
)
for post in results.data:
    print(post["like_count"])
```

**TypeScript:**
```typescript
const results = await client.twitter.searchPosts("AI", {
  fields: ["id", "text", "likeCount", "authorUsername"],
});
for (const post of results.data) {
  console.log(post.likeCount);
}
```

---

## Complete Method Reference

### Twitter (13 methods)

| Python (snake_case) | TypeScript (camelCase) | Description |
|---------------------|----------------------|-------------|
| `client.twitter.get_user(identifier)` | `client.twitter.getUser(identifier)` | Get a single user by ID or username |
| `client.twitter.get_users(identifiers)` | `client.twitter.getUsers(identifiers)` | Get 1-100 users by IDs or usernames |
| `client.twitter.search_users(query)` | `client.twitter.searchUsers(query)` | Fuzzy search users by name |
| `client.twitter.get_user_connections(identifier)` | `client.twitter.getUserConnections(identifier)` | Get followers or following list |
| `client.twitter.get_users_by_keywords(query)` | `client.twitter.getUsersByKeywords(query)` | Find users who posted about a topic |
| `client.twitter.get_posts_by_ids(ids)` | `client.twitter.getPostsByIds(ids)` | Get 1-100 posts by ID |
| `client.twitter.get_posts_by_author(identifier)` | `client.twitter.getPostsByAuthor(identifier)` | Get all posts from a username |
| `client.twitter.search_posts(query)` | `client.twitter.searchPosts(query)` | Search posts by keywords |
| `client.twitter.get_retweets(post_id)` | `client.twitter.getRetweets(postId)` | Get retweets of a post |
| `client.twitter.get_quotes(post_id)` | `client.twitter.getQuotes(postId)` | Get quote tweets of a post |
| `client.twitter.get_comments(post_id)` | `client.twitter.getComments(postId)` | Get replies to a post |
| `client.twitter.get_post_interacting_users(post_id)` | `client.twitter.getPostInteractingUsers(postId)` | Get commenters, quoters, or retweeters |
| `client.twitter.count_posts(query)` | `client.twitter.countPosts(query)` | Count tweets matching a phrase |

### Instagram (9 methods)

| Python (snake_case) | TypeScript (camelCase) | Description |
|---------------------|----------------------|-------------|
| `client.instagram.get_user(identifier)` | `client.instagram.getUser(identifier)` | Get a user by ID or username |
| `client.instagram.search_users(query)` | `client.instagram.searchUsers(query)` | Fuzzy search users by name |
| `client.instagram.get_user_connections(identifier)` | `client.instagram.getUserConnections(identifier)` | Get followers or following list |
| `client.instagram.get_users_by_keywords(query)` | `client.instagram.getUsersByKeywords(query)` | Find users who posted about a topic |
| `client.instagram.get_post_interacting_users(post_id)` | `client.instagram.getPostInteractingUsers(postId)` | Get commenters or likers of a post |
| `client.instagram.get_posts_by_ids(ids)` | `client.instagram.getPostsByIds(ids)` | Get posts by strong_id |
| `client.instagram.get_posts_by_user(identifier)` | `client.instagram.getPostsByUser(identifier)` | Get posts from a user |
| `client.instagram.search_posts(query)` | `client.instagram.searchPosts(query)` | Search posts by keywords in captions |
| `client.instagram.get_comments(post_id)` | `client.instagram.getComments(postId)` | Get comments on a post |

### Reddit (9 methods)

| Python (snake_case) | TypeScript (camelCase) | Description |
|---------------------|----------------------|-------------|
| `client.reddit.get_user(identifier)` | `client.reddit.getUser(identifier)` | Get a user by username |
| `client.reddit.search_users(query)` | `client.reddit.searchUsers(query)` | Fuzzy search users by name |
| `client.reddit.get_users_by_keywords(query)` | `client.reddit.getUsersByKeywords(query)` | Find users who posted about a topic |
| `client.reddit.search_posts(query)` | `client.reddit.searchPosts(query)` | Search posts by keywords |
| `client.reddit.get_post_with_comments(post_id)` | `client.reddit.getPostWithComments(postId)` | Get a post with all its comments |
| `client.reddit.search_comments(query)` | `client.reddit.searchComments(query)` | Search comments by keywords |
| `client.reddit.search_subreddits(query)` | `client.reddit.searchSubreddits(query)` | Search subreddits by name |
| `client.reddit.get_subreddit_with_posts(name)` | `client.reddit.getSubredditWithPosts(name)` | Get subreddit details with posts |
| `client.reddit.get_subreddits_by_keywords(query)` | `client.reddit.getSubredditsByKeywords(query)` | Search subreddits by keyword in description |

### TikTok (9 methods)

| Python (snake_case) | TypeScript (camelCase) | Description |
|---------------------|----------------------|-------------|
| `client.tiktok.get_user(identifier)` | `client.tiktok.getUser(identifier)` | Get a user by ID or username |
| `client.tiktok.search_users(query)` | `client.tiktok.searchUsers(query)` | Fuzzy search users by name |
| `client.tiktok.get_users_by_keywords(query)` | `client.tiktok.getUsersByKeywords(query)` | Find users who posted about a topic |
| `client.tiktok.get_users_by_hashtags(hashtags)` | `client.tiktok.getUsersByHashtags(hashtags)` | Find users who used specific hashtags |
| `client.tiktok.get_posts_by_ids(ids)` | `client.tiktok.getPostsByIds(ids)` | Get posts by ID |
| `client.tiktok.get_posts_by_user(identifier)` | `client.tiktok.getPostsByUser(identifier)` | Get posts from a user |
| `client.tiktok.search_posts(query)` | `client.tiktok.searchPosts(query)` | Search posts by keywords |
| `client.tiktok.get_posts_by_hashtags(hashtags)` | `client.tiktok.getPostsByHashtags(hashtags)` | Search posts by hashtags |
| `client.tiktok.get_comments(post_id)` | `client.tiktok.getComments(postId)` | Get comments on a post |

### Tracking (3 methods)

| Python (snake_case) | TypeScript (camelCase) | Description |
|---------------------|----------------------|-------------|
| `client.tracking.get_tracked_items()` | `client.tracking.getTrackedItems()` | List all tracked items |
| `client.tracking.add_tracked_items(items)` | `client.tracking.addTrackedItems(items)` | Add items to tracking |
| `client.tracking.remove_tracked_items(items)` | `client.tracking.removeTrackedItems(items)` | Remove items from tracking |