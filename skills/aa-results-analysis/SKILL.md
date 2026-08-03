---
name: aa-results-analysis
description: Contains detailed instructions on how to utilize the scan results from AuditAgent's security scan of smart contracts
---

# AuditAgent scan results

AuditAgent utilizes AI to perform its security scans and is provided as a "pre-audit" service. Therefore you must keep in mind that the `scan_results` output you get from it, is AI-assisted — it is not a formal audit and not a confirmed exploit list.

This file contains detailed instructions on how you can best utilize the scan results.

## When to use this skill

Once the results from a scan are available and you want to:

- Review or explain results
- Explain how to apply fixes
- Turn invariants into tests
- Separate what AuditAgent reported from what the code actually does

### Results structure

The result structure is as follows:

| Field | Use |
|-------|-----|
| `scan_id` | UUID for this scan |
| `scan_number` | Human-readable scan index in the workspace |
| `scan_type` | Scan quality display name (e.g. `"Developer Scan"`) |
| `summary` | AI-generated summary of the project |
| `total_vulnerabilities` | The total number of vulnerabilities found in the scan |
| `total_lines_of_code` | The total number of lines of code analyzed |
| `contract_files` | Files included in the scan |
| `findings` | Reported issues |
| `date` | Completion date string when available |
| `duration_seconds` | Wall-clock duration of the scan |
| `audit_score` | 0–100 directional score (see below) |
| `source_name` | ZIP upload name (typical for MCP scans) |
| `repository_name` | Repo / project name when present (if available) |
| `organization` / `branch_name` / `commit_hash` | GitHub-oriented metadata; empty for ZIP/MCP scans |
| `chain` / `ethereum_address` | This is for on-chain scans; empty for ZIP/MCP scans |
| `invariants` | Properties that were detected from the code or docs that can be used to write tests |

## Findings

The findings are the issues that were detected in the codebase. They are structured as follows (they include more fields than the ones listed here, but you can ignore those):

- `issue` — short title
- `description` — detailed explanation of the issue
- `severity` — The severity of the issue: High | Medium | Low | Info | Best Practices
- `contracts` — project-relative file paths (not bare contract names) the issue affects
- `category` — vulnerability class: reentrancy, access_control, integer_overflow_underflow, denial_of_service, unchecked_call, front_running, config_dependent, business_logic, precision_loss, centralization_risk, other
- `index` — serves as an ID for the issue, it doesn't necessarily match the order of the finding in the array it's in
- `excluded` — matched a user-defined exclusion pattern (this means the user previously saw a similar issue and decided it should be ignored / excluded and is a non-issue)

### Triage rules

1. **Filter excluded** — Skip `excluded: true` unless the user asks for dismissed items.
2. **Sort by severity** — High → Medium → Low → Info → Best Practices.
3. **Verify in code** — Open the `contracts` paths and read the code before recommending a fix. Results are hints, not proof.
4. **Description is diagnostic** — It explains the issue; it does not prescribe the fix. Propose fixes only after verification.
5. **No exploit claims** — Do not call a finding a confirmed exploit or guaranteed vulnerability.

## Invariants

An **invariant** is a condition that should hold they are generated from the code and the docs. If violated under realistic execution, there is a logic flaw. Treat AuditAgent invariants as reusable security properties for your test suite — **not** as filed bugs.

| Field | Meaning |
|-------|---------|
| `description` | Plain-English property |
| `condition` | Checkable condition — candidate for fuzzing, invariant tests, or formal verification |
| `function` | Function scope, or `"Contract-wide"` |
| `path` | File path, or `"Protocol-wide"` |

### How to use invariants

1. **Test backlog, not bug list** — Encode and fuzz them; do not treat them as filed vulnerabilities.
2. **Match tooling to the stack** — Solidity: Foundry `invariant_*` / fuzz tests, Echidna, or assertions. Cairo / Rust (Starknet / Solana): use the project's existing test / fuzz framework rather than assuming Foundry.
3. **Cross-check findings** — When a High/Medium finding overlaps an invariant `condition`, use both for the test and the fix.
4. **No finding ≠ unimportant** — Invariants without a matching finding still matter: properties the scanner believes should hold.
5. **Scope markers** — `"Contract-wide"` and `"Protocol-wide"` are broader than one function; design tests accordingly.
6. **AI-generated** — Can be vague, wrong, or mis-scoped. Refine with the developer before treating as spec.

## audit_score

- Range: **0–100** (higher is better). May be null (e.g. no code-line metrics).
- Weighted High / Medium / Low findings (weights 10 / 4 / 1), adjusted by detector confidence and issue density vs code size, minus a small comment-ratio quality penalty. Info and Best Practices have weight **0**.
- Scoring does not filter `excluded: true`, and excluding later does not recalculate the stored score.
- Experimental — false positives can move it. Not a ship/no-ship grade and not a substitute for human review.

## summary

AI-generated orientation for the codebase. Useful at the start of a review; verify claims against the code.

## Presenting results to the developer

**Structure**

1. Brief scan metadata (`scan_id`, `scan_type`, duration, LOC; `audit_score` as context only).
2. Work High → Medium first: for each, verify in code, then state what you checked and what you saw.
3. Two lists:
   - **Findings** — severity, category, paths, and your code-level assessment (confirmed concern / false positive / needs more context).
   - **Invariants to encode as tests** — from `invariants`, with a concrete test approach for the project's stack.
4. Mention excluded findings only if the user cares about dismissed items.

**Tone**

- Explicit: **AI-assisted scanning**, not a professional security audit.
- Prefer "reported concern", "potential issue", "worth verifying" — not "confirmed vulnerability".
- Concrete next steps: open file X, inspect function Y, add a test for condition Z.

**Example next steps**

- "Open `contracts/Vault.sol` at the `withdraw` path in finding #3; check external call order against the reported reentrancy pattern."
- "Add an invariant test from this `condition`: total supply equals sum of balances after any `deposit`/`withdraw` sequence."
- "After patching High findings, start a new scan and compare — the previous `scan_id` does not update in place."
