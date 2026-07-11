# AGENTS.md

## Repo Reality
- This repo is flat and static: all deliverables are standalone HTML files in the root (`*.html`) plus one style/process guide (`skill_UI.md`).
- There is no build/test/lint/typecheck tooling in-repo (no `package.json`, Python project files, Makefile, or CI workflow config).
- `skill_UI.md` is the source of truth for the "Editorial Notebook" pattern used by most pages (tokens, layout, interaction patterns, and final checklist).
- `neural_networks_zero_to_expert.html` is an older one-off structure (`.wrap` flow, no `.topbar`/left TOC layout), so do not assume one shared page template.

## Editing Constraints That Matter Here
- Each HTML page inlines its own CSS and JS; there are no shared stylesheets/scripts. If you want consistency across pages, apply edits file-by-file.
- For notebook-style pages, keep anchors synchronized across three places: top nav links, left TOC links, and `<main><section id="...">` IDs.
- Scroll UI wiring depends on specific selectors/IDs (`#progress`, `#topbar`, `.reveal`, `.toc a`, `main section[id]`); breaking these silently kills progress/reveal/scrollspy behavior.
- Keep the reduced-motion guard at the end of the `<style>` block (`@media (prefers-reduced-motion: reduce)`), matching existing pages and `skill_UI.md` guidance.
- `self_attention.html` and `backpropagation.html` steppers are state-driven via `STEPS` arrays plus fixed DOM IDs; update step state and element IDs together.

## External Dependencies
- All pages load Google Fonts from `fonts.googleapis.com`/`fonts.gstatic.com`.
- All pages except `transformer-encoder-animated.html` load MathJax from jsDelivr.
- If a page injects/changes math after initial render, call `MathJax.typesetPromise()` (already done in `activation-functions-blog.html` and `normalization-guide.html`).

## Local Verification (Manual Only)
- Quick preview command from repo root: `python3 -m http.server 8000` then open `http://localhost:8000/<page>.html`.
- No automated test suite exists here, so manually verify each touched page: no console errors, nav/scroll behavior still works, interactive controls still run, and MathJax equations still render.
