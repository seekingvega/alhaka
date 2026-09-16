# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

`alhaka` (Alpaca Hackathon Knowledge Agent) studies the ~430 public submissions to the
[Alpaca AI Trading Agents Hackathon](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon)
on lablab.ai and distills them into notes for building agentic trading prototypes. Our own
entry is PACA (`win-or-die/paca-position-aware-agentic-capital-allocator`), referenced as
"ours" in the pages and scripts. `main` is research tooling, a generated research page, and a
Quarto website. The repo is becoming a monorepo: PACA is to live on its own branch, and code
borrowed from other submissions gets modified and tested on further branches, so trading code
is never on `main`.

Three parts:

- `pages/` — generated research. `hackathon-submissions.qmd` is the category map of every
  submission (rendered by the skill below, do not hand-edit).
- `.claude/skills/hackathon-submissions/` — the pipeline that produces the category map
  (see below). The only code in the repo.
- Quarto website (`_quarto.yml`, `index.qmd`, `posts.qmd`, `about.qmd`, `posts/`) —
  `index.qmd` is a prose landing page, `posts.qmd` is the blog listing over flat
  `posts/*.qmd` files (hand-written follow-up studies such as
  `posts/market-structure-submissions.qmd`, which links to the category map). The navbar is
  Submissions · Posts · About · GitHub. `about.qmd` is still an untouched Quarto template
  placeholder (fake social links). Only `*.qmd` files render; markdown files never do.

## Commands

Quarto site (Quarto 1.6 is installed):

```bash
quarto preview          # live-reload dev server
quarto render           # builds to _site/ (gitignored, as is .quarto/)
```

Posts freeze computational output (`posts/_metadata.yml`), so a changed code cell needs
`quarto render posts/<name>.qmd` to refresh its cache.

Hackathon submissions pipeline (stdlib-only Python, no pyproject; run with `uv run python`):

```bash
SKILL=.claude/skills/hackathon-submissions
uv run python $SKILL/submissions.py fetch --event alpaca-ai-trading-agents-hackathon
uv run python $SKILL/submissions.py batch --event <slug> --top 10 --size 5 --out <scratch>/batches
uv run python $SKILL/submissions.py build --event <slug> --taxonomy T.json --assignments <dir> [--top N] [--ours <uid>]
```

There are no tests or linters.

## The hackathon-submissions pipeline

Read `SKILL.md` in the skill folder before running or changing it; it is the authoritative
runbook and its "Lessons from 2026" section records API quirks that cost real time. The
shape, which is what you need to understand edits across its five files:

1. `fetch` pages lablab's `/api/v4/submissions` newest-first and filters client-side (the
   server ignores every event filter), verifies the count against `live-stats`, and caches
   to `logs/lablab_<event>.json`.
2. `batch` splits the cache into `batch_NN.json` files in the scratchpad.
3. **Pass 1** — one `general-purpose` subagent per batch follows `pass1_propose.md` against
   `taxonomy_seed.json` and proposes categories. A human merges proposals into a
   `taxonomy.json` (judgment step; show the user).
4. **Pass 2** — one subagent per batch follows `pass2_assign.md` and assigns every project
   to exactly one category with a one-line "notable".
5. `build` merges assignments deterministically and renders `pages/hackathon-submissions.qmd`
   (YAML frontmatter with `toc: true`, then markdown using the `.notable`, `.ours` and `.count`
   helper classes). It refuses to write if any uid is missing, duplicated, or in an unknown
   category. Fix the assignment files rather than weakening that gate.

Invariants that are easy to break:

- Projects are keyed on `uid` = `team_slug/slug`, never bare `slug` (slugs repeat across teams).
- Subagents read only the `description` text. No WebFetch/WebSearch, no opening repos or demos.
- Categorize by primary trading approach, not asset class, tech stack, or "LLM proposes,
  Python gate disposes" (near-universal, so not a category). Tighten tie-break rules in
  `pass2_assign.md` before adding categories.
- In-page anchors follow pandoc's rule, which Quarto uses for heading ids (punctuation
  dropped, whitespace runs collapsed to one hyphen), so "a / b" → `a-b`; `anchor()` in
  `submissions.py` implements this and the category-definitions table depends on it. GitHub's
  `a--b` rule is wrong for the rendered site.
- The pipeline runs in two stages with a mandatory stop: test on the top 10, report, then
  wait for an explicit go-ahead before the full ~430-project run. Do not collapse them.
- Be gentle with lablab: about 30 sequential GETs total, no retry loops, no parallel fetches.
- The skill commits only the generated qmd and README at the end and never pushes.

The `logs/` cache is gitignored; regenerate it with `fetch` rather than committing it.

## Design tokens

The site's look is derived, not picked: `design.md` is the brief (keystone: one orange marker
on a quiet field) and `design.tokens.json` holds the values. `theme-light.scss` and
`theme-dark.scss` are generated from the JSON and wired in `_quarto.yml`; `site.scss` applies
the tokens to Quarto components and is hand-maintained. To change a colour or font, edit the
JSON, then:

```bash
uv run https://ohjho.github.io/dotfiles/scripts/design_tokens.py check design.tokens.json
uv run https://ohjho.github.io/dotfiles/scripts/design_tokens.py render md   design.tokens.json --into design.md
uv run https://ohjho.github.io/dotfiles/scripts/design_tokens.py render scss design.tokens.json --theme light -o theme-light.scss
uv run https://ohjho.github.io/dotfiles/scripts/design_tokens.py render scss design.tokens.json --theme dark  -o theme-dark.scss
```

Posts and the generated page should use the helper classes in `site.scss` (`.notable`,
`.count`, `.ours`, `.gain`, `.loss`) rather than inline colours, and never a title banner or a
second accent.

## Skills

`design-derivation`, `quarto-writeup`, and `surge-artifacts` under `.claude/skills/` are
symlinks into `../_GADA_experiments/dotfiles/` on the author's machine, not repo content.
They resolve only there; `quarto-writeup` is the intended path for turning research into
posts under `posts/`.
