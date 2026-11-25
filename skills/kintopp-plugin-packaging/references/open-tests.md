# Open tests (only the user can run these, in the apps)

None of these block packaging. Until a test has a recorded result, keep its subject out
of READMEs as a promise. When the user reports a result, move it into `platforms.md` with
the date, the client and the plan.

## 1. Duplicate tools

When a user already has the server as a standalone custom connector with the same URL,
do the plugin's tools appear once or twice?

- **Steps:** with the standalone connector present, install the plugin and connect its
  connector. Look at the tool list in a chat and on the Connectors page.
- **Record:** one set or two. If two, the README tells users to remove the standalone
  connector first.

## 2. Updates on Claude

Does a version bump reach an installed plugin?

- **Steps:** after a bump is pushed, use **Check for updates** on the marketplace, or wait
  with automatic sync turned on.
- **Record:** whether the plugin page shows the new version without a reinstall, and how
  long it took.
- **Second step:** does remove-and-reinstall without a bump serve cached files?

## 3. Viewer through a plugin connector

For servers with an MCP App viewer, does the viewer render when the connector comes from
a plugin?

- **Where:** in claude.ai, Claude Desktop, and ChatGPT desktop in Codex mode.
- **Until then:** the README offers the direct remote connector, local stdio or `.mcpb`
  for the viewer.

## 4. ChatGPT custom connector in Codex mode

Does a connector added through ChatGPT's custom-connector route also appear in Codex mode,
and is that route open to free accounts?

## 5. Plugin icon

Does the icon from `files.md` § Icon show up?

- **Claude:** on the plugin's page and in the marketplace list in claude.ai and Desktop.
  The docs say only Anthropic's directory reads `icon`, so "no" is the expected answer.
- **ChatGPT desktop:** in the plugin browser before install, on the plugin page after
  install, and in the composer (`composerIcon`).
- **Record:** where it appears and where it doesn't, with the date and app version. If
  ChatGPT shows nothing before install, try whether an `interface.logo` on the marketplace
  entry is read, and what its path is relative to.
