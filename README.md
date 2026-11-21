# Kintopp Demo Plugins

A private plugin marketplace for Claude (claude.ai, Claude Desktop, Claude Code) and ChatGPT (desktop app, Codex).
Each plugin bundles a remote MCP connector with its research skill. The plugin files live in each project's
own public repository; this repository only lists them.

| Plugin | Display name | Source |
|---|---|---|
| `odeuropa-eokg` | Odeuropa EOKG | [kintopp/odeuropa-eokg-mcp](https://github.com/kintopp/odeuropa-eokg-mcp/tree/main/plugin/odeuropa-eokg) |

## Files

- `.claude-plugin/marketplace.json`: the catalog Claude reads.
- `.agents/plugins/marketplace.json`: the same catalog in the format ChatGPT and Codex read.

Keep the two files in step: same marketplace `name`, same plugin names and sources.

## Add the marketplace

- **claude.ai or Claude Desktop:** Customize → Plugins → Add → Add marketplace, enter `kintopp/mcp-plugins`, and
  give the Claude GitHub App access to this repository when asked. Then add each plugin and connect its connector
  on the plugin's Connectors tab.
- **Claude Code:** `claude plugin marketplace add kintopp/mcp-plugins`, then
  `claude plugin install odeuropa-eokg@kintopp-demo-plugins`.
- **ChatGPT desktop app:** Plugins → Add plugin marketplace, enter `kintopp/mcp-plugins` as the Source and leave
  Git ref and Sparse paths empty. Then install from the plugin browser.
- **Codex CLI:** `codex plugin marketplace add kintopp/mcp-plugins`.
