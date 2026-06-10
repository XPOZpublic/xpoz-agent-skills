# Reddit Tools Reference

All 9 Reddit tools with parameters, available fields, and usage examples across MCP, Python SDK, TypeScript SDK, and CLI.

## Table of Contents

- [getRedditUser](#getreddituser)
- [searchRedditUsers](#searchredditusers)
- [getRedditUsersByKeywords](#getreddituserbykeywords)
- [getRedditPostsByKeywords](#getredditpostsbykeywords)
- [getRedditPostWithCommentsById](#getredditpostwithcommentsbyid)
- [getRedditCommentsByKeywords](#getredditcommentsbykeywords)
- [searchRedditSubreddits](#searchredditsubreddits)
- [getRedditSubredditWithPostsByName](#getredditsubredditwithpostsbyname)
- [getRedditSubredditsByKeywords](#getredditsubredditsbykeywords)

---

## getRedditUser

Get a Reddit user by username.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `username` | string | Yes | — | Reddit username (no `u/` prefix) |
| `fields` | string[] | No | `["id", "username", "totalKarma"]` | Fields to return |

### Available Fields

`id`, `username`, `profileUrl`, `profilePicUrl`, `linkKarma`, `commentKarma`, `totalKarma`, `awardeeKarma`, `awarderKarma`, `isGold`, `isMod`, `isEmployee`, `hasVerifiedEmail`, `isSuspended`, `verified`, `profileDescription`, `createdAt`

### MCP

```
Call getRedditUser:
  username: "spez"
  fields: ["id", "username", "totalKarma", "linkKarma", "commentKarma", "profileDescription", "createdAt"]
```

### Python SDK

```python
user = client.reddit.get_user(
    "spez",
    fields=["id", "username", "total_karma", "link_karma", "comment_karma", "profile_description", "created_at"]
)
```

### TypeScript SDK

```typescript
const user = await client.reddit.getUser("spez", {
  fields: ["id", "username", "totalKarma", "linkKarma", "commentKarma", "profileDescription", "createdAt"],
});
```

### CLI

```bash
xpoz-cli reddit get_user --username spez --fields id username total_karma link_karma comment_karma profile_description created_at
```

---

## searchRedditUsers

Fuzzy search Reddit users by name.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | string | Yes | — | Name to search for |
| `limit` | number | No | 50 | Max results (max 50) |
| `fields` | string[] | No | `["id", "username", "totalKarma"]` | Fields to return |

### Available Fields

Same as [getRedditUser](#getreddituser).

### MCP

```
Call searchRedditUsers:
  name: "programming"
  limit: 20
  fields: ["id", "username", "totalKarma", "profileDescription"]
```

### Python SDK

```python
users = client.reddit.search_users(
    "programming",
    limit=20,
    fields=["id", "username", "total_karma", "profile_description"]
)
```

### TypeScript SDK

```typescript
const users = await client.reddit.searchUsers("programming", {
  limit: 20,
  fields: ["id", "username", "totalKarma", "profileDescription"],
});
```

### CLI

```bash
xpoz-cli reddit search_users --name programming --limit 20 --fields id username total_karma profile_description
```

---

## getRedditUsersByKeywords

Find Reddit users who posted about a topic.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | Yes | — | Search query (supports boolean syntax) |
| `fields` | string[] | No | — | Fields to return (user fields + aggregate fields) |
| `startDate` | string | No | — | Start date (YYYY-MM-DD) |
| `endDate` | string | No | — | End date (YYYY-MM-DD) |
| `subreddit` | string | No | — | Filter to a subreddit (no `r/` prefix) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | string | No | `"fast"` | `"fast"`, `"paging"`, or `"csv"` |
| `limit` | number | No | — | Max results |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Available Fields

User fields from [getRedditUser](#getreddituser), plus aggregate fields:

| Aggregate Field | Description |
|----------------|-------------|
| `aggRelevance` | Relevance score for the query |
| `relevantPostsCount` | Number of posts matching the query |
| `relevantPostsUpvotesSum` | Total upvotes across matching posts |
| `relevantPostsCommentsCountSum` | Total comments across matching posts |

### MCP

```
Call getRedditUsersByKeywords:
  query: "rust programming"
  fields: ["id", "username", "totalKarma", "relevantPostsCount", "aggRelevance"]
  startDate: "2026-01-01"
  endDate: "2026-06-10"
  subreddit: "rust"
```

### Python SDK

```python
results = client.reddit.get_users_by_keywords(
    "rust programming",
    fields=["id", "username", "total_karma", "relevant_posts_count", "agg_relevance"],
    start_date="2026-01-01",
    end_date="2026-06-10",
    subreddit="rust"
)
```

### TypeScript SDK

```typescript
const results = await client.reddit.getUsersByKeywords("rust programming", {
  fields: ["id", "username", "totalKarma", "relevantPostsCount", "aggRelevance"],
  startDate: "2026-01-01",
  endDate: "2026-06-10",
  subreddit: "rust",
});
```

### CLI

```bash
xpoz-cli reddit get_users_by_keywords --query "rust programming" --fields id username total_karma relevant_posts_count agg_relevance --start-date 2026-01-01 --end-date 2026-06-10 --subreddit rust
```

---

## getRedditPostsByKeywords

Search Reddit posts by keywords (searches titles and self-text).

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | Yes | — | Search query (supports boolean syntax) |
| `fields` | string[] | No | `["id", "title", "authorUsername", "subredditName", "createdAtDate"]` | Fields to return |
| `startDate` | string | No | — | Start date (YYYY-MM-DD) |
| `endDate` | string | No | — | End date (YYYY-MM-DD) |
| `sort` | string | No | — | `"relevance"`, `"hot"`, `"top"`, `"new"`, or `"comments"` |
| `time` | string | No | — | `"hour"`, `"day"`, `"week"`, `"month"`, `"year"`, or `"all"` |
| `subreddit` | string | No | — | Filter to a subreddit (no `r/` prefix) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | string | No | `"fast"` | `"fast"`, `"paging"`, or `"csv"` |
| `limit` | number | No | — | Max results |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Available Fields

`id`, `title`, `selftext`, `url`, `permalink`, `authorId`, `authorUsername`, `subredditName`, `subredditId`, `score`, `upvotes`, `downvotes`, `upvoteRatio`, `commentsCount`, `crosspostsCount`, `isSelf`, `isVideo`, `over18`, `spoiler`, `locked`, `stickied`, `archived`, `createdAtDate`

### MCP

```
Call getRedditPostsByKeywords:
  query: "\"artificial intelligence\" AND ethics"
  fields: ["id", "title", "selftext", "authorUsername", "subredditName", "score", "commentsCount", "createdAtDate"]
  startDate: "2026-05-01"
  endDate: "2026-06-10"
  sort: "top"
  time: "month"
```

### Python SDK

```python
results = client.reddit.search_posts(
    '"artificial intelligence" AND ethics',
    fields=["id", "title", "selftext", "author_username", "subreddit_name", "score", "comments_count", "created_at_date"],
    start_date="2026-05-01",
    end_date="2026-06-10",
    sort="top",
    time="month"
)
```

### TypeScript SDK

```typescript
const results = await client.reddit.searchPosts('"artificial intelligence" AND ethics', {
  fields: ["id", "title", "selftext", "authorUsername", "subredditName", "score", "commentsCount", "createdAtDate"],
  startDate: "2026-05-01",
  endDate: "2026-06-10",
  sort: "top",
  time: "month",
});
```

### CLI

```bash
xpoz-cli reddit search_posts --query "\"artificial intelligence\" AND ethics" --fields id title selftext author_username subreddit_name score comments_count created_at_date --start-date 2026-05-01 --end-date 2026-06-10 --sort top --time month
```

---

## getRedditPostWithCommentsById

Get a Reddit post with all its comments.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `postId` | string | Yes | — | Reddit post ID |
| `postFields` | string[] | No | — | Fields to return for the post |
| `commentFields` | string[] | No | — | Fields to return for comments |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | string | No | `"fast"` | `"fast"` or `"paging"` |
| `limit` | number | No | — | Max comments to return |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Response Modes

| Mode | Behavior |
|------|----------|
| `"fast"` | Returns up to 300 comments immediately |
| `"paging"` | Async, 100 comments per page — poll with `checkOperationStatus` |

### Available Post Fields

Same as [getRedditPostsByKeywords](#getredditpostsbykeywords).

### Available Comment Fields

`id`, `body`, `authorId`, `authorUsername`, `score`, `upvotes`, `parentId`, `depth`, `isSubmitter`, `stickied`, `createdAtDate`

### MCP

```
Call getRedditPostWithCommentsById:
  postId: "1abc2de"
  postFields: ["id", "title", "selftext", "authorUsername", "score", "commentsCount"]
  commentFields: ["id", "body", "authorUsername", "score", "depth", "createdAtDate"]
```

### Python SDK

```python
result = client.reddit.get_post_with_comments(
    "1abc2de",
    post_fields=["id", "title", "selftext", "author_username", "score", "comments_count"],
    comment_fields=["id", "body", "author_username", "score", "depth", "created_at_date"]
)
```

### TypeScript SDK

```typescript
const result = await client.reddit.getPostWithComments("1abc2de", {
  postFields: ["id", "title", "selftext", "authorUsername", "score", "commentsCount"],
  commentFields: ["id", "body", "authorUsername", "score", "depth", "createdAtDate"],
});
```

### CLI

```bash
xpoz-cli reddit get_post_with_comments --post-id 1abc2de --post-fields id title selftext author_username score comments_count --comment-fields id body author_username score depth created_at_date
```

---

## getRedditCommentsByKeywords

Search Reddit comments by keywords (searches comment body text).

**NOTE:** This is a database-only search with no API fallback. Results are limited to comments already indexed by Xpoz.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | Yes | — | Search query (supports boolean syntax) |
| `fields` | string[] | No | `["id", "body", "authorUsername", "createdAtDate"]` | Fields to return |
| `startDate` | string | No | — | Start date (YYYY-MM-DD) |
| `endDate` | string | No | — | End date (YYYY-MM-DD) |
| `subreddit` | string | No | — | Filter to a subreddit (no `r/` prefix) |
| `responseType` | string | No | `"fast"` | `"fast"`, `"paging"`, or `"csv"` |
| `limit` | number | No | — | Max results |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Available Fields

`id`, `body`, `authorId`, `authorUsername`, `score`, `upvotes`, `parentId`, `depth`, `isSubmitter`, `stickied`, `createdAtDate`

### MCP

```
Call getRedditCommentsByKeywords:
  query: "\"type safety\" AND (\"rust\" OR \"typescript\")"
  fields: ["id", "body", "authorUsername", "score", "createdAtDate"]
  startDate: "2026-01-01"
  subreddit: "programming"
```

### Python SDK

```python
results = client.reddit.search_comments(
    '"type safety" AND ("rust" OR "typescript")',
    fields=["id", "body", "author_username", "score", "created_at_date"],
    start_date="2026-01-01",
    subreddit="programming"
)
```

### TypeScript SDK

```typescript
const results = await client.reddit.searchComments('"type safety" AND ("rust" OR "typescript")', {
  fields: ["id", "body", "authorUsername", "score", "createdAtDate"],
  startDate: "2026-01-01",
  subreddit: "programming",
});
```

### CLI

```bash
xpoz-cli reddit search_comments --query "\"type safety\" AND (\"rust\" OR \"typescript\")" --fields id body author_username score created_at_date --start-date 2026-01-01 --subreddit programming
```

---

## searchRedditSubreddits

Search subreddits by name.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | Yes | — | Subreddit name to search for |
| `limit` | number | No | 50 | Max results (max 50) |
| `fields` | string[] | No | — | Fields to return |

### Available Fields

`id`, `displayName`, `title`, `publicDescription`, `description`, `subscribersCount`, `activeUserCount`, `subredditType`, `over18`, `lang`, `url`, `iconImg`, `createdAt`

### MCP

```
Call searchRedditSubreddits:
  query: "machine learning"
  limit: 10
  fields: ["id", "displayName", "title", "subscribersCount", "activeUserCount", "publicDescription"]
```

### Python SDK

```python
subreddits = client.reddit.search_subreddits(
    "machine learning",
    limit=10,
    fields=["id", "display_name", "title", "subscribers_count", "active_user_count", "public_description"]
)
```

### TypeScript SDK

```typescript
const subreddits = await client.reddit.searchSubreddits("machine learning", {
  limit: 10,
  fields: ["id", "displayName", "title", "subscribersCount", "activeUserCount", "publicDescription"],
});
```

### CLI

```bash
xpoz-cli reddit search_subreddits --query "machine learning" --limit 10 --fields id display_name title subscribers_count active_user_count public_description
```

---

## getRedditSubredditWithPostsByName

Get subreddit details along with its posts.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `subredditName` | string | Yes | — | Subreddit name (no `r/` prefix) |
| `subredditFields` | string[] | No | — | Fields to return for the subreddit |
| `postFields` | string[] | No | — | Fields to return for posts |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | string | No | `"fast"` | `"fast"` or `"paging"` |
| `limit` | number | No | — | Max posts to return |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Available Subreddit Fields

Same as [searchRedditSubreddits](#searchredditsubreddits).

### Available Post Fields

Same as [getRedditPostsByKeywords](#getredditpostsbykeywords).

### MCP

```
Call getRedditSubredditWithPostsByName:
  subredditName: "LocalLLaMA"
  subredditFields: ["id", "displayName", "subscribersCount", "activeUserCount", "publicDescription"]
  postFields: ["id", "title", "authorUsername", "score", "commentsCount", "createdAtDate"]
```

### Python SDK

```python
result = client.reddit.get_subreddit_with_posts(
    "LocalLLaMA",
    subreddit_fields=["id", "display_name", "subscribers_count", "active_user_count", "public_description"],
    post_fields=["id", "title", "author_username", "score", "comments_count", "created_at_date"]
)
```

### TypeScript SDK

```typescript
const result = await client.reddit.getSubredditWithPosts("LocalLLaMA", {
  subredditFields: ["id", "displayName", "subscribersCount", "activeUserCount", "publicDescription"],
  postFields: ["id", "title", "authorUsername", "score", "commentsCount", "createdAtDate"],
});
```

### CLI

```bash
xpoz-cli reddit get_subreddit_with_posts --subreddit-name LocalLLaMA --subreddit-fields id display_name subscribers_count active_user_count public_description --post-fields id title author_username score comments_count created_at_date
```

---

## getRedditSubredditsByKeywords

Search subreddits by keyword in their descriptions.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | Yes | — | Search query (supports boolean syntax) |
| `fields` | string[] | No | — | Fields to return |
| `startDate` | string | No | — | Start date (YYYY-MM-DD) |
| `endDate` | string | No | — | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | string | No | `"fast"` | `"fast"` or `"paging"` |
| `limit` | number | No | — | Max results |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Available Fields

Same as [searchRedditSubreddits](#searchredditsubreddits).

### MCP

```
Call getRedditSubredditsByKeywords:
  query: "open source AI models"
  fields: ["id", "displayName", "title", "subscribersCount", "publicDescription"]
  startDate: "2026-01-01"
  endDate: "2026-06-10"
```

### Python SDK

```python
results = client.reddit.get_subreddits_by_keywords(
    "open source AI models",
    fields=["id", "display_name", "title", "subscribers_count", "public_description"],
    start_date="2026-01-01",
    end_date="2026-06-10"
)
```

### TypeScript SDK

```typescript
const results = await client.reddit.getSubredditsByKeywords("open source AI models", {
  fields: ["id", "displayName", "title", "subscribersCount", "publicDescription"],
  startDate: "2026-01-01",
  endDate: "2026-06-10",
});
```

### CLI

```bash
xpoz-cli reddit get_subreddits_by_keywords --query "open source AI models" --fields id display_name title subscribers_count public_description --start-date 2026-01-01 --end-date 2026-06-10
```
