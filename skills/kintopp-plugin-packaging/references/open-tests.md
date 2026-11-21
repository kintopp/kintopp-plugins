# Open tests (only the user can run these, in the apps)

None of these block packaging. Until a test has a recorded result, keep its subject out
of READMEs as a promise. When the user reports a result, move it into `platforms.md` with
the date, the client and the plan.

## 1. A second plugin connector on a free claude.ai account

Free accounts allow one custom connector. Does a plugin's connector count toward that
limit?

- **Steps:** on a free account with one plugin connected, install a second plugin and
  try to connect its connector.
- **Record:** whether it connects, the exact error if not, and whether the first one must
  be disabled or removed.

## 2. Duplicate tools

When a user already has the server as a standalone custom connector with the same URL,
do the plugin's tools appear once or twice?

- **Steps:** with the standalone connector present, install the plugin and connect its
  connector. Look at the tool list in a chat and on the Connectors page.
- **Record:** one set or two. If two, the README tells users to remove the standalone
  connector first.

## 3. Updates on Claude

Does a version bump reach an installed plugin?

- **Steps:** after a bump is pushed, use **Check for updates** on the marketplace, or wait
  with automatic sync turned on.
- **Record:** whether the plugin page shows the new version without a reinstall, and how
  long it took.
- **Second step:** does remove-and-reinstall without a bump serve cached files?

## 4. Viewer through a plugin connector

For servers with an MCP App viewer, does the viewer render when the connector comes from
a plugin?

- **Where:** in claude.ai, Claude Desktop, and ChatGPT desktop in Codex mode.
- **Until then:** the README offers the direct remote connector, local stdio or `.mcpb`
  for the viewer.

## 5. ChatGPT custom connector in Codex mode

Does a connector added through ChatGPT's custom-connector route also appear in Codex mode,
and is that route open to free accounts?
