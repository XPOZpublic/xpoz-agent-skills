# TikTok Tools

## Table of Contents

- [getTiktokUser](#gettiktokuser) -- Get user by ID or username
- [searchTiktokUsers](#searchtiktokusers) -- Fuzzy search users by name
- [getTiktokUsersByKeywords](#gettiktokuserbykeywords) -- Find users who posted about a topic
- [getTiktokUsersByHashtags](#gettiktokuserbyhashtags) -- Find users by hashtags (UNIQUE to TikTok)
- [getTiktokPostsByIds](#gettiktokpostsbyids) -- Get posts by ID
- [getTiktokPostsByUser](#gettiktokpostsbyuser) -- Get posts from a user
- [getTiktokPostsByKeywords](#gettiktokpostsbykeywords) -- Search posts by keywords
- [getTiktokPostsByHashtags](#gettiktokpostsbyhashtags) -- Search posts by hashtags (UNIQUE to TikTok)
- [getTiktokCommentsByPostId](#gettiktokcommentsbypostid) -- Get comments on a post

---

## User Fields

Available on all user tools via the `fields` parameter.

| Field | Description |
|-------|-------------|
| `id` | Numeric user ID |
| `username` | Unique handle |
| `nickname` | Display name |
| `signature` | Bio / description |
| `isPrivate` | Whether the account is private |
| `isVerified` | Whether the account is verified |
| `followerCount` | Number of followers |
| `followingCount` | Number of accounts followed |
| `likeCount` | Total likes received across posts |
| `postCount` | Number of posts |
| `avatar` | Profile picture URL |

Default user fields: `["id", "username", "nickname"]`

## Post Fields

Available on all post tools via the `fields` parameter.

| Field | Category | Description |
|-------|----------|-------------|
| `id` | Core | Post ID |
| `description` | Core | Post caption / text |
| `userId` | Core | Author user ID |
| `username` | Core | Author username |
| `nickname` | Core | Author display name |
| `createdAtDate` | Core | Post date (YYYY-MM-DD) |
| `likeCount` | Engagement | Likes |
| `commentCount` | Engagement | Comments |
| `playCount` | Engagement | Video views |
| `forwardCount` | Engagement | Shares / forwards |
| `collectCount` | Engagement | Bookmarks / saves |
| `downloadCount` | Engagement | Downloads |
| `videoThumbnail` | Media | Thumbnail image URL |
| `videoUrl` | Media | Video URL |
| `duration` | Media | Video length |
| `postType` | Media | Type of post |
| `hashtags` | Content | Hashtags on the post |

Default post fields: `["id", "description", "username", "createdAtDate"]`

## Comment Fields

Available on `getTiktokCommentsByPostId` via the `fields` parameter.

| Field | Description |
|-------|-------------|
| `id` | Comment ID |
| `text` | Comment text |
| `postId` | Parent post ID |
| `userId` | Commenter user ID |
| `username` | Commenter username |
| `likeCount` | Likes on the comment |
| `createdAt` | Full timestamp |
| `createdAtTimestamp` | Unix timestamp |
| `createdAtDate` | Date (YYYY-MM-DD) |

Default comment fields: `["id", "text", "username", "createdAtDate"]`

## Aggregate Fields (Users by Keywords / Hashtags)

These fields are available on `getTiktokUsersByKeywords` and `getTiktokUsersByHashtags` and must be explicitly requested in the `fields` array.

| Field | Description |
|-------|-------------|
| `aggRelevance` | Relevance score based on matching posts |
| `relevantPostsCount` | Number of matching posts by this user |
| `relevantPostsLikesSum` | Total likes on matching posts |
| `relevantPostsCommentsSum` | Total comments on matching posts |
| `relevantPostsPlaysSum` | Total plays on matching posts |
| `relevantPostsForwardsSum` | Total forwards on matching posts |

---

## getTiktokUser

Get a TikTok user profile by ID or username.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `identifier` | string | Yes | -- | User ID or username |
| `identifierType` | `"id"` \| `"username"` | Yes | -- | Whether `identifier` is a numeric ID or username |
| `fields` | string[] | No | `["id", "username", "nickname"]` | Fields to return (see User Fields above) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |

### When to use

- You have an exact username or user ID
- You want a single user profile

For fuzzy/name-based search, use `searchTiktokUsers` instead.

### MCP

```json
{
  "tool": "getTiktokUser",
  "arguments": {
    "identifier": "charlidamelio",
    "identifierType": "username",
    "fields": ["id", "username", "nickname", "followerCount", "isVerified"]
  }
}
```

### Python SDK

```python
user = client.tiktok.get_user(
    "charlidamelio",
    identifier_type="username",
    fields=["id", "username", "nickname", "follower_count", "is_verified"]
)
```

### TypeScript SDK

```typescript
const user = await client.tiktok.getUser("charlidamelio", {
  identifierType: "username",
  fields: ["id", "username", "nickname", "followerCount", "isVerified"],
});
```

### CLI

```bash
xpoz-cli tiktok get_user charlidamelio \
  --identifier-type username \
  --fields id username nickname follower_count is_verified
```

---

## searchTiktokUsers

Fuzzy search TikTok users by name or username via external API.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | string | Yes | -- | Name or username to search for |
| `limit` | number | No | 10 | Max results (max 10) |
| `fields` | string[] | No | `["id", "username", "nickname"]` | Fields to return |

### When to use

- You have a display name, partial name, or approximate username
- You want to discover multiple candidate users

For exact username lookup, use `getTiktokUser` instead.

### MCP

```json
{
  "tool": "searchTiktokUsers",
  "arguments": {
    "name": "Charli D'Amelio",
    "limit": 5,
    "fields": ["id", "username", "nickname", "followerCount"]
  }
}
```

### Python SDK

```python
users = client.tiktok.search_users(
    "Charli D'Amelio",
    limit=5,
    fields=["id", "username", "nickname", "follower_count"]
)
```

### TypeScript SDK

```typescript
const users = await client.tiktok.searchUsers("Charli D'Amelio", {
  limit: 5,
  fields: ["id", "username", "nickname", "followerCount"],
});
```

### CLI

```bash
xpoz-cli tiktok search_users "Charli D'Amelio" \
  --limit 5 \
  --fields id username nickname follower_count
```

---

## getTiktokUsersByKeywords

Find TikTok users who authored posts matching keywords. Returns deduplicated user profiles.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | Yes | -- | Keyword query (supports boolean syntax) |
| `fields` | string[] | No | `["id", "username", "nickname"]` | Fields to return (user fields + aggregate fields) |
| `startDate` | string | No | -- | Start date (YYYY-MM-DD) |
| `endDate` | string | No | -- | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | `"fast"` | Response mode |
| `limit` | number | No | -- | Max results (fast mode) |
| `pageNumber` | number | No | -- | Page to fetch (paging mode, 1-indexed) |
| `pageNumberEnd` | number | No | -- | Last page to fetch (bulk paging) |
| `tableName` | string | No | -- | Cached table from previous page request |

### MCP

```json
{
  "tool": "getTiktokUsersByKeywords",
  "arguments": {
    "query": "skincare routine",
    "fields": ["id", "username", "nickname", "followerCount", "relevantPostsCount", "relevantPostsPlaysSum"],
    "startDate": "2025-01-01"
  }
}
```

### Python SDK

```python
users = client.tiktok.get_users_by_keywords(
    "skincare routine",
    fields=["id", "username", "nickname", "follower_count", "relevant_posts_count", "relevant_posts_plays_sum"],
    start_date="2025-01-01"
)
```

### TypeScript SDK

```typescript
const users = await client.tiktok.getUsersByKeywords("skincare routine", {
  fields: ["id", "username", "nickname", "followerCount", "relevantPostsCount", "relevantPostsPlaysSum"],
  startDate: "2025-01-01",
});
```

### CLI

```bash
xpoz-cli tiktok get_users_by_keywords "skincare routine" \
  --fields id username nickname follower_count relevant_posts_count relevant_posts_plays_sum \
  --start-date 2025-01-01
```

---

## getTiktokUsersByHashtags

> **UNIQUE TO TIKTOK** -- This tool has no equivalent on other platforms.

Find TikTok users who authored posts tagged with specific hashtags. Returns deduplicated user profiles.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `hashtags` | string[] | Yes | -- | 1-5 bare alphanumeric tags (no `#` prefix) |
| `fields` | string[] | No | `["id", "username", "nickname"]` | Fields to return (user fields + aggregate fields) |
| `startDate` | string | No | -- | Start date (YYYY-MM-DD) |
| `endDate` | string | No | -- | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | `"fast"` | Response mode |
| `limit` | number | No | -- | Max results (fast mode) |
| `pageNumber` | number | No | -- | Page to fetch (paging mode, 1-indexed) |
| `pageNumberEnd` | number | No | -- | Last page to fetch (bulk paging) |
| `tableName` | string | No | -- | Cached table from previous page request |

### Hashtag rules

- Pass bare strings: `["fyp", "skincare"]`, not `["#fyp", "#skincare"]`
- Alphanumeric and underscores only
- 1-5 hashtags per request
- OR semantics: matches users who posted with ANY of the listed hashtags

### When to use

- You want to find creators who used specific TikTok hashtags
- You are doing hashtag-based influencer discovery
- You want to see who is participating in a hashtag trend

For keyword/phrase search in post descriptions, use `getTiktokUsersByKeywords` instead.

### MCP

```json
{
  "tool": "getTiktokUsersByHashtags",
  "arguments": {
    "hashtags": ["fyp", "skincare", "beautytok"],
    "fields": ["id", "username", "nickname", "followerCount", "relevantPostsCount"],
    "startDate": "2025-01-01"
  }
}
```

### Python SDK

```python
users = client.tiktok.get_users_by_hashtags(
    ["fyp", "skincare", "beautytok"],
    fields=["id", "username", "nickname", "follower_count", "relevant_posts_count"],
    start_date="2025-01-01"
)
```

### TypeScript SDK

```typescript
const users = await client.tiktok.getUsersByHashtags(["fyp", "skincare", "beautytok"], {
  fields: ["id", "username", "nickname", "followerCount", "relevantPostsCount"],
  startDate: "2025-01-01",
});
```

### CLI

```bash
xpoz-cli tiktok get_users_by_hashtags \
  --hashtags fyp skincare beautytok \
  --fields id username nickname follower_count relevant_posts_count \
  --start-date 2025-01-01
```

---

## getTiktokPostsByIds

Get TikTok posts by their IDs (1-100 per request). Searches the database first, then falls back to the external API for missing or stale data (>3 days).

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `postIds` | string[] | Yes | -- | 1-100 post IDs |
| `fields` | string[] | No | `["id", "description", "username", "createdAtDate"]` | Fields to return (see Post Fields above) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |

### MCP

```json
{
  "tool": "getTiktokPostsByIds",
  "arguments": {
    "postIds": ["7234567890123456789", "7234567890123456790"],
    "fields": ["id", "description", "username", "likeCount", "playCount"]
  }
}
```

### Python SDK

```python
posts = client.tiktok.get_posts_by_ids(
    ["7234567890123456789", "7234567890123456790"],
    fields=["id", "description", "username", "like_count", "play_count"]
)
```

### TypeScript SDK

```typescript
const posts = await client.tiktok.getPostsByIds(
  ["7234567890123456789", "7234567890123456790"],
  { fields: ["id", "description", "username", "likeCount", "playCount"] }
);
```

### CLI

```bash
xpoz-cli tiktok get_posts_by_ids \
  --post-ids 7234567890123456789 7234567890123456790 \
  --fields id description username like_count play_count
```

---

## getTiktokPostsByUser

Get posts from a TikTok user by ID or username. Searches the database first, then falls back to the external API for stale or missing data.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `identifier` | string | Yes | -- | User ID or username |
| `identifierType` | `"id"` \| `"username"` | Yes | -- | Whether `identifier` is a numeric ID or username |
| `fields` | string[] | No | `["id", "description", "username", "createdAtDate"]` | Fields to return |
| `startDate` | string | No | -- | Start date (YYYY-MM-DD) |
| `endDate` | string | No | -- | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | `"fast"` | Response mode |
| `limit` | number | No | -- | Max results (fast mode) |
| `pageNumber` | number | No | -- | Page to fetch (paging mode, 1-indexed) |
| `pageNumberEnd` | number | No | -- | Last page to fetch (bulk paging) |
| `tableName` | string | No | -- | Cached table from previous page request |

### MCP

```json
{
  "tool": "getTiktokPostsByUser",
  "arguments": {
    "identifier": "charlidamelio",
    "identifierType": "username",
    "fields": ["id", "description", "likeCount", "playCount", "createdAtDate", "hashtags"],
    "startDate": "2025-01-01"
  }
}
```

### Python SDK

```python
posts = client.tiktok.get_posts_by_user(
    "charlidamelio",
    identifier_type="username",
    fields=["id", "description", "like_count", "play_count", "created_at_date", "hashtags"],
    start_date="2025-01-01"
)
```

### TypeScript SDK

```typescript
const posts = await client.tiktok.getPostsByUser("charlidamelio", {
  identifierType: "username",
  fields: ["id", "description", "likeCount", "playCount", "createdAtDate", "hashtags"],
  startDate: "2025-01-01",
});
```

### CLI

```bash
xpoz-cli tiktok get_posts_by_user charlidamelio \
  --identifier-type username \
  --fields id description like_count play_count created_at_date hashtags \
  --start-date 2025-01-01
```

---

## getTiktokPostsByKeywords

Search TikTok posts by keywords in post descriptions. Supports boolean query syntax.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | Yes | -- | Keyword query (supports boolean syntax) |
| `fields` | string[] | No | `["id", "description", "username", "createdAtDate"]` | Fields to return |
| `startDate` | string | No | -- | Start date (YYYY-MM-DD) |
| `endDate` | string | No | -- | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | `"fast"` | Response mode |
| `limit` | number | No | -- | Max results (fast mode) |
| `pageNumber` | number | No | -- | Page to fetch (paging mode, 1-indexed) |
| `pageNumberEnd` | number | No | -- | Last page to fetch (bulk paging) |
| `tableName` | string | No | -- | Cached table from previous page request |

### MCP

```json
{
  "tool": "getTiktokPostsByKeywords",
  "arguments": {
    "query": "\"AI\" AND \"productivity\"",
    "fields": ["id", "description", "username", "likeCount", "playCount", "createdAtDate"],
    "startDate": "2025-06-01",
    "endDate": "2025-06-10"
  }
}
```

### Python SDK

```python
posts = client.tiktok.search_posts(
    '"AI" AND "productivity"',
    fields=["id", "description", "username", "like_count", "play_count", "created_at_date"],
    start_date="2025-06-01",
    end_date="2025-06-10"
)
```

### TypeScript SDK

```typescript
const posts = await client.tiktok.searchPosts('"AI" AND "productivity"', {
  fields: ["id", "description", "username", "likeCount", "playCount", "createdAtDate"],
  startDate: "2025-06-01",
  endDate: "2025-06-10",
});
```

### CLI

```bash
xpoz-cli tiktok search_posts '"AI" AND "productivity"' \
  --fields id description username like_count play_count created_at_date \
  --start-date 2025-06-01 \
  --end-date 2025-06-10
```

---

## getTiktokPostsByHashtags

> **UNIQUE TO TIKTOK** -- This tool has no equivalent on other platforms.

Search TikTok posts by hashtags. Searches the indexed `hashtags` column directly, not post descriptions.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `hashtags` | string[] | Yes | -- | 1-5 bare alphanumeric tags (no `#` prefix) |
| `fields` | string[] | No | `["id", "description", "username", "createdAtDate"]` | Fields to return |
| `startDate` | string | No | -- | Start date (YYYY-MM-DD) |
| `endDate` | string | No | -- | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | `"fast"` | Response mode |
| `limit` | number | No | -- | Max results (fast mode) |
| `pageNumber` | number | No | -- | Page to fetch (paging mode, 1-indexed) |
| `pageNumberEnd` | number | No | -- | Last page to fetch (bulk paging) |
| `tableName` | string | No | -- | Cached table from previous page request |

### Hashtag rules

- Pass bare strings: `["fyp", "cooking"]`, not `["#fyp", "#cooking"]`
- Alphanumeric and underscores only
- 1-5 hashtags per request
- OR semantics: matches posts tagged with ANY of the listed hashtags

### When to use

- You want posts tagged with specific TikTok hashtags
- You are tracking hashtag trends or challenges
- You want to analyze content within a hashtag

For keyword/phrase search in post descriptions, use `getTiktokPostsByKeywords` instead.

### MCP

```json
{
  "tool": "getTiktokPostsByHashtags",
  "arguments": {
    "hashtags": ["booktok", "reading"],
    "fields": ["id", "description", "username", "likeCount", "playCount", "createdAtDate", "hashtags"],
    "startDate": "2025-01-01"
  }
}
```

### Python SDK

```python
posts = client.tiktok.get_posts_by_hashtags(
    ["booktok", "reading"],
    fields=["id", "description", "username", "like_count", "play_count", "created_at_date", "hashtags"],
    start_date="2025-01-01"
)
```

### TypeScript SDK

```typescript
const posts = await client.tiktok.getPostsByHashtags(["booktok", "reading"], {
  fields: ["id", "description", "username", "likeCount", "playCount", "createdAtDate", "hashtags"],
  startDate: "2025-01-01",
});
```

### CLI

```bash
xpoz-cli tiktok get_posts_by_hashtags \
  --hashtags booktok reading \
  --fields id description username like_count play_count created_at_date hashtags \
  --start-date 2025-01-01
```

---

## getTiktokCommentsByPostId

Get comments on a TikTok post. Searches the database first, then falls back to the external API for stale data (>1 week).

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `postId` | string | Yes | -- | TikTok post ID |
| `fields` | string[] | No | `["id", "text", "username", "createdAtDate"]` | Fields to return (see Comment Fields above) |
| `startDate` | string | No | -- | Start date (YYYY-MM-DD) |
| `endDate` | string | No | -- | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | `"fast"` | Response mode |
| `limit` | number | No | -- | Max results (fast mode) |
| `pageNumber` | number | No | -- | Page to fetch (paging mode, 1-indexed) |
| `pageNumberEnd` | number | No | -- | Last page to fetch (bulk paging) |
| `tableName` | string | No | -- | Cached table from previous page request |

### MCP

```json
{
  "tool": "getTiktokCommentsByPostId",
  "arguments": {
    "postId": "7234567890123456789",
    "fields": ["id", "text", "username", "createdAtDate", "likeCount"]
  }
}
```

### Python SDK

```python
comments = client.tiktok.get_comments(
    "7234567890123456789",
    fields=["id", "text", "username", "created_at_date", "like_count"]
)
```

### TypeScript SDK

```typescript
const comments = await client.tiktok.getComments("7234567890123456789", {
  fields: ["id", "text", "username", "createdAtDate", "likeCount"],
});
```

### CLI

```bash
xpoz-cli tiktok get_comments \
  --post-id 7234567890123456789 \
  --fields id text username created_at_date like_count
```

---

## Data Freshness

| Data Type | Cache Threshold | Behavior |
|-----------|----------------|----------|
| Posts (by ID) | >3 days | DB first, API fallback if stale or missing |
| Posts (by user) | >1 week | DB first, API fallback if stale |
| Comments | >1 week | DB first, API fallback if stale |

Use `forceLatest: true` to bypass the cache and always fetch from the external API (increases latency and cost).

## Response Modes

All paginated tools (`getTiktokUsersByKeywords`, `getTiktokUsersByHashtags`, `getTiktokPostsByUser`, `getTiktokPostsByKeywords`, `getTiktokPostsByHashtags`, `getTiktokCommentsByPostId`) support three response modes:

| Mode | Behavior | Best For |
|------|----------|----------|
| `"fast"` (default) | Returns up to 300 results immediately | Quick lookups, exploration |
| `"paging"` | Async, returns `operationId` -- poll with `checkOperationStatus` | Large datasets, page-by-page (100/page) |
| `"csv"` | Async CSV export to S3 -- returns download URL | Bulk export, offline analysis |

### Paging workflow

1. First call: omit `pageNumber` and `tableName`. Returns page 1 with `tableName`, `totalPages`, `totalRows`.
2. Subsequent pages: pass `tableName` from step 1 with `pageNumber` (2, 3, ...).
3. Bulk fetch: pass `pageNumber` + `pageNumberEnd` + `tableName` to get multiple consecutive pages.
