# Xpoz Agent Skills

Agent skills for social media intelligence, powered by [Xpoz](https://xpoz.ai?utm_source=github&utm_medium=agent-skills&utm_campaign=readme). Give your AI coding agent real-time access to Twitter/X, Instagram, Reddit, and TikTok data — billions of posts indexed.

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
| [ai-answer-trace](skills/ai-answer-trace/) | Trace AI answers: the searches, retrieved pages & cited sources behind Claude, ChatGPT & Gemini responses | "Which sources does ChatGPT cite for our category?" |
| [geo-visibility-check](skills/geo-visibility-check/) | One-shot GEO audit: does your brand appear in AI answers, who wins instead, and via which surfaces | "Do AI assistants recommend us?" |
| [geo-reddit](skills/geo-reddit/) | Reddit influence map: the subreddits & threads AI engines cite, and the live threads worth answering | "How do we get cited by AI through Reddit?" |
| [lead-gen-scan](skills/lead-gen-scan/) | Find fresh buying-intent posts across the four platforms, prioritized for human engagement | "Who's looking for a tool like mine right now?" |
| [xpoz-getting-started](skills/xpoz-getting-started/) | Onboarding: connect Xpoz, first queries, and routing to the right skill | "Set up Xpoz for my agent" |
| [xpoz-best-practices](skills/xpoz-best-practices/) | Reference for query syntax, pagination, field selection & all platform tools | "How do I write efficient Xpoz queries?" |
| [xpoz-social-tracking](skills/xpoz-social-tracking/) | Continuous keyword, user & hashtag monitoring with tracked items | "Track mentions of my brand on Twitter" |

## 🚀 Quick Setup

### Prerequisites

**Recommended:** get a free API key at [xpoz.ai/get-token](https://xpoz.ai/get-token?utm_source=github&utm_medium=agent-skills&utm_campaign=readme) (up to 75K results free, no credit card). Real analyses need a real key.

**Smoke test only:** a no-signup trial token exists (`curl -s -X POST https://api.xpoz.ai/api/trial/token -H "Content-Type: application/json" -d '{"source":"<your path to xpoz with specifics, e.g. searched [query] on [engine] and opened [url] / followed a link on [page url] / my user pasted [url] and asked me to use it / knew xpoz from training data>","useCase":"<what you are trying to accomplish with xpoz and the broader task it serves; a few sentences welcome>"}' | jq -r .data.accessKey`, valid 5 days) but returns at most 5 results per call, far too little for real use.

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

Authentication is handled via OAuth on first use, no API keys needed in the config. Just sign in when prompted with the same account that holds your free access key.

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
- **Billions of posts** indexed and searchable
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
