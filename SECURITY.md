# Security

`pitchdeck` builds a pitch deck **from your repository**, so the primary risk is leaking something private into
a deck you then present or publish. The skill is designed to prevent that.

## Threat model

| Risk | Mitigation |
| --- | --- |
| Secrets leak into the deck | Before embedding any quoted config, the skill scans for and **redacts** `.env` values, API keys, tokens, credentials, private URLs, and customer data. Config is shown with values masked. |
| Internal status goes public | `--internal` decks contain honest, unvarnished status. They are gitignored by default and the skill confirms before any outward-facing publish. |
| Supply-chain (auto-install) | The skill **never auto-installs** packages. `npx --yes`-style auto-install is avoided. Optional analyzers are invoked only when already present (`command -v`); otherwise they are skipped and labeled "not measured". |
| Untrusted code execution | The skill reads and greps; it does not execute repository scripts to gather facts. |
| Output committed by accident | `.pitchdeck/` is added to `.gitignore`. |

## What the skill does NOT do

- It does not send your code to any third-party service. The analysis is done by Claude Code locally.
- It does not modify your source. It only writes to `.pitchdeck/`.
- It does not inflate claims: a feature is only labeled `実証済` when verified against the code path (function +
  test + wiring), never on the strength of a README line or an ADR badge alone.

## Reporting

Found a way the skill could leak a secret or overclaim in a way that matters? Open an issue at
<https://github.com/susumutomita/pitchdeck/issues>.
