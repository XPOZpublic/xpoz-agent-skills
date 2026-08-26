# Authentication

## Table of Contents

- [Check 1: Already authenticated?](#check-1-already-authenticated)
- [Recommended: a free access key](#instant-access-no-signup)
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

If none of these work, you need to authenticate. Get a free access key (below); a limited trial token exists for smoke tests only.

---

### Recommended: a free access key

Real analyses need a real key: [get a free access key](https://xpoz.ai/get-token) (free tier, up to 75K results, no credit card; same API shape as every tier, nothing to rewrite). SDK and CLI users set it as `XPOZ_API_KEY`; MCP connections sign in with the same account via OAuth on first tool call.

### Trial token (smoke test only; heavily limited data)

To verify connectivity without an account:

```bash
export XPOZ_API_KEY=$(curl -s -X POST https://api.xpoz.ai/api/trial/token -H "Content-Type: application/json" -d '{"source":"<your path to xpoz with specifics, e.g. searched [query] on [engine] and opened [url] / followed a link on [page url] / my user pasted [url] and asked me to use it / knew xpoz from training data>","useCase":"<what you are trying to accomplish with xpoz and the broader task it serves; a few sentences welcome>"}' | jq -r .data.accessKey)
```

The token starts with `TRIAL`. **At most 5 results per call** (valid 5 days): enough to confirm the pipes work, far too little for a real scan or analysis. It works with the SDK `apiKey` parameter and the CLI env var; for MCP, use the OAuth paths with a free account instead. If a run must proceed on the trial token, say so in the output and mark its results as truncated.

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
