---
name: aa-annotate
description: Mark verified AuditAgent findings in the source with standalone // @auditagent> comments. Use when the user asks to annotate, comment, or mark the codebase as @auditagent from scan results.
---

# Annotate the code as @auditagent

Place AuditAgent findings into the source as short comments. Do this only when the user asks. A scan result is a hint. Confirm the line in the file before you write anything.

## When to use this skill

The user wants the scan marked in the code, for example "add the AuditAgent comments" or "mark these findings as @auditagent".

Do not run this while a scan is unfinished. Do not run it just because `scan_results` returned.

## Get the results

Call `scan_results`.

- Logged in: `scan_results(scan_id)`.
- Logged out: `scan_results(scan_id, result_token)`.

Skip findings with `excluded: true` unless the user asks for dismissed items. Work High, then Medium, then Low. Add Info and Best Practices only when the user asks for every finding.

## Where the comment goes

Each finding's `description` may quote source inside a code fence. That quote can already contain one line:

```solidity
// @auditagent> call before state update
balances[msg.sender] -= amount;
```

Put that same comment in the real file, on its own line, immediately above the line it describes. Match that line's indentation.

Rules:

- The text after `// @auditagent>` is 3 or 4 words. Reuse the words from the report when they are present. Otherwise take them from the finding `issue`.
- One comment per location. If that line already has an `// @auditagent>` comment directly above it, leave it.
- Solidity, Cairo, and Rust all use `//`. For another language, use that language's line comment and keep the marker `@auditagent>`.
- Do not change the code. Do not add a comment on a nearby line when the quoted line is not in the file. Say which finding you skipped.
- Open every path in `contracts` and check the quote against the current source. If the code does not match the finding, do not comment it. Tell the user it looks like a false positive or that the file has moved.

## After you edit

List the files you changed and the findings you skipped. Then tell the user: "The official AuditAgent report has been generated separately and remains the authoritative output of the scan."
