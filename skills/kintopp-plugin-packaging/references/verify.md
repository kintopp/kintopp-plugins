# Verification

Silent failure is the typical bug: a wrong enum value, a path that resolves elsewhere, or
a skill that lost its `references/` still installs "successfully". Each check below is one
that could fail. Record the client version with every result (`claude --version`,
`codex --version`).

## 1. Static checks

```bash
claude plugin validate plugin/<plugin>          # Claude manifest + .mcp.json
claude plugin validate <kintopp-plugins clone>  # Claude's catalog only (.claude-plugin/marketplace.json)
```

Add `--strict` to either command to fail on warnings too, such as an unknown field or a
name that reads as Anthropic's.

`.agents/plugins/marketplace.json` has no validator here: check it as valid JSON with the
same plugin names and sources as Claude's catalog, then let the Codex install ladder below
prove it. Check the Agent Plugins schemas, which the validator above doesn't cover. Fetch each
schema from its `$schema` URL into a scratch directory, then:

```bash
npx -y ajv-cli@5 validate --spec=draft2020 -s <scratch>/plugin.schema.json -d plugin/<plugin>/plugin.json
npx -y ajv-cli@5 validate --spec=draft2020 -s <scratch>/mcp.schema.json    -d plugin/<plugin>/mcp.json
```

Then run the repo's own tests and lint, including the new drift checks. Break one check on
purpose to see it fail, then restore it.

## 2. The endpoint, as a client calls it

POST an MCP `initialize` to `<url>` with `Accept: application/json, text/event-stream` and
**no `Origin` header**. If that fails, the problem is the server's guards, not the
packaging.

## 3. Install ladder from a local marketplace

You can test before anything is pushed. Make a scratch directory, outside both repos,
holding both catalog files with local sources (`"source": "./<plugin>"` for Claude,
`{"source": "local", "path": "./<plugin>"}` for Codex) and a copy of `plugin/<plugin>/`.
Both CLIs install from such a directory without git (verified 2026-11-21, Claude Code
2.1.330 and codex-cli 0.167.0), so a folder that is still gitignored in the server repo
doesn't matter there. Once the server repo's `main` has the folder, test against the
kintopp-plugins clone instead.

```bash
codex plugin marketplace add <path-or-owner/repo>
codex plugin list                                 # rung 1: listed?
codex plugin add <plugin>@<marketplace>           # rung 2: installs?
find ~/.codex/plugins/cache/<marketplace>/<plugin>/<version> -type f   # rung 3: every skill file present?
```

```bash
claude plugin marketplace add <path-or-owner/repo>
claude plugin install <plugin>@<marketplace>
claude plugin details <plugin>@<marketplace>      # one skill + the <Key> server?
```

Rung 4 is a live session: ask the client to list its skills without using tools.
`<plugin>:<skill>` must appear. Then remove the test installs and any scratch marketplace
(`codex plugin remove`, `claude plugin uninstall`, `… marketplace remove`). Don't remove
the user's registered `kintopp-plugins` marketplace.

After the user pushes both repos, run `claude plugin marketplace update kintopp-plugins`
and `codex plugin marketplace upgrade`. Each plugin must be listed exactly once.

## The confound, and probe plugins

A plugin's server shows up in the client's server list, and so does a connector the user
added by hand under the same key. So a server row alone proves nothing about the plugin;
the skill does, because it has no other way in.

To settle what a client reads or shows, build the smallest plugin that only one answer
explains. Give it unique names (marketplace, plugin, key, skill), version `0.0.1`, and a
marker sentence in the skill. Install it, observe, then remove it everywhere. The
connector-label question was settled this way: one throwaway plugin declared one URL under
several candidate keys. Note that connecting a probe's connector in claude.ai leaves a
standalone custom connector behind after the probe is removed, so tell the user to delete
it.
