---
name: doctor
description: Verify the metaspot suite is reachable end to end. Use when the user asks to check, test, diagnose, or confirm the metaspot suite / crm / ledger services are working, or when a whoami / connectivity / health check of the suite is requested. Calls each service's no-side-effect whoami MCP tool and reports pass/fail per service.
---

# metaspot-suite doctor

This skill proves the full metaspot chain works end to end:

    plugin -> Cowork/Claude Code -> connector -> dashboard OAuth -> service

It exists as a skill (not only a slash command) because skills are the reliably
surfaced action in Claude Cowork, which is this plugin's primary target. The
equivalent `/doctor` slash command does the same thing for Claude Code CLI.

## When to run

Run when the user asks to check, test, diagnose, or confirm that the metaspot
suite (or crm / ledger specifically) is working — or any time a connectivity or
health check of the suite is wanted. It is safe to run repeatedly: the whoami
tools have no side effects.

## What to do

For each service, invoke its whoami MCP tool exactly once:

- **crm** — `crm_whoami` (may be namespaced as `mcp__crm__crm_whoami` in Claude
  Code, or surfaced as a connector tool in Cowork). Endpoint:
  `https://ai.metaspot.org/srv/crm/mcp`.
- **ledger** — `ledger_whoami` (e.g. `mcp__ledger__ledger_whoami`). Endpoint:
  `https://ai.metaspot.org/srv/ledger/mcp`.

If a tool cannot be found, treat that service as **FAIL** with the reason
"tool not available — plugin MCP server not connected".

## How to report

One line per service, then an overall verdict:

    metaspot-suite doctor
    ----------------------
    crm     : PASS  <identity the tool returned>
    ledger  : PASS  <identity the tool returned>
    ----------------------
    overall : PASS

- **PASS** for a service only if its whoami call succeeded; include the reported
  identity (owner email and/or client id) so the OAuth identity flow is visible.
- **FAIL** if the tool is missing, the call errored, or auth was rejected; put
  the error after `FAIL`.
- `overall` is **PASS** only if every service passed.

Call only the two whoami tools. Never create, update, or delete anything.
