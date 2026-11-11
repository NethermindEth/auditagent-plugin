# AuditAgent Plugin

[AuditAgent](https://auditagent.nethermind.io) is Nethermind's AI security auditor for smart contracts on EVM (Solidity), Starknet (Cairo) and Solana (Rust). This plugin lets Claude Code upload your code, run a scan and pull the findings back into the session, with skills that help Claude act on them. Sign in with your AuditAgent account, or pay per scan by card without one.

AuditAgent is automated analysis and is not a substitute for a professional security audit.

## Example prompts

- "Please audit my smart contracts using AuditAgent"
- "Do a security scan on the staking logic in my smart contracts"
- "Please help analyze my AuditAgent scan's report for scan_id: `<YOUR_SCAN_ID>`"

## Install

### Local / development

From a clone of this repo:

```
claude plugin validate .
claude --plugin-dir .
```

## Sign in and payment

Every connection authenticates in the browser. Claude Code should ask you to authenticate on first use; if it doesn't, use the `/mcp` command. In the browser, pick how to pay for scans:

- **With your AuditAgent account.** Log in, pick a default workspace, and connect. Scans spend that workspace's credits, you get an email when a scan finishes, and results appear in the AuditAgent app. You can ask Claude to switch workspaces at any time.
- **Use without an account.** No sign-up. Each scan is a one-off paid by card through Stripe (minimum $0.50 per scan), and nothing is stored in a workspace or emailed.

To switch later, ask Claude to log out, then authenticate the `auditagent` server again from `/mcp` and pick the other option.

### Price quote before every scan

Every scan starts with a quote: the price of each file in scope, in credits and US dollars, and the total. Nothing runs and nothing is charged until you approve it. You can drop files to lower the price, or decline.

- **With an account**, approving spends the credits and starts the scan right away. If the workspace is short on credits, nothing is spent and you can trim the scope or add credits.
- **Without an account**, approving returns a card payment link (valid for 30 minutes) and a result token. The scan starts once the payment is confirmed. Tell Claude when you have paid.

Each scan returns a **result token** for reading it. Keep it: a scan run without an account can only be read with its token, and AuditAgent cannot show it again. A scan that fails after it started is refunded automatically, to your workspace credits or to your card.

### What is sent to AuditAgent

To scan, Claude Code packages the files you select into a ZIP (contract sources, the `.md` docs you include, and the build files needed to compile, such as `foundry.toml` or `package.json`) and uploads it to AuditAgent. Claude also sends a short description of the project's intent, roles, and assumptions taken from your conversation and codebase, so the scanner can tell intended behaviour from bugs. Make sure Claude Code may only access the files and folders you want scanned before you start.

By using the plugin and AuditAgent, you agree to the AuditAgent [Terms of Use](https://auditagent.nethermind.io/terms-of-use) and [Privacy Policy](https://auditagent.nethermind.io/privacy-policy).

### Manual token setup

If your client can't authenticate in the browser, see the [MCP docs](https://docs.auditagent.nethermind.io/integrations/mcp-remote) to set a token by hand, either your own account token or the public no-account token.

## MCP Tools

| Tool | Purpose |
|------|---------|
| `auth_status` | Report whether this connection is logged in to an AuditAgent account or using it without one |
| `logout` | End this connection's login, with or without an account, so you can authenticate again and pick the other option |
| `info` | Show the active workspace name, subscription type, and spendable credits (account only) |
| `list_workspaces` | List workspaces available to the authenticated user (account only) |
| `switch_workspace` | Change the active workspace (account only) |
| `prepare_zip_upload` | Get a one-time upload URL for a ZIP of the contracts and docs to scan |
| `scan` | With `price_ack` false, quote a per-file price without starting anything. After you approve, with `price_ack` true: start the scan on workspace credits (account) or return a card payment link (no account). Both return a `scan_id` and a result token |
| `scan_results` | Fetch a scan's progress or finished report and invariants with its result token (an account connection may omit it for its own workspace's scans) |

## Skills

| Skill | Purpose |
|-------|---------|
| `aa-results-analysis` | Reviews, triages, and explains a finished scan's findings, invariants, and audit score |
| `aa-annotate` | Inserts `// @auditagent>` comments into the source from verified scan findings |

## Other clients

The plugin connects to AuditAgent's remote MCP server at `https://api.auditagent.nethermind.io/mcp` (streamable HTTP, OAuth 2.1 with dynamic client registration).

This repo is also a Cursor plugin (`.cursor-plugin/plugin.json` with the same skills and server) and holds the server's [MCP Registry](https://registry.modelcontextprotocol.io) entry (`server.json`). To connect any other MCP client, see the [MCP docs](https://docs.auditagent.nethermind.io/integrations/mcp-remote).

## Support

- Docs: [docs.auditagent.nethermind.io](https://docs.auditagent.nethermind.io/integrations/mcp-remote) ([llms.txt](https://docs.auditagent.nethermind.io/llms.txt))
- Email: [auditagent@nethermind.io](mailto:auditagent@nethermind.io)
- X: [@NethermindSec](https://x.com/NethermindSec)
- Telegram: [t.me/ai_smart_contract_auditor](https://t.me/ai_smart_contract_auditor)

## License

[MIT](LICENSE)
