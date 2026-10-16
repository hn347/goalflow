# Discovery

Run discovery before creating the final spec.

## Step 0 — Probe available MCP servers

Before querying any MCP server, check which are actually connected and live. Run this check once at
the start of the discovery phase:

```bash
# List all connected MCP servers and their status (if MCP tools are available)
# This returns a list of server names that are live
mcp_list_servers 2>&1 | grep "active\|connected" || echo "No MCP servers connected"
```

Record which servers are available in `.goal/discovery.md` under **MCP servers**:

```markdown
## MCP servers

- Carbon MCP: [✅ live | ❌ unavailable]
- Context7: [✅ live | ❌ unavailable]  
- Exa: [✅ live | ❌ unavailable]
```

If MCP tools are not available at all (the command returns "No MCP servers connected" or errors),
record that and fall back to built-in `web_search` for all research needs.

## Repo discovery

Ask an `explore` subagent when available:

> Find the relevant files, architecture patterns, test commands, and risk areas for this goal. Return only paths, summaries, and uncertainties.

Record useful findings in `.goal/discovery.md`.

## Documentation and UI guidance research

Follow this priority chain based on what MCP servers are available:

### For framework/library docs
1. **Context7** (if live) — first choice for official docs
2. **Exa** (if live) — fallback if Context7 is unavailable or returns no results
3. **`web_search`** (built-in) — final fallback if no MCP servers connected

### For IBM Carbon UI guidance (when the goal includes UI work)
1. **Carbon MCP** (if live) — first choice
2. **`web_search`** (built-in) — fallback to search "IBM Carbon Design System [component name]"

Never assume a server is available — always check first. If a query to a server fails (timeout, auth
error, 5xx), immediately fall back to the next tier and record the failure in `discovery.md`.
