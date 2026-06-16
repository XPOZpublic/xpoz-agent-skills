# CLI Reference

## Table of Contents

- [Installation](#installation)
- [Command Structure](#command-structure)
- [Global Options](#global-options)
- [Parameter Naming](#parameter-naming)
- [Lists](#lists)
- [Rendering Modes](#rendering-modes)
  - [Standard (JSON)](#standard-json)
  - [CSV Export](#csv-export)
  - [Paginated Walk](#paginated-walk)
  - [Jump to Page](#jump-to-page)
- [Examples by Platform](#examples-by-platform)
  - [Twitter](#twitter)
  - [Instagram](#instagram)
  - [Reddit](#reddit)
  - [TikTok](#tiktok)

---

## Installation

```bash
pip install xpoz-cli
```

Verify installation:
```bash
xpoz-cli --help
```

---

## Command Structure

```
xpoz-cli [global-opts] <platform> <method> [--arg value]
```

- **platform**: `twitter`, `instagram`, `reddit`, `tiktok`, `tracking`
- **method**: snake_case SDK method name (e.g., `search_posts`, `get_user`)
- **args**: method-specific parameters as `--kebab-case` flags

---

## Global Options

| Option | Environment Variable | Description |
|--------|---------------------|-------------|
| `--api-key KEY` | `XPOZ_API_KEY` | API key for authentication |
| `--server-url URL` | — | Custom server URL (defaults to production) |

If `--api-key` is not provided, the CLI reads from the `XPOZ_API_KEY` environment variable.

```bash
# Using the flag
xpoz-cli --api-key your-key twitter search_posts --query "AI"

# Using the environment variable
export XPOZ_API_KEY=your-key
xpoz-cli twitter search_posts --query "AI"
```

---

## Parameter Naming

SDK parameter names in **snake_case** are converted to **--kebab-case** flags:

| SDK Parameter | CLI Flag |
|--------------|----------|
| `start_date` | `--start-date` |
| `end_date` | `--end-date` |
| `identifier_type` | `--identifier-type` |
| `response_type` | `--response-type` |
| `page_number` | `--page-number` |
| `force_latest` | `--force-latest` |
| `filter_out_retweets` | `--filter-out-retweets` (Twitter only) |

---

## Lists

Pass multiple values to list parameters by separating them with spaces:

```bash
# Select specific fields
xpoz-cli twitter search_posts --query "AI" --fields id text author_username like_count

# Multiple identifiers
xpoz-cli twitter get_users --identifiers elonmusk kaborahane --identifier-type username

# Multiple hashtags
xpoz-cli tiktok get_posts_by_hashtags --hashtags ai machinelearning deeplearning
```

---

## Rendering Modes

**Note:** CSV export and paginated walk modes are async operations and may take longer than the default JSON mode.

### Standard (JSON)

By default, the CLI outputs JSON to stdout.

```bash
xpoz-cli twitter search_posts --query "bitcoin" --limit 10
```

Output:
```json
{
  "data": [...],
  "pagination": {
    "totalRows": 15000,
    "pageNumber": 1,
    "totalPages": 150
  }
}
```

Pipe to `jq` for filtering:
```bash
xpoz-cli twitter search_posts --query "bitcoin" --limit 10 | jq '.data[].text'
```

### CSV Export

Export results directly to a CSV file on S3 and print the download URL.

```bash
xpoz-cli twitter search_posts --query "bitcoin" --response-type csv --export-csv-url
```

Output:
```
https://s3.amazonaws.com/xpoz-exports/export_abc123.csv
```

### Paginated Walk

Automatically iterate through all pages and output all results.

```bash
# Walk all pages
xpoz-cli reddit search_posts --query "python" --all-pages

# Walk up to N pages
xpoz-cli reddit search_posts --query "python" --all-pages --max-pages 5
```

### Jump to Page

Fetch a specific page number from a paginated result set.

```bash
xpoz-cli twitter search_posts --query "AI" --response-type paging --page 3
```

---

## Examples by Platform

### Twitter

**Search posts with date range and limit:**
```bash
xpoz-cli twitter search_posts --query "bitcoin" --start-date 2025-01-01 --limit 20
```

**Get a user profile:**
```bash
xpoz-cli twitter get_user --identifier elonmusk --identifier-type username
```

**Get posts by author with field selection:**
```bash
xpoz-cli twitter get_posts_by_author --identifier elonmusk --fields id text like_count retweet_count created_at
```

**Count tweets matching a phrase:**
```bash
xpoz-cli twitter count_posts --query "artificial intelligence"
```

**Search users who post about a topic:**
```bash
xpoz-cli twitter get_users_by_keywords --query "machine learning researcher"
```

### Instagram

**Get a user profile:**
```bash
xpoz-cli instagram get_user --identifier cristiano
```

**Search posts by keyword:**
```bash
xpoz-cli instagram search_posts --query "fitness" --start-date 2025-01-01 --limit 50
```

**Get posts from a specific user:**
```bash
xpoz-cli instagram get_posts_by_user --identifier cristiano --limit 20
```

**Get comments on a post:**
```bash
xpoz-cli instagram get_comments --post-id "3012345678901234567"
```

### Reddit

**Search posts with subreddit filter and paginate through all results:**
```bash
xpoz-cli reddit search_posts --query "python" --subreddit learnpython --all-pages
```

**Get a post with all its comments:**
```bash
xpoz-cli reddit get_post_with_comments --post-id "t3_abc123"
```

**Search subreddits by name:**
```bash
xpoz-cli reddit search_subreddits --query "programming"
```

**Find users who post about a topic:**
```bash
xpoz-cli reddit get_users_by_keywords --query "data science"
```

### TikTok

**Search posts and export to CSV:**
```bash
xpoz-cli tiktok search_posts --query "ai" --response-type csv --export-csv-url
```

**Get a user profile:**
```bash
xpoz-cli tiktok get_user --identifier charlidamelio
```

**Search posts by hashtags:**
```bash
xpoz-cli tiktok get_posts_by_hashtags --hashtags ai machinelearning --limit 50
```

**Find users who used specific hashtags:**
```bash
xpoz-cli tiktok get_users_by_hashtags --hashtags fitness workout --limit 20
```