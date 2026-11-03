# AuditAgent Claude Code Plugin

This is a plugin for [AuditAgent](https://auditagent.nethermind.io) that allows you to scan smart contracts.

## Install

### From the Claude plugin directory (recommended)

In Claude Code, open `/plugin` → **Discover**, search for **AuditAgent**, and install it.

### Local / development

From a clone of this repo:

```
claude plugin validate .
claude --plugin-dir .
```

### Authentication

Every connection authenticates in the browser. Claude Code should ask you to authenticate on first use; if it doesn't, use the `/mcp` command. In the browser, pick how to pay for scans:

- **With your AuditAgent account.** Scans spend your workspace credits.
- **Use without an account.** Each scan is paid by card, and nothing is stored in a workspace.

To switch later, ask Claude to log out, then authenticate the `auditagent` server again from `/mcp` and pick the other option.

Every scan starts with a quote: the price of each file in scope. Nothing runs and nothing is charged until you approve it. Each launch returns a result token for reading that scan; keep it, since a scan run without an account can only be read with it.

See the [MCP docs](https://docs.auditagent.nethermind.io/integrations/mcp-remote) for manual token setup.

## MCP Tools

| Tool | Purpose |
|------|---------|
| `auth_status` | Report whether this connection is logged in to an AuditAgent account |
| `logout` | End this connection's login so you can authenticate again and pick the other option |
| `info` | Show the active workspace name, subscription type, and spendable credits (account only) |
| `list_workspaces` | List workspaces available to the authenticated user (account only) |
| `switch_workspace` | Change the active workspace (account only) |
| `prepare_zip_upload` | Get a one-time S3 URL to upload a ZIP of the contracts and extra context to be scanned |
| `scan` | Quote a per-file price for an uploaded ZIP; after you approve, start the scan (account) or return a card payment link (no account), plus a result token |
| `scan_results` | Fetch a scan's progress or results with its result token |

## Skills

| Skill | Purpose |
|-------|---------|
| `aa-results-analysis` | Reviews, triages, and explains a finished scan's findings, invariants, and audit score |
| `aa-annotate` | Inserts `// @auditagent>` comments into the source from verified scan findings |
