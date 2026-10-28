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

This plugin requires you to be authenticated to AuditAgent. Claude code should automatically ask you for authentication, if it doesn't, you can manually authenticate by using the `/mcp` command.

## MCP Tools

| Tool | Purpose |
|------|---------|
| `info` | Show the active workspace name, subscription type, and spendable credits |
| `list_workspaces` | List workspaces available to the authenticated user |
| `switch_workspace` | Change the active workspace |
| `prepare_zip_upload` | Get an S3 URL to upload a ZIP of the contracts and extra context to be scanned |
| `scan` | Start a security scan on an uploaded ZIP |
| `scan_results` | Fetch the scan results |

## Skills

| Skill | Purpose |
|-------|---------|
| `aa-results-analysis` | Contains detailed instructions on how to utilize the scan results |
| `aa-annotate` | Inserts `// @auditagent>` comments into the source from verified scan findings |
