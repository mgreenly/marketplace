# metaspot marketplace

A **private Claude plugin marketplace** that delivers the metaspot suite's
remote-HTTP MCP servers into Claude. This is a **technology demonstrator** — no
permanent users, it serves only `ai.metaspot.org`. The build is the experiment:
it confirms end to end that a GitHub-hosted plugin can deliver remote-HTTP MCP
servers into **Claude Cowork** (desktop) and stay auto-synced.

## Layout

```
.claude-plugin/marketplace.json   marketplace "metaspot", one plugin entry
metaspot-suite/                   the plugin "metaspot-suite"
  .claude-plugin/plugin.json      plugin manifest
  .mcp.json                       remote-HTTP MCP servers (auto-discovered)
  commands/doctor.md              /doctor slash command (Claude Code CLI)
  skills/doctor/SKILL.md          doctor skill (reliable surface in Cowork)
```

Install string: **`metaspot-suite@metaspot`**.

## What it ships

Two remote-HTTP MCP servers, declared in `metaspot-suite/.mcp.json`. A plugin
auto-discovers a `.mcp.json` at its root, so no pointer in `plugin.json` is
needed:

| server | url |
|---|---|
| `crm`    | `https://ai.metaspot.org/srv/crm/mcp` |
| `ledger` | `https://ai.metaspot.org/srv/ledger/mcp` |

Cowork reaches these from Anthropic's cloud, so the endpoints must be
public-internet reachable — `ai.metaspot.org` is, so this works.

## The doctor check (the proof)

The demonstrator's success criterion is the doctor check passing **inside
Cowork**. It calls each service's no-side-effect `whoami` MCP tool
(`crm_whoami`, `ledger_whoami`) and reports pass/fail per service. A green run
proves the whole chain: `plugin -> Cowork -> connector -> dashboard OAuth ->
service`.

It ships in **two forms** on purpose:

- **`/doctor` slash command** (`commands/doctor.md`) — for Claude Code CLI.
- **doctor skill** (`skills/doctor/SKILL.md`) — for Claude Cowork.

Why both: whether plugin slash commands surface in Cowork the same way they do
in the CLI is not confirmed in the docs (as of 2026-06-01). Skills are the
confirmed-supported action surface in Cowork, so shipping the doctor as a skill
guarantees the proof is reachable there; the slash command is the CLI
convenience. If a future check confirms slash commands surface in Cowork, the
skill becomes redundant and can be dropped.

## Using it from Claude Code (the free bonus)

```
claude plugin marketplace add mgreenly/marketplace
claude plugin install metaspot-suite@metaspot
```

Then `/doctor` (or ask Claude to "check the metaspot suite").

## Using it from Claude Cowork (the primary target)

These steps are a **human, UI-driven** workflow and cannot be automated:

1. **Install the Claude GitHub App** on the `mgreenly/marketplace` repo.
2. On a **Team/Enterprise** Claude account (Owner role; Cowork + Skills both
   enabled), go to **Organization settings -> Plugins (Libraries)** and connect
   the private `mgreenly/marketplace` repo as a plugin marketplace.
3. Enable the **`metaspot-suite`** plugin.
4. Toggle **"Sync automatically"** ON, tracking the **`main`** branch (an
   unpinned ref) — that is what keeps installed users in sync as `main` moves.
5. In **Cowork desktop**, confirm the crm and ledger tools appear, then run the
   doctor check and confirm both whoami calls succeed. **That success is the
   demonstrator's goal met.**

## Status / remaining manual steps

Built and committed in this repo:

- [x] `marketplace.json`, `plugin.json`, `.mcp.json`
- [x] `/doctor` slash command + doctor skill
- [x] git repo initialized and pushed to `mgreenly/marketplace`

Cannot be automated (require the Claude/GitHub web UIs — do these by hand):

- [ ] Install the Claude GitHub App on the repo.
- [ ] Connect the repo + enable `metaspot-suite` + turn on autosync in the
      Claude org settings.
- [ ] Run the doctor check in Cowork and confirm both whoami calls pass.
