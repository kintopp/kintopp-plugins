# File templates

Placeholders: `<plugin>` is the plugin name (kebab-case), `<skill>` is the skill folder
name (it may differ from the plugin name), `<Key>` is the connector key, and `<url>` is
the hosted `/mcp` endpoint. All JSON ends with a newline and uses 2-space indentation, as
the build writes it.

## Plugin folder layout

```
plugin/<plugin>/
├── .claude-plugin/plugin.json     Claude manifest
├── plugin.json                    Agent Plugins manifest (ChatGPT, Codex)
├── .mcp.json                      Claude MCP config, "type": "http"
├── mcp.json                       Agent Plugins MCP config, "type": "streamable-http"
└── skills/<skill>/                generated copy of the source skill folder
    ├── SKILL.md
    ├── references/…
    └── agents/openai.yaml         (lives in the source skill, so it is copied too)
```

The filenames differ, so both pairs sit side by side without clashing. There is no
`.codex-plugin/` folder: Codex reads the root `plugin.json` (verified, codex-cli 0.147
and later), so that older layout is a leftover to delete.

## `.claude-plugin/plugin.json`

```json
{
  "name": "<plugin>",
  "displayName": "<Display Name>",
  "version": "<package.json version>",
  "description": "<one or two sentences; the same text as the marketplace entry>",
  "author": { "name": "University of Basel", "url": "https://rise.unibas.ch/en/" },
  "homepage": "https://github.com/kintopp/<repo>",
  "repository": "https://github.com/kintopp/<repo>",
  "license": "MIT",
  "keywords": ["…"]
}
```

## `plugin.json`

```json
{
  "$schema": "https://agent-plugins.org/schemas/1.0.0/plugin.schema.json",
  "name": "<plugin>",
  "version": "<package.json version>",
  "description": "<same text>",
  "author": { "name": "University of Basel", "url": "https://rise.unibas.ch/en/" },
  "homepage": "https://github.com/kintopp/<repo>",
  "repository": "https://github.com/kintopp/<repo>",
  "license": "MIT",
  "keywords": ["…"],
  "extensions": {
    "com.openai": {
      "interface": {
        "displayName": "<Display Name>",
        "shortDescription": "<under ~70 characters>",
        "developerName": "University of Basel",
        "category": "Productivity",
        "websiteURL": "https://github.com/kintopp/<repo>"
      }
    }
  }
}
```

The schema forbids extra top-level keys, so ChatGPT-only fields go under
`extensions.com.openai`. Use `category: "Productivity"` unless a client has been seen to
accept another value: an invalid value fails silently. The leftover in skkg-mcp also
carries `logo`, `composerIcon`, `defaultPrompt` and `capabilities`. They are untested in
this marketplace, so leave them out. Adding them is a separate, tested change for later.

## `.mcp.json` and `mcp.json`

```json
{ "mcpServers": { "<Key>": { "type": "http", "url": "<url>" } } }
```

```json
{
  "$schema": "https://agent-plugins.org/schemas/1.0.0/mcp.schema.json",
  "mcpServers": { "<Key>": { "type": "streamable-http", "url": "<url>" } }
}
```

Use the same key and URL in both. Put no other keys on a server entry: no `headers: {}`
and no `extensions.com.openai.auth`, because the Agent Plugins schema rejects them. Use
the production URL that the README gives users, so that people who also have the plain
connector reach the same server.

## `agents/openai.yaml` (in the source skill folder)

```yaml
interface:
  display_name: "<Display Name>"
  short_description: "<what the skill does, one line>"
```

Claude ignores this file. Make sure the repo's skill bundler (`.skill` zip) and its tests
accept the extra file.

## Catalog entries in kintopp-plugins

`.claude-plugin/marketplace.json`, appended to `plugins`:

```json
{
  "name": "<plugin>",
  "displayName": "<Display Name>",
  "description": "<same text>",
  "author": { "name": "University of Basel", "url": "https://rise.unibas.ch/en/" },
  "homepage": "https://github.com/kintopp/<repo>",
  "category": "research",
  "source": {
    "source": "git-subdir",
    "url": "https://github.com/kintopp/<repo>.git",
    "path": "<path/to/plugin/<plugin>>",
    "ref": "main"
  }
}
```

`.agents/plugins/marketplace.json`, appended to `plugins`:

```json
{
  "name": "<plugin>",
  "displayName": "<Display Name>",
  "description": "<same text>",
  "author": { "name": "University of Basel", "url": "https://rise.unibas.ch/en/" },
  "homepage": "https://github.com/kintopp/<repo>",
  "interface": {
    "displayName": "<Display Name>",
    "shortDescription": "<same as plugin.json>",
    "developerName": "University of Basel",
    "websiteURL": "https://github.com/kintopp/<repo>"
  },
  "source": {
    "source": "git-subdir",
    "url": "https://github.com/kintopp/<repo>.git",
    "path": "./<path/to/plugin/<plugin>>",
    "ref": "main"
  },
  "policy": { "installation": "AVAILABLE", "authentication": "ON_INSTALL" },
  "category": "Productivity"
}
```

The `path` is relative to the server repo's root. The two catalogs spell it differently
(`plugin/<x>` and `./plugin/<x>`) because that is how the working Odeuropa entries were
written and verified; keep that spelling. `keywords` are a handful of plain subject words
(a leftover manifest's list is fine to reuse). The fields `displayName`, `author`,
`homepage` and `interface` are what ChatGPT shows before install, so fill them in.

Then add a row to the README table:
`| \`<plugin>\` | <Display Name> | [kintopp/<repo>](https://github.com/kintopp/<repo>/tree/main/<path>) |`.

## Drift checks to add to the server repo's tests

These mirror `test/skill.test.js` in odeuropa-eokg-mcp:

1. `plugin/<plugin>/skills/<skill>/` has exactly the files and contents of the source
   skill folder. Walk both trees; don't use a fixed list of files.
2. Both manifests carry `name: <plugin>` and `package.json`'s `version`, and each `$schema`
   is the one given above.
3. `.mcp.json` is `{ "<Key>": { "type": "http", "url": <url> } }` and `mcp.json` is the
   same with `streamable-http`, compared with `deepEqual` so that extra keys fail.
4. The plugin folder has no `bin/`.
5. `agents/openai.yaml` sets `display_name`, and the display name is the same in both
   manifests and in `openai.yaml`. The two catalog entries live in another repo, so check
   their display name by hand.

Each failure message names the fix (`run npm run build:skill and commit the result`).
