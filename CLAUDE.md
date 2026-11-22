# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

The public `kintopp/kintopp-plugins` marketplace for Claude and ChatGPT/Codex. It holds no plugin code: each plugin lives under `plugin/<name>/` in its own sibling server repo (e.g. `kintopp/odeuropa-eokg-mcp`), and this repo only lists them. There is no build, test or lint step.

- `.claude-plugin/marketplace.json`: Claude's catalog.
- `.agents/plugins/marketplace.json`: the same catalog in the ChatGPT/Codex format (adds `interface`, `policy`, a different `category`).
- `README.md`: the plugin table and the install steps for each platform.
- `skills/kintopp-plugin-packaging/`: a skill for agents that package a server repo as a plugin. It is not a plugin and is not in either catalog. It is symlinked into `~/.agents/skills/` and `~/.claude/skills/`, so edits here are live in every session at once.

## Keeping the catalogs in step

Every change to one catalog needs the matching change in the other: the same marketplace `name`, the same plugin names, descriptions, display names and sources. Also update the README table row and the plugin's `claude plugin install` line. Templates for new entries are in `skills/kintopp-plugin-packaging/references/files.md` § Catalog entries.

- The source `path` is spelled `plugin/<x>` in Claude's catalog and `./plugin/<x>` in the ChatGPT one. Both spellings were verified, so keep them.
- Use only `git-subdir` sources. Claude's `github` source type has no OpenAI equivalent.
- The server repo has to be pushed first, so the `git-subdir` path exists on `main`, then this repo. The user decides when to push.

## Verifying

```bash
claude plugin validate .                     # validates .claude-plugin/marketplace.json only
jq . .agents/plugins/marketplace.json        # no validator exists; check it parses and matches
claude plugin marketplace update kintopp-plugins   # after a push
codex plugin marketplace upgrade                   # after a push
```

After a push, check that each plugin is listed exactly once. For the full install ladder and the probe-plugin method, see `skills/kintopp-plugin-packaging/references/verify.md`.

## Conventions

- Commit catalog changes separately from changes to the packaging skill. Commit subjects are short and imperative (`List the <plugin> plugin`).
- This repo is public: no home paths, personal emails, private marketplaces or account plans.
- When writing about platform behaviour in the README or the skill, say whether each claim was verified (with the date and client version), inferred, or taken from docs only. Leave untested behaviour out of the README.
