# Authentication

## Table of Contents

- [Check 1: Already authenticated?](#check-1-already-authenticated)
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

If none of these work, you need to authenticate. Choose the path that fits your environment:

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
