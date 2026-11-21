# Kintopp Plugins

A plugin marketplace for Claude (claude.ai, Claude Desktop, Claude Code) and ChatGPT (desktop app, Codex).
Each plugin bundles a remote MCP connector with its research skill. The plugin files live in each project's
own public repository; this repository only lists them.

| Plugin | Display name | Source |
|---|---|---|
| `odeuropa-eokg` | Odeuropa EOKG | [kintopp/odeuropa-eokg-mcp](https://github.com/kintopp/odeuropa-eokg-mcp/tree/main/plugin/odeuropa-eokg) |
| `skkg` | SKKG Collection | [kintopp/skkg-mcp](https://github.com/kintopp/skkg-mcp/tree/main/plugin/skkg) |
| `rijksmuseum-iconclass` | Rijksmuseum Iconclass | [kintopp/rijksmuseum-iconclass-mcp](https://github.com/kintopp/rijksmuseum-iconclass-mcp/tree/main/plugin/rijksmuseum-iconclass) |
| `rijksmuseum` | Rijksmuseum MCP+ | [kintopp/rijksmuseum-mcp-plus](https://github.com/kintopp/rijksmuseum-mcp-plus/tree/main/plugin/rijksmuseum) |

## Files

- `.claude-plugin/marketplace.json`: the catalog Claude reads.
- `.agents/plugins/marketplace.json`: the same catalog in the format ChatGPT and Codex read.

- `skills/kintopp-plugin-packaging/`: a skill for coding agents that package a server repo as a plugin here. It is not a plugin and isn't listed in either catalog.

Keep the two files in step: same marketplace `name`, same plugin names and sources.

## Add the marketplace

- **claude.ai or Claude Desktop:** Customize → Plugins → Add → Add marketplace, enter `kintopp/kintopp-plugins`, then add each plugin
  and connect its connector on the plugin's Connectors tab. This works on free accounts too.
- **Claude Code:** `claude plugin marketplace add kintopp/kintopp-plugins`, then
  `claude plugin install odeuropa-eokg@kintopp-plugins`, `claude plugin install skkg@kintopp-plugins`, `claude plugin install rijksmuseum-iconclass@kintopp-plugins` or `claude plugin install rijksmuseum@kintopp-plugins`.
- **ChatGPT desktop app:** Plugins → Add plugin marketplace, enter `kintopp/kintopp-plugins` as the Source and leave
  Git ref and Sparse paths empty. Then install from the plugin browser. This works on free accounts too, but the plugin runs only in Codex mode,
  not in ordinary ChatGPT chats. ChatGPT on the web (chatgpt.com) can't add a
  marketplace.
- **Codex CLI:** `codex plugin marketplace add kintopp/kintopp-plugins`.
