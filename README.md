# Xpoz Agent Skills

Agent skills for social media intelligence, powered by [Xpoz](https://xpoz.ai?utm_source=github&utm_medium=agent-skills&utm_campaign=readme). Give your AI coding agent real-time access to Twitter/X, Instagram, Reddit, and TikTok data — 1.5B+ posts indexed.

Compatible with **Claude Code**, **OpenAI Codex CLI**, **ChatGPT**, and any agent that supports the [SKILL.md](https://github.com/anthropics/skills) standard.

## 🧠 Skills

| Skill | Description | Use When |
|-------|-------------|----------|
| [social-sentiment-analyzer](skills/social-sentiment-analyzer/) | Analyze brand/topic sentiment across Twitter, Reddit & Instagram | "What's the sentiment around Tesla?" |
| [twitter-data-export](skills/twitter-data-export/) | Export Twitter search results to CSV (up to 500K rows) | "Export tweets about AI from last month" |
| [influencer-discovery](skills/influencer-discovery/) | Find and rank influencers by niche, engagement & authenticity | "Find top crypto influencers on Twitter" |
| [reddit-research](skills/reddit-research/) | Search and analyze Reddit discussions for market research | "What are people saying about Cursor on Reddit?" |
| [competitive-intel](skills/competitive-intel/) | Compare brands: share of voice, sentiment & positioning | "Compare Slack vs Discord vs Teams sentiment" |
| [security-osint](skills/security-osint/) | Monitor social platforms for vulnerability & threat discussions | "Find discussions about Log4j on Twitter and Reddit" |

## 🚀 Quick Setup

### Prerequisites

Get a free API key at [xpoz.ai/get-token](https://xpoz.ai/get-token?utm_source=github&utm_medium=agent-skills&utm_campaign=readme) — 100,000 results/month free, no credit card required.

### Install Skills

```bash
# Clone the repo
git clone https://github.com/XPOZpublic/xpoz-agent-skills.git

# Copy skills to your agent's skills directory
# Claude Code:
cp -r xpoz-agent-skills/skills/* ~/.claude/skills/

# Or install individual skills:
cp -r xpoz-agent-skills/skills/social-sentiment-analyzer ~/.claude/skills/
```

### Configure Xpoz

Each skill supports **two integration paths** — pick whichever fits your workflow:

#### Option A: MCP Server (recommended for Claude Code)

Add to your `~/.claude.json`:

```json
{
  "mcpServers": {
    "xpoz": {
      "url": "https://mcp.xpoz.ai/mcp",
      "transport": "http-stream"
    }
  }
}
```

Authentication is handled via OAuth on first use — no API keys needed in the config. Just sign in with your Google account when prompted.

If using **mcporter** (included with OpenClaw):
```bash
mcporter config add xpoz https://mcp.xpoz.ai/mcp --auth oauth
mcporter config login xpoz
```

#### Option B: Python SDK (recommended for coding tasks)

```bash
pip install xpoz
export XPOZ_API_KEY=your-api-key
```

#### Option C: TypeScript SDK

```bash
npm install @xpoz/xpoz
export XPOZ_API_KEY=your-api-key
```

## 📊 What Xpoz Provides

- **Multi-platform**: Twitter/X, Instagram, Reddit, TikTok
- **1.5B+ posts** indexed and searchable
- **Natural language queries** with boolean operators
- **CSV exports** up to 500K rows in one download
- **User intelligence**: profiles, followers, engagement metrics, authenticity scoring
- **Real-time + historical**: rolling 60-day windows with instant results

## 📄 License

MIT — see [LICENSE](LICENSE).

## 🔗 Links

- **Xpoz Platform**: [xpoz.ai](https://xpoz.ai?utm_source=github&utm_medium=agent-skills&utm_campaign=readme)
- **Get API Key**: [xpoz.ai/get-token](https://xpoz.ai/get-token?utm_source=github&utm_medium=agent-skills&utm_campaign=readme)
- **Python SDK**: [github.com/XPOZpublic/xpoz-python-sdk](https://github.com/XPOZpublic/xpoz-python-sdk)
- **TypeScript SDK**: [github.com/XPOZpublic/xpoz-ts-sdk](https://github.com/XPOZpublic/xpoz-ts-sdk)
- **MCP Server**: [github.com/XPOZpublic/xpoz-mcp](https://github.com/XPOZpublic/xpoz-mcp)

## Pairing: RedditAPIs (optional managed Reddit data backend)

Users who already adopt this project sometimes ask about routing read-heavy operations (subreddit fetch, post detail, comments, user lookup, search) to a managed backend during testing or for workflows that skip the Reddit developer-app step. The [RedditAPIs code samples repo](https://github.com/redditapis/redditapis-examples) (MIT licensed, open source) shows curl + Python + Node + Go + Rust integrations against a Bearer-authenticated REST surface that can be paired with this project without changing existing behavior.

Two integration patterns:

1. **Side-by-side in your application.** Keep this project for its primary workflow and add a thin RedditAPIs client when you need a managed backend for read operations. Each call maps to whichever backend the user has configured.

2. **PRAW-style migration reference.** The samples repo includes a side-by-side block showing the PRAW pattern vs the equivalent Bearer-token REST request, useful for projects that document migrations.

Subset that pairs cleanly with this project's read path:

- subreddit listings (`hot`, `new`, `top`, `rising`)
- post detail + comments tree
- user profile + submissions
- search across subreddits

Repository: https://github.com/redditapis/redditapis-examples

This pairing is fully optional. No behavior change for existing users of this project.

