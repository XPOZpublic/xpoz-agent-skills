# Instagram Tools

## Table of Contents

- [getInstagramUser](#getinstagramuser)
- [searchInstagramUsers](#searchinstagramusers)
- [getInstagramUserConnections](#getinstagramuserconnections)
- [getInstagramUsersByKeywords](#getinstagramusersbykeywords)
- [getInstagramPostInteractingUsers](#getinstagrampostinteractingusers)
- [getInstagramPostsByIds](#getinstagrampostsbyids)
- [getInstagramPostsByUser](#getinstagrampostsbyuser)
- [getInstagramPostsByKeywords](#getinstagrampostsbykeywords)
- [getInstagramCommentsByPostId](#getinstagramcommentsbypostid)

---

> **strong_id format**: Several Instagram tools require post IDs in `strong_id` format: `{media_id}_{user_id}` (e.g., `"3606450040306139062_4836333238"`). A plain `media_id` will not work. Tools that require this format are marked below.

---

## getInstagramUser

Get an Instagram user profile by ID or username.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `identifier` | string | Yes | — | User ID or username |
| `identifierType` | `"id"` \| `"username"` | Yes | — | How to interpret `identifier` |
| `fields` | string[] | No | `["id", "username", "fullName"]` | Fields to return |
| `forceLatest` | boolean | No | false | Bypass cache for fresh data |

### Available Fields

`id`, `username`, `fullName`, `biography`, `isPrivate`, `isVerified`, `followerCount`, `followingCount`, `mediaCount`, `profilePicUrl`

### Examples

**MCP:**
```json
{
  "tool": "getInstagramUser",
  "arguments": {
    "identifier": "natgeo",
    "identifierType": "username",
    "fields": ["id", "username", "fullName", "followerCount", "biography"]
  }
}
```

**Python SDK:**
```python
user = client.instagram.get_user(
    "natgeo",
    identifier_type="username",
    fields=["id", "username", "full_name", "follower_count", "biography"]
)
```

**TypeScript SDK:**
```typescript
const user = await client.instagram.getUser("natgeo", {
  identifierType: "username",
  fields: ["id", "username", "fullName", "followerCount", "biography"],
});
```

**CLI:**
```bash
xpoz-cli instagram get_user natgeo --identifier-type username --fields id username full_name follower_count biography
```

---

## searchInstagramUsers

Fuzzy search Instagram users by name.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | string | Yes | — | Name to search for |
| `limit` | number | No | 10 | Max results (max 10) |
| `fields` | string[] | No | `["id", "username", "fullName"]` | Fields to return |

### Available Fields

Same as [getInstagramUser](#available-fields).

### Examples

**MCP:**
```json
{
  "tool": "searchInstagramUsers",
  "arguments": {
    "name": "National Geographic",
    "limit": 5,
    "fields": ["id", "username", "fullName", "followerCount", "isVerified"]
  }
}
```

**Python SDK:**
```python
results = client.instagram.search_users(
    "National Geographic",
    limit=5,
    fields=["id", "username", "full_name", "follower_count", "is_verified"]
)
```

**TypeScript SDK:**
```typescript
const results = await client.instagram.searchUsers("National Geographic", {
  limit: 5,
  fields: ["id", "username", "fullName", "followerCount", "isVerified"],
});
```

**CLI:**
```bash
xpoz-cli instagram search_users "National Geographic" --limit 5 --fields id username full_name follower_count is_verified
```

---

## getInstagramUserConnections

Get followers or following list for a user.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `username` | string | Yes | — | Instagram username |
| `connectionType` | `"followers"` \| `"following"` | Yes | — | Which connection list to retrieve |
| `fields` | string[] | No | `["id", "username", "fullName"]` | Fields to return |
| `forceLatest` | boolean | No | false | Bypass cache and fetch from API |
| `responseType` | `"fast"` \| `"paging"` | No | `"fast"` | Response mode |
| `limit` | number | No | — | Max results (fast: up to 300) |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Response Modes

| Mode | Behavior |
|------|----------|
| `"fast"` | Returns up to 300 results immediately |
| `"paging"` | Async, returns 100 results per page. Returns `operationId` — poll with `checkOperationStatus` |

### Available Fields

Same as [getInstagramUser](#available-fields).

### Examples

**MCP:**
```json
{
  "tool": "getInstagramUserConnections",
  "arguments": {
    "username": "natgeo",
    "connectionType": "followers",
    "fields": ["id", "username", "fullName", "followerCount"],
    "responseType": "fast",
    "limit": 100
  }
}
```

**Python SDK:**
```python
followers = client.instagram.get_user_connections(
    "natgeo",
    connection_type="followers",
    fields=["id", "username", "full_name", "follower_count"],
    force_latest=False
)
```

**TypeScript SDK:**
```typescript
const followers = await client.instagram.getUserConnections("natgeo", "followers", {
  fields: ["id", "username", "fullName", "followerCount"],
});
```

**CLI:**
```bash
xpoz-cli instagram get_user_connections natgeo --connection-type followers --fields id username full_name follower_count --response-type fast --limit 100
```

---

## getInstagramUsersByKeywords

Find users who posted about a topic. Returns users with aggregate engagement metrics for matching posts.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | Yes | — | Search query (supports boolean syntax) |
| `fields` | string[] | No | `["id", "username", "fullName"]` | Fields to return |
| `startDate` | string | No | — | Start date (YYYY-MM-DD) |
| `endDate` | string | No | — | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache and fetch from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | `"fast"` | Response mode |
| `limit` | number | No | — | Max results |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Available Fields

Standard user fields plus aggregate fields:

**User fields:** `id`, `username`, `fullName`, `biography`, `isPrivate`, `isVerified`, `followerCount`, `followingCount`, `mediaCount`, `profilePicUrl`

**Aggregate fields:** `aggRelevance`, `relevantPostsCount`, `relevantPostsLikesSum`, `relevantPostsCommentsSum`, `relevantPostsResharesSum`, `relevantPostsVideoPlaysSum`

### Examples

**MCP:**
```json
{
  "tool": "getInstagramUsersByKeywords",
  "arguments": {
    "query": "sustainable fashion",
    "fields": ["id", "username", "fullName", "followerCount", "relevantPostsCount", "relevantPostsLikesSum"],
    "startDate": "2026-01-01",
    "endDate": "2026-06-01"
  }
}
```

**Python SDK:**
```python
users = client.instagram.get_users_by_keywords(
    "sustainable fashion",
    fields=["id", "username", "full_name", "follower_count", "relevant_posts_count", "relevant_posts_likes_sum"],
    start_date="2026-01-01",
    end_date="2026-06-01"
)
```

**TypeScript SDK:**
```typescript
const users = await client.instagram.getUsersByKeywords("sustainable fashion", {
  fields: ["id", "username", "fullName", "followerCount", "relevantPostsCount", "relevantPostsLikesSum"],
  startDate: "2026-01-01",
  endDate: "2026-06-01",
});
```

**CLI:**
```bash
xpoz-cli instagram get_users_by_keywords "sustainable fashion" --fields id username full_name follower_count relevant_posts_count relevant_posts_likes_sum --start-date 2026-01-01 --end-date 2026-06-01
```

---

## getInstagramPostInteractingUsers

Get users who commented on or liked a specific post.

> **Requires strong_id format** for `postId` (e.g., `"3606450040306139062_4836333238"`).

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `postId` | string | Yes | — | Post ID in **strong_id** format (`{media_id}_{user_id}`) |
| `interactionType` | `"commenters"` \| `"likers"` | Yes | — | Which interacting users to retrieve |
| `fields` | string[] | No | `["id", "username", "fullName"]` | Fields to return |
| `forceLatest` | boolean | No | false | Bypass cache and fetch from API |
| `responseType` | `"fast"` \| `"paging"` | No | `"fast"` | Response mode |
| `limit` | number | No | — | Max results |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Available Fields

Same as [getInstagramUser](#available-fields).

### Examples

**MCP:**
```json
{
  "tool": "getInstagramPostInteractingUsers",
  "arguments": {
    "postId": "3606450040306139062_4836333238",
    "interactionType": "commenters",
    "fields": ["id", "username", "fullName", "followerCount"]
  }
}
```

**Python SDK:**
```python
commenters = client.instagram.get_post_interacting_users(
    "3606450040306139062_4836333238",
    interaction_type="commenters",
    fields=["id", "username", "full_name", "follower_count"],
    force_latest=False
)
```

**TypeScript SDK:**
```typescript
const commenters = await client.instagram.getPostInteractingUsers(
  "3606450040306139062_4836333238",
  "commenters",
  { fields: ["id", "username", "fullName", "followerCount"] }
);
```

**CLI:**
```bash
xpoz-cli instagram get_post_interacting_users "3606450040306139062_4836333238" --interaction-type commenters --fields id username full_name follower_count
```

---

## getInstagramPostsByIds

Get 1-100 Instagram posts by their IDs.

> **Requires strong_id format** for all IDs in `postIds` (e.g., `"3606450040306139062_4836333238"`). A plain `media_id` will not work.

**Data freshness:** Returns cached data from DB, with automatic API fallback if data is stale (>3 days).

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `postIds` | string[] | Yes | — | 1-100 post IDs in **strong_id** format |
| `fields` | string[] | No | `["id", "caption", "username", "createdAtDate"]` | Fields to return |
| `forceLatest` | boolean | No | false | Bypass cache and fetch from API |

### Available Fields

`id`, `caption`, `userId`, `username`, `fullName`, `createdAtDate`, `likeCount`, `commentCount`, `reshareCount`, `videoPlayCount`, `mediaType`, `imageUrl`, `videoUrl`, `subtitles`, `videoDuration`

### Examples

**MCP:**
```json
{
  "tool": "getInstagramPostsByIds",
  "arguments": {
    "postIds": [
      "3606450040306139062_4836333238",
      "3605872119044821507_25025320"
    ],
    "fields": ["id", "caption", "username", "likeCount", "commentCount", "createdAtDate"]
  }
}
```

**Python SDK:**
```python
posts = client.instagram.get_posts_by_ids(
    ["3606450040306139062_4836333238", "3605872119044821507_25025320"],
    fields=["id", "caption", "username", "like_count", "comment_count", "created_at_date"]
)
```

**TypeScript SDK:**
```typescript
const posts = await client.instagram.getPostsByIds(
  ["3606450040306139062_4836333238", "3605872119044821507_25025320"],
  { fields: ["id", "caption", "username", "likeCount", "commentCount", "createdAtDate"] }
);
```

**CLI:**
```bash
xpoz-cli instagram get_posts_by_ids --post-ids "3606450040306139062_4836333238" "3605872119044821507_25025320" --fields id caption username like_count comment_count created_at_date
```

---

## getInstagramPostsByUser

Get posts from a specific user.

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `identifier` | string | Yes | — | User ID or username |
| `identifierType` | `"id"` \| `"username"` | Yes | — | How to interpret `identifier` |
| `fields` | string[] | No | `["id", "caption", "username", "createdAtDate"]` | Fields to return |
| `startDate` | string | No | — | Start date (YYYY-MM-DD) |
| `endDate` | string | No | — | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache and fetch from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | `"fast"` | Response mode |
| `limit` | number | No | — | Max results |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Available Fields

Same as [getInstagramPostsByIds](#available-fields-5).

### Examples

**MCP:**
```json
{
  "tool": "getInstagramPostsByUser",
  "arguments": {
    "identifier": "natgeo",
    "identifierType": "username",
    "fields": ["id", "caption", "likeCount", "commentCount", "createdAtDate"],
    "startDate": "2026-01-01",
    "endDate": "2026-06-01"
  }
}
```

**Python SDK:**
```python
posts = client.instagram.get_posts_by_user(
    "natgeo",
    identifier_type="username",
    fields=["id", "caption", "like_count", "comment_count", "created_at_date"],
    start_date="2026-01-01",
    end_date="2026-06-01"
)
```

**TypeScript SDK:**
```typescript
const posts = await client.instagram.getPostsByUser("natgeo", {
  identifierType: "username",
  fields: ["id", "caption", "likeCount", "commentCount", "createdAtDate"],
  startDate: "2026-01-01",
  endDate: "2026-06-01",
});
```

**CLI:**
```bash
xpoz-cli instagram get_posts_by_user natgeo --identifier-type username --fields id caption like_count comment_count created_at_date --start-date 2026-01-01 --end-date 2026-06-01
```

---

## getInstagramPostsByKeywords

Search Instagram posts by keywords in captions and subtitles.

**Data freshness:** Returns cached data from DB, with automatic API fallback if data is stale (>1 week).

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | string | Yes | — | Search query (supports boolean syntax) |
| `fields` | string[] | No | `["id", "caption", "username", "createdAtDate"]` | Fields to return |
| `startDate` | string | No | — | Start date (YYYY-MM-DD) |
| `endDate` | string | No | — | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache and fetch from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | `"fast"` | Response mode |
| `limit` | number | No | — | Max results |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Available Fields

Same as [getInstagramPostsByIds](#available-fields-5).

### Examples

**MCP:**
```json
{
  "tool": "getInstagramPostsByKeywords",
  "arguments": {
    "query": "\"artificial intelligence\" AND ethics",
    "fields": ["id", "caption", "username", "likeCount", "createdAtDate"],
    "startDate": "2026-01-01",
    "endDate": "2026-06-01"
  }
}
```

**Python SDK:**
```python
posts = client.instagram.search_posts(
    "\"artificial intelligence\" AND ethics",
    fields=["id", "caption", "username", "like_count", "created_at_date"],
    start_date="2026-01-01",
    end_date="2026-06-01"
)
```

**TypeScript SDK:**
```typescript
const posts = await client.instagram.searchPosts(
  '"artificial intelligence" AND ethics',
  {
    fields: ["id", "caption", "username", "likeCount", "createdAtDate"],
    startDate: "2026-01-01",
    endDate: "2026-06-01",
  }
);
```

**CLI:**
```bash
xpoz-cli instagram search_posts "\"artificial intelligence\" AND ethics" --fields id caption username like_count created_at_date --start-date 2026-01-01 --end-date 2026-06-01
```

---

## getInstagramCommentsByPostId

Get comments on a specific Instagram post.

> **Requires strong_id format** for `postId` (e.g., `"3606450040306139062_4836333238"`).

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `postId` | string | Yes | — | Post ID in **strong_id** format (`{media_id}_{user_id}`) |
| `fields` | string[] | No | `["id", "text", "username", "createdAtDate", "likeCount"]` | Fields to return |
| `startDate` | string | No | — | Start date (YYYY-MM-DD) |
| `endDate` | string | No | — | End date (YYYY-MM-DD) |
| `forceLatest` | boolean | No | false | Bypass cache and fetch from API |
| `responseType` | `"fast"` \| `"paging"` \| `"csv"` | No | `"fast"` | Response mode |
| `limit` | number | No | — | Max results |
| `pageNumber` | number | No | — | Start page (paging mode) |
| `pageNumberEnd` | number | No | — | End page (paging mode) |
| `tableName` | string | No | — | Resume from a previous operation |

### Available Fields

`id`, `text`, `username`, `createdAtDate`, `likeCount`

### Examples

**MCP:**
```json
{
  "tool": "getInstagramCommentsByPostId",
  "arguments": {
    "postId": "3606450040306139062_4836333238",
    "fields": ["id", "text", "username", "createdAtDate", "likeCount"],
    "limit": 50
  }
}
```

**Python SDK:**
```python
comments = client.instagram.get_comments(
    "3606450040306139062_4836333238",
    fields=["id", "text", "username", "created_at_date", "like_count"],
    start_date="2026-01-01",
    end_date="2026-06-01"
)
```

**TypeScript SDK:**
```typescript
const comments = await client.instagram.getComments(
  "3606450040306139062_4836333238",
  {
    fields: ["id", "text", "username", "createdAtDate", "likeCount"],
    startDate: "2026-01-01",
    endDate: "2026-06-01",
  }
);
```

**CLI:**
```bash
xpoz-cli instagram get_comments "3606450040306139062_4836333238" --fields id text username created_at_date like_count --start-date 2026-01-01 --end-date 2026-06-01
```
