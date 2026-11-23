# Platforms: install routes, limits and labels

Grades: **verified** means tested on the date and client given, **inferred** means it
follows from strong evidence but was not exercised directly, and **docs** means taken
from vendor documentation and untested. Re-check before you rely on any of them.

## Install routes for a public marketplace

| Client | How | Result | Grade |
|---|---|---|---|
| claude.ai (web) and Claude Desktop | Customize → Plugins → Add → Add marketplace → `kintopp/kintopp-plugins`, add the plugin, then connect its connector on the plugin's Connectors tab | Connector and skill, in ordinary chat. Works on a **free** account with no GitHub access, because the repo is public | verified 2026-11-21 |
| Claude Code | `claude plugin marketplace add kintopp/kintopp-plugins`, then `claude plugin install <plugin>@kintopp-plugins` | Skill and server | verified 2026-11-21, Claude Code 2.1.x |
| ChatGPT desktop app | Plugins → Add plugin marketplace, Source `kintopp/kintopp-plugins`, Git ref and Sparse paths empty, then install from the plugin browser | Works on a **free** account, but the plugin runs **only in Codex mode**, not in ordinary ChatGPT chat | verified 2026-11-21 |
| chatgpt.com (web) | none | It can't add a marketplace; it points to the desktop app. A free account has no Codex mode at all | verified 2026-11-21 |
| Codex CLI | `codex plugin marketplace add kintopp/kintopp-plugins`, then `codex plugin add <plugin>@kintopp-plugins` | Skill and server | verified 2026-11-21, codex-cli 0.167.0 |

So on ChatGPT, plugins are a desktop and Codex feature. For ordinary ChatGPT chat, the
route is a **custom connector** (Settings → Apps → developer mode, paste the server URL).
That is a different mechanism: it depends on the plan and gives the tools without the skill.

### What a README should say about each route

- **Viewers (MCP Apps).** It is untested whether a viewer renders when the connector
  comes from a plugin. For servers whose viewer matters, the README keeps the direct
  routes as the way to get it: the remote connector URL, a local stdio install, or a
  desktop extension (`.mcpb`). Offer the plugin as the way to get connector and skill
  together.
- **Free claude.ai accounts** allow one custom connector. It is untested whether a
  plugin's connector counts toward that limit, so don't promise that several plugins can
  be installed side by side on a free account.
- Say which half each route delivers: a connector alone has no skill, and an uploaded
  `.skill` alone has no tools.

## Labels (what users see)

| Item | Claude | ChatGPT | Grade |
|---|---|---|---|
| Marketplace | The **GitHub repo name** ("From kintopp-plugins"); Claude's catalog has no display field | The catalog's `name`, title-cased ("Kintopp Plugins"), or `interface.displayName` | verified 2026-11-21 |
| Plugin | `displayName` in the manifest | Before install: only the fields in the **marketplace entry** (`displayName`, `description`, `author`, `homepage`, `interface`) for a plugin hosted in another repo; after install: the manifest | verified 2026-11-21 |
| Connector | The server key, as written | The server key with its first letter capitalised; no display field exists; keys match `^[a-zA-Z0-9_:@/.-]+$` | verified 2026-11-21 |
| Skill | The skill's `name` (kebab-case) | `agents/openai.yaml` `interface.display_name`, otherwise the name title-cased ("Odeuropa Eokg") | verified 2026-11-21 |
| Skill page | Not checked | `display_name`, then `short_description`, then the SKILL.md body, with line breaks inside a paragraph kept as breaks | ChatGPT: verified 2026-11-21, desktop app |

The marketplace's `name` field and the repo name are both `kintopp-plugins` so that the
two platforms show the same label. Don't rename either one alone.

## Updates

- Install caches are keyed by plugin version, on both platforms (verified for Codex:
  `~/.codex/plugins/cache/<marketplace>/<plugin>/<version>/`). Without a version bump,
  existing installs keep the old files.
- Claude Code: background auto-update is off for a user-added marketplace until the user
  turns it on under **Marketplaces** in `/plugin`; the catalog has no field for it.
  Without it, users run `claude plugin marketplace update kintopp-plugins` or
  `claude plugin update <plugin>@kintopp-plugins` (docs, 2026-11-23).
- claude.ai and Desktop: users choose **Check for updates** on the marketplace, or turn on
  **Sync automatically**, which is offered for a marketplace added from github.com (docs,
  2026-11-23). Whether either picks up a bump without a reinstall is an open test.
- Codex and ChatGPT desktop: `codex plugin marketplace upgrade` refreshes the snapshot.
  The ChatGPT desktop app's own installs were not visible to the Codex CLI on 2026-11-21,
  so update those inside the app (inferred). An August note that the two "share one
  config" is out of date.
- A renamed connector key counts as a new connector: users must connect it again.
- A renamed plugin `name` breaks existing installs. Claude's catalog takes a top-level
  `renames` map (`"old-name": "new-name"`, or `null` for a removed plugin) that migrates
  Claude Code users; keep its entries once added (docs, 2026-11-23). No equivalent is
  known for the ChatGPT catalog, so treat plugin names as permanent.
- Changes to the marketplace entry alone (labels, description) show after a marketplace
  refresh, with no version bump (verified on Codex 2026-11-21).

## Not yet used (docs only)

- **Claude Team/Enterprise org sync** needs a **private** marketplace repo, the Claude
  GitHub App and an Owner. It accepts `./`, `github`, `url` and `git-subdir` sources; a
  private source must have the marketplace's owner. Its sync trigger is described
  inconsistently (every push vs. a version-bump pull request). The public repo can't serve
  it, so it would need a private copy.
- **ChatGPT Business/Enterprise workspace import** reads public or private GitHub
  marketplaces and syncs daily. Plugins that declare MCP servers "may get a Desktop only
  label".
- Claude allows up to 25 user-added marketplaces per account, and a plugin of up to 5,000
  files and 200 MB on claude.ai. Claude Code refuses marketplace names that imitate
  official Anthropic ones.
- Publishing in Anthropic's or OpenAI's public directory involves a review.
