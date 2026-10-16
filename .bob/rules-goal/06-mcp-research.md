# MCP Research

Use MCP for current, source-grounded research. Always probe which servers are live before querying.

## Auto-discovery and fallback chain

At the start of every discovery phase, probe the MCP servers:

```bash
mcp_list_servers 2>&1 | grep "active\|connected" || echo "No MCP"
```

Record which are live in `.goal/discovery.md` (see [03-discovery.md](03-discovery.md)).

## Priority chains by use case

### Framework and library documentation

Query in this order until you get results:

1. **Context7** (if live) — official docs, changelogs, API references
2. **Exa** (if live) — web-sourced docs, blog posts, guides
3. **`web_search`** (built-in tool) — final fallback when no MCP available

Stop at the first tier that returns usable results. If Context7 is live but returns nothing, try Exa
before falling back to `web_search`.

### IBM Carbon UI guidance

For goals that involve UI work (rendering components, layout, design tokens):

1. **Carbon MCP** (if live) — official Carbon components, guidelines, code snippets
2. **`web_search` → "IBM Carbon Design System [query]"** (fallback)

### General web research

For non-docs research (news, examples, blog posts, third-party guides):

1. **Exa** (if live) — web search tuned for high-quality sources
2. **`web_search`** (built-in tool) — always available

## Credential handling

- Never commit real credentials to `.bob/mcp.json`.
- Use `.bob/mcp.example.json` as a template with placeholder keys.
- Add `mcp.json` and `.bob/mcp.json` to `.gitignore`.
- Prefer global Bob MCP settings (`~/.bob/mcp.json` or `~/.bob/mcp_settings.json`) for shared configs.

## If MCP tools are not available

Record `mcp_unavailable: true` in `.goal/discovery.md` and proceed using only `web_search`. Do not
halt — the loop can complete successfully with built-in tools alone, it just loses the benefit of
curated official sources.
