---
description: Check the metaspot suite end to end by calling each service's no-side-effect whoami MCP tool and reporting pass/fail per service.
---

You are running the metaspot-suite **doctor** check. Its purpose is to prove the
full chain works end to end:

    plugin -> Cowork/Claude Code -> connector -> dashboard OAuth -> service

The two services each expose a no-side-effect `whoami` MCP tool. Calling them
exercises authentication and routing without changing any data, so this check is
safe to run any time.

## What to do

For each of the two services below, invoke its whoami tool exactly once:

- **crm** — call the `crm_whoami` tool (it may be namespaced, e.g.
  `mcp__crm__crm_whoami` in Claude Code, or surfaced as a connector tool in
  Cowork). It maps to `https://ai.metaspot.org/srv/crm/mcp`.
- **ledger** — call the `ledger_whoami` tool (e.g. `mcp__ledger__ledger_whoami`).
  It maps to `https://ai.metaspot.org/srv/ledger/mcp`.

If you cannot find one of these tools at all, treat that service as **FAIL** with
the reason "tool not available — plugin MCP server not connected".

## How to report

Print one line per service, then an overall verdict. Use this exact shape:

    metaspot-suite doctor
    ----------------------
    crm     : PASS  <owner email / client id the tool returned>
    ledger  : PASS  <owner email / client id the tool returned>
    ----------------------
    overall : PASS

Rules:

- A service is **PASS** only if its whoami call returned successfully. Include
  the identity it reported (owner email and/or client id) so the operator can
  confirm the OAuth identity flowed through.
- A service is **FAIL** if the tool is missing, the call errored, or it returned
  an auth/permission error. Put the error message after `FAIL`.
- `overall` is **PASS** only if every service passed; otherwise **FAIL**.

Do not call any tool other than the two whoami tools. Do not create, update, or
delete anything.
