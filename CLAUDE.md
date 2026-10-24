# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A flat, static collection of standalone HTML "explainer" blog posts about machine learning
(activation functions, backpropagation, self-attention, transformers, normalization, positional
encoding, model-evaluation metrics, feed-forward nets). Every page is a single self-contained
`.html` file in the repo root with its **own inlined CSS and JS** — there are no shared
stylesheets or scripts, and no build/test/lint/package tooling of any kind.

## Two source-of-truth docs — read them before editing

- **`skill_UI.md`** — the full "Editorial Notebook" design recipe (tokens, layout, interaction
  patterns, the stepped-walkthrough widget engine, final checklist). This is the spec that most
  pages implement. Consult it before adding or restyling any page so the look stays consistent.
- **`AGENTS.md`** — concise repo-specific constraints and gotchas. Overlaps with this file; keep
  the two in sync if you change one.

## Preview / verify (manual only — no test suite exists)

```bash
python3 -m http.server 8000      # from repo root, then open http://localhost:8000/<page>.html
```

After touching a page, manually check: no console errors, nav/scroll behavior works, interactive
widgets still run, and MathJax equations still render.

## Architecture notes that span multiple files

- **`index.html` is a hand-maintained hub** linking to the individual posts. Adding a new page
  does *not* auto-list it — update `index.html`'s card grid too. (It currently omits some pages,
  e.g. `model-evaluation-metrics-blog.html`, so don't assume the list is complete.)
- **Most pages share the "notebook" template** (`.topbar` + left `.toc` + `main`), but
  `neural_networks_zero_to_expert.html` is an older one-off (`.wrap` flow, no left TOC). Do not
  assume a single shared template across all pages.
- **Consistency is applied file-by-file.** Because CSS/JS is inlined per page, a cross-cutting
  change means editing each file individually.

## Editing constraints that break things silently

- **Keep the three navigation label sets in sync** on notebook pages: top nav links, left TOC
  links, and `<main><section id="...">` IDs must all agree.
- **Scroll UI wiring depends on exact selectors/IDs:** `#progress`, `#topbar`, `.reveal`,
  `.toc a`, `main section[id]`. Renaming any of these silently kills the progress bar, scroll
  reveal, or scrollspy.
- **Stepped-walkthrough widgets** (notably inclaude --resume 9461ed95-23f3-4f77-abba-a425162c4712 `self_attention.html` and `backpropagation.html`)
  are driven by a `STEPS` array plus fixed DOM IDs; each step declares the *full* visual state,
  not a diff. Update the step state and the element IDs together.
- **Keep the `@media (prefers-reduced-motion: reduce)` guard as the last rule** in each page's
  `<style>` block.

## External dependencies (the only ones)

- Google Fonts (`fonts.googleapis.com` / `fonts.gstatic.com`) on every page.
- MathJax from jsDelivr on every page **except** `transformer-encoder-animated.html`. If you
  inject or change math after initial render, call `MathJax.typesetPromise()` (see
  `activation-functions-blog.html` and `normalization-guide.html` for the pattern).

claude --resume 9461ed95-23f3-4f77-abba-a425162c4712