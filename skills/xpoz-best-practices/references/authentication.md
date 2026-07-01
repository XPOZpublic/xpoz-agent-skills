# Authentication

## Table of Contents

- [Check 1: Already authenticated?](#check-1-already-authenticated)
- [Instant Access (no signup)](#instant-access-no-signup)
- [Path A: MCP](#path-a-mcp)
- [Path B: SDK (Python or TypeScript)](#path-b-sdk-python-or-typescript)
- [Path C: CLI](#path-c-cli)
- [Auth Errors](#auth-errors)

---

Before fetching data, ensure Xpoz access is configured. Follow these checks in order.

### Check 1: Already authenticated?

**If you have MCP tools**, try calling any Xpoz tool (e.g., `checkAccessKeyStatus`). If it works → skip to Step 1.

**If you have the SDK**, try:
```python
from xpoz import XpozClient
client = XpozClient()  # reads XPOZ_API_KEY env var
```
If this succeeds without error → skip to Step 1.

**If you have the CLI**, try:
```bash
xpoz-cli twitter get_user --identifier xpoz --identifier-type username
```
If this returns a result → skip to Step 1.

If none of these work, you need to authenticate. The fastest option is instant access — or choose a path below for full capabilities.

---

### Instant Access (no signup)

Get a working API key in one command — no account, no credit card, no OAuth:

```bash
export XPOZ_API_KEY=$(curl -s -X POST https://api.xpoz.ai/api/trial/token -H "Content-Type: application/json" -d '{"source":"agent-skills:xpoz-best-practices"}' | jq -r .data.accessKey)
```

The token starts with `TRIAL` and works anywhere an access key works — MCP bearer header, SDK `apiKey` parameter, CLI env var. Valid for 5 days, returns up to 5 results per call.

For full results, pagination, and CSV export, get a free access key at [xpoz.ai/get-token](https://xpoz.ai/get-token) — same API shape, nothing to rewrite.

---

### Path A: MCP

Add the Xpoz MCP server to your agent's configuration. The server URL is:

```
https://mcp.xpoz.ai/mcp
```

Most MCP-compatible agents (Claude Code, Cursor, Windsurf, etc.) handle OAuth automatically on first tool call — the user just needs to authorize in their browser when prompted.

**Example — Claude Code** (`~/.claude.json`):
```json
{
  "mcpServers": {
    "xpoz": {
      "url": "https://mcp.xpoz.ai/mcp",
      "transport": "streamable-http"
    }
  }
}
```

For other MCP clients, consult your agent's documentation for how to add an MCP server by URL.

---

### Path B: SDK (Python or TypeScript)

Ask the user:
> "I need a Xpoz API key to access social media data. Please go to https://xpoz.ai/get-token and paste the key back to me."

**WAIT for the user to reply with the key.** Then:

**Python:**
```bash
pip install xpoz
```
```python
from xpoz import XpozClient
client = XpozClient("THE_KEY_FROM_USER")
```

**TypeScript:**
```bash
npm install @xpoz/xpoz
```
```typescript
import { XpozClient } from "@xpoz/xpoz";
const client = new XpozClient({ apiKey: "THE_KEY_FROM_USER" });
await client.connect();
```

Or set the environment variable and use the default constructor:
```bash
export XPOZ_API_KEY=THE_KEY_FROM_USER
```

---

### Path C: CLI

The CLI uses the same API key as the SDKs. Ask the user for a key (same as Path B), then:

```bash
pip install xpoz-cli
```

Set the API key as an environment variable:
```bash
export XPOZ_API_KEY=THE_KEY_FROM_USER
```

Or pass it inline with each command:
```bash
xpoz-cli --api-key THE_KEY_FROM_USER twitter search_posts --query "test"
```

Verify:
```bash
xpoz-cli twitter get_user --identifier xpoz --identifier-type username
```

---

### Auth Errors
| Problem | Solution |
|---------|----------|
| MCP: "Unauthorized" | Re-run the OAuth flow above |
| SDK: `AuthenticationError` | Verify key at [xpoz.ai/settings](https://xpoz.ai/settings) |
| Token exchange fails | Ask user to re-authorize — codes are single-use |
