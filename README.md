<p align="center">
  <br>
  <strong style="font-size:48px">pitchdeck</strong>
  <br>
  <em>Pitch what's real, not what you wish you'd built.</em>
  <br><br>
  <a href="https://github.com/susumutomita/pitchdeck/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License"></a>
  <a href="https://github.com/susumutomita/pitchdeck"><img src="https://img.shields.io/github/stars/susumutomita/pitchdeck?style=social" alt="Stars"></a>
</p>

<p align="center">
  A Claude Code skill that turns your repository into a keynote-quality pitch deck —<br>
  and refuses to overclaim. Every capability is verified against the code, not the README.
</p>

<p align="center">
  <a href="#why">Why</a> &middot;
  <a href="#install">Install</a> &middot;
  <a href="#what-happens">What happens</a> &middot;
  <a href="#the-honesty-rule">The honesty rule</a> &middot;
  <a href="#modes">Modes</a>
</p>

---

## Why pitchdeck exists <a name="why"></a>

**Ask an AI to make a pitch deck and you get a beautiful lie.**

It reads your README's ambitions and presents them as shipped. It marks the roadmap as done. It puts features
on slides that don't exist in the code. The deck looks great — until the first *"does that actually work?"*
and it collapses.

pitchdeck does the opposite. It encodes the skepticism of a senior investor / reviewer into the skill itself:
it doesn't trust your README or an ADR's status badge — it **verifies each claim against the actual code path**
(functions, tests, wiring) and then labels everything honestly:

```
実証済 (proven)   — tests pass / wired end-to-end / actually runs
実装中 (partial)  — code path incomplete — confirmed against the code, not a badge
構想   (planned)  — declared in docs / roadmap, no code yet
```

A pitch you can defend under questioning beats a pretty pitch that falls apart on contact.
**Honesty is the moat.**

> Real lesson that shaped this skill: an ADR badge read `Proposed` while the feature was already shipped — and
> the inverse happens just as often, a README claiming "supported" over a stub. Neither the badge nor the README
> is ground truth. **The code path is.**

## Install

```bash
npx skills add susumutomita/pitchdeck
```

Then in any project, open Claude Code and type:

```
/pitchdeck
```

That's it.

## What happens <a name="what-happens"></a>

1. **Scans** your repo — pulls positioning from README / docs in the team's own words (verbatim taglines)
2. **Counts what's real** — for every headline feature, checks tests / wiring / the actual function, and labels it 実証済 / 実装中 / 構想 (`N of M` when a number is claimed)
3. **Finds the moat & traction** — what's structurally hard to copy, plus `git log` velocity and shipped features (shipped > promised)
4. **Generates** a self-contained deck at `.pitchdeck/index.html` — offline-safe (no external assets), keyboard nav, progress bar, `Cmd-P` → per-slide PDF
5. **Opens** it in your browser

Re-run `/pitchdeck` and it rebuilds **everything from scratch** — no stale claims, no ghost features from a
renamed module.

## The honesty rule <a name="the-honesty-rule"></a>

This is the whole point. Most deck generators answer *"what would impress?"*. pitchdeck answers *"what can you
prove on stage?"*.

| Most AI deck-makers | pitchdeck |
| --- | --- |
| Reads the README's ambitions, presents them as done | Verifies the code path — function + test + wiring |
| Trusts an ADR / status badge | Treats the badge as a hint; confirms against code (badges go stale both ways) |
| Inflates the headline number | Counts the real one: `12 problems` → `4 of 12 deploy & score today` |
| Marks the roadmap complete | Labels it 構想 and puts it on the roadmap slide |
| One pretty slide of "features" | A dedicated **"what actually works"** slide, tagged 実証済 / 実装中 / 構想 |

Every run includes an honest-inventory slide. That slide is what makes investors, judges, and your own team
trust the rest of the deck.

## Modes <a name="modes"></a>

| Command | Audience | Emphasis |
| --- | --- | --- |
| `/pitchdeck` | Investors / community / users | Problem → solution → why now → demo → moat → **what works** → roadmap → CTA |
| `/pitchdeck --demoday` | Hackathon judges | 5-minute, demo-first, who/what/wow up front, one message per slide |
| `/pitchdeck --internal` | Team / stakeholders | No gloss — honest status, what works vs what's left, decisions to make, risks |

## Works with any stack

The skill reads source, tests, Git history, and docs — language-agnostic. Tested shape: TypeScript / Node,
Python, Go, Rust, AWS CDK / Terraform, and monorepos. If a brand exists in `landing/`, the deck reuses its
palette and fonts; otherwise it falls back to a clean default.

## Security

- The deck is built from your repo, so pitchdeck **redacts secrets** — `.env` values, API keys, tokens, private
  URLs, customer data are never embedded (config is quoted with values masked).
- Runs **locally**. Never auto-installs packages (`npx --yes` is avoided — supply-chain risk); analyzers are only
  invoked when already present (`command -v`).
- Output `.pitchdeck/` is gitignored by default. `--internal` decks carry honest internal status — confirm before
  publishing them anywhere public.

See [SECURITY.md](SECURITY.md).

## Output

Generated deck goes to `.pitchdeck/index.html`. Add `.pitchdeck/` to your `.gitignore`. Re-run = full rebuild.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- A modern browser (the deck is a single self-contained HTML file)

## License

MIT
