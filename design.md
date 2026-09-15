# Design: alhaka

Tokens for the whole Quarto site (`_quarto.yml`, `index.qmd`, `posts/`). Written 2026-09-15 by the design-derivation skill; the SCSS themes in `theme-light.scss` and `theme-dark.scss` are rendered from `design.tokens.json`, so edit the JSON and re-render rather than the SCSS.

## Derivation

| Input | Read | Produces |
|---|---|---|
| Content | A field map of ~430 trading-agent submissions distilled into prototype ideas: categories, counts, one "notable" line per project, and our own entry (PACA) marked among them. | The keystone: **one marker on a quiet field.** A single warm accent means "the distilled idea"; the field itself stays in ink and grey. Extras for `ours` and for `gain`/`loss` in P&L charts. |
| Audience | Agent and quant builders who read options strategies, lablab summaries and code. | Mono for structure (titles, headings, category labels, counts), sans for prose. Tight 4px radius, hairline rules, no shadows. |
| Goal | Skim a dense field and pull out what to build next. | Listing-first, scannable layout. Tables and counts do the work. Static, no interactivity. Measure 70ch, 8px spacing base. |
| Constraint | Quarto website on the cosmo base; light and dark; Google Fonts allowed; one token set for the whole site. | Both colour sets, `prefers-color-scheme` handled by Quarto's theme toggle, fonts via Google Fonts with system fallbacks. |

## Keystone

**The orange marker is the only thing that says "build this."** It carries links, category counts, and the tinted *Notable* callouts, and nothing decorative. It forbids: a coloured hero banner, coloured headings, gradient or shadow effects, and any second accent competing for attention. Teal (`ours`) is the one sanctioned exception, and it appears only on our own entry. Green and red exist only inside charts and tables as P&L.

## Tokens

Standard set: core roles, four keystone extras (`ours`, `ours-soft`, `gain`, `loss`), font roles, radius, spacing base, measure. Check result is recorded below the table.

<!-- tokens:start -->
| token | light | dark | from | note |
|---|---|---|---|---|
| bg | `#F7F6F2` | `#131412` | constraint | warm paper ground; the field stays quiet on it |
| surface | `#FFFFFF` | `#1C1D1A` | constraint | cards, listing tiles, table headers |
| ink | `#1C1B18` | `#ECE9E1` | audience |  |
| muted | `#625F57` | `#A49F93` | audience | team names, dates, vote counts |
| line | `#DDD9CF` | `#2E2F2A` | constraint | hairline rules and table borders |
| accent | `#C2410C` | `#F59E4B` | content | the marker: links, the distilled idea, category counts |
| accent-soft | `#FBE7D6` | `#3A2510` | content | tinted callouts for 'notable' lines |
| ours | `#0F766E` | `#4FC3B5` | content | marks our own entry (PACA) wherever it appears |
| ours-soft | `#D5F0EC` | `#0F3230` | content |  |
| gain | `#1F7A4D` | `#5CC98E` | content | P&L up, in charts and tables only |
| loss | `#B42318` | `#F27D6B` | content | P&L down, in charts and tables only |

| token | value | from | note |
|---|---|---|---|
| font-display | `"JetBrains Mono", ui-monospace, "SFMono-Regular", "Menlo", monospace` | audience | mono for structure: post titles, section headings, category labels, counts |
| font-body | `"Source Sans 3", system-ui, "-apple-system", "Segoe UI", sans-serif` | audience | sans for prose and the long project listings |
| font-mono | `"JetBrains Mono", ui-monospace, "SFMono-Regular", "Menlo", monospace` | audience | code cells, uids, tickers |
| radius-md | `4px` | audience | tight corners; notes, not a marketing site |
| space | `8px` | goal | dense enough to scan a 76-item category on one screen |
| measure | `70ch` | goal | listing lines and definitions wrap without becoming a wall |
<!-- tokens:end -->

## Layout & rhythm

- The home page is the Quarto `default` listing: title in mono, date and categories in muted, description in sans. No thumbnails larger than the text they illustrate.
- Posts: title block without a banner (the cosmo banner would be a second accent), 70ch measure, `h2` in mono with a hairline rule beneath, tables full-width with `line` borders and a `surface` header row.
- Density is high on purpose: an 8px base, 1.5 line-height on body, 1.25 on headings. A 76-item category should fit in two screens.
- Code cells use `mono` on `surface` with a `line` border, no rounded pill styling.

## Signature move

Category counts and the *Notable* lines are set in the accent, so scanning a post for orange finds the ideas worth prototyping. Everything else stays quiet: ink text, grey metadata, hairline rules, white cards on warm paper.

## Self-check

Re-derived for a general-audience newsletter about "AI trading bots": mono headings and 8px density would read as cold and cramped, so body would move to a serif at 62ch with more air, the counts would lose their accent and one editorial image per post would carry the hook. The aesthetic changes when the audience does, so the derivation is rooted in the brief.

Re-derived for an internal PACA design doc rather than a public blog: the same tokens would hold, but the `ours` teal would become the primary accent, since the reader is us.

## Handoff

Consumer: **quarto-writeup** for posts, **dataviz** for charts.

```bash
uv run https://ohjho.github.io/dotfiles/scripts/design_tokens.py check design.tokens.json
uv run https://ohjho.github.io/dotfiles/scripts/design_tokens.py render md   design.tokens.json --into design.md
uv run https://ohjho.github.io/dotfiles/scripts/design_tokens.py render scss design.tokens.json --theme light -o theme-light.scss
uv run https://ohjho.github.io/dotfiles/scripts/design_tokens.py render scss design.tokens.json --theme dark  -o theme-dark.scss
```

Charts: `accent` is the single-series colour, `gain`/`loss` the diverging pair, `ours` the highlight for PACA's own series. Run dataviz's contrast validator in both themes.
