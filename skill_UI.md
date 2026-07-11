# The "Editorial Notebook" Blog — Build Guide

A reusable recipe for the warm, paper-textured, single-page explainer style used in the
backpropagation, feed-forward, and self-attention pages. Follow the steps in order and you
will reproduce the same look, feel, and interactions every time.

This guide is **self-contained**: copy the snippets as-is. Everything is vanilla HTML + CSS +
JS plus two CDN includes (Google Fonts and MathJax). No build tools, no frameworks.

---

## 0. The 10-step build order (the "standard")

Build every page in this exact sequence — it keeps the work predictable.

1. **Page skeleton** — `<!DOCTYPE html>`, `<head>` with fonts + MathJax, `<body>`.
2. **Design tokens** — paste the `:root` CSS variables (Section 3). Never hard-code a colour again.
3. **Base typography & layout** — body, headings, links, the `.layout` grid (Section 4).
4. **Progress bar + sticky top bar** (Section 7.1).
5. **Hero** — eyebrow, title with one italic accent word, lead, two buttons, a visual on the right (Section 5).
6. **Contents rail (TOC)** — sticky left column with numbered links (Section 6).
7. **Sections** — each with a mono section-number, an `<h2>`, prose, and at most one feature (figure / table / callout / code).
8. **Figures & diagrams** — inline SVG in a white `.fig` card with a mono caption (Section 8).
9. **Interactive widget(s)** — the dark stepped walkthrough and/or the light tabbed one (Section 9).
10. **Footer + scripts** — footer, then the JS block: progress, reveal, scrollspy, widget logic (Section 7).

> Rule of thumb: **one idea per section, one feature per section.** Prose carries the
> explanation; the feature (a diagram, a table, a code block, or an animation) earns its place
> only when it shows something words can't.

---

## 1. What the theme *is* (the feel)

- **Editorial / "notebook" aesthetic:** warm off-white "paper" with a faint grain, like a
  printed science notebook — not a typical dark dashboard.
- **Serif-led:** a characterful display serif (Fraunces) for headings, a readable body serif
  (Spectral), and a mono (JetBrains Mono) for labels, code, and numbers.
- **Calm palette, two accents:** deep teal is the "ink"; warm amber and gold are the only
  loud colours, used sparingly for emphasis and the "active" state.
- **Soft, layered surfaces:** white cards with gentle shadows and ~18px rounded corners float
  on the paper.
- **Motion is quiet:** content fades up on scroll; one or two interactive widgets let the
  reader *step through* a process. Nothing autoplays in your face; everything respects
  reduced-motion.

---

## 2. Fonts

Load three families from Google Fonts in `<head>`:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,400;9..144,500;9..144,600;9..144,700&family=Spectral:ital,wght@0,400;0,500;0,600;1,400&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
```

| Role | Family | Used for |
|------|--------|----------|
| Display | **Fraunces** (serif) | `h1`–`h4`, hero title, figure-less headings |
| Body | **Spectral** (serif) | paragraphs, lead text |
| Mono | **JetBrains Mono** | section numbers, TOC, code, captions, labels, numbers |

The hero's one-word emphasis is Fraunces *italic* in amber (e.g. *Attention*, *propagation*).

---

## 3. Colour system (copy the tokens verbatim)

Paste this `:root` block first. Every other rule references these variables.

```css
:root{
  /* surfaces */
  --paper:#F6F1E7;     /* page background (warm off-white) */
  --paper2:#FBF7EF;    /* slightly lighter inset / code-adjacent panels */
  --card:#FFFFFF;      /* cards, figures */

  /* text */
  --ink:#22282A;       /* primary text + headings on light */
  --ink-soft:#4C5658;  /* body paragraphs */
  --muted:#7E8888;     /* captions, secondary labels */
  --line:#E4DBCB;      /* hairline borders, dividers */

  /* primary (the "ink" accent) */
  --teal:#0E5C58;
  --teal-deep:#093F3C; /* heading colour, deep panels */
  --teal-soft:#E3EEEC; /* tinted callout background */

  /* warm accents (use sparingly) */
  --amber:#D9663B;     /* emphasis, active/hot state, italic hero word */
  --amber-soft:#F7E4D8;
  --gold:#C9A24B;      /* highlights, "peak" values, primary widget buttons */

  /* extra hues for diagrams / multi-item legends only */
  --violet:#6C5CE7;
  --blue:#2E86DE;
  --green:#2E9E5B;     /* also "positive" in diagrams; pair with amber as "negative" */

  /* depth */
  --shadow:0 1px 2px rgba(20,40,38,.04),0 8px 30px -12px rgba(20,40,38,.18);
  --shadow-lg:0 30px 60px -24px rgba(14,92,88,.30);

  /* geometry */
  --radius:18px;
  --maxw:1180px;

  /* fonts */
  --mono:'JetBrains Mono',monospace;
  --disp:'Fraunces',serif;
  --body:'Spectral',Georgia,serif;
}
```

### How to *use* the colours (the rules that keep it consistent)

- **Background** is always `--paper`; cards/figures are `--card` (white) or `--paper2` (inset).
- **Headings** are `--teal-deep`; **body** is `--ink-soft`; **captions/labels** are `--muted`.
- **Borders** are always `--line` (never pure grey/black).
- **Teal = structure** (headings, primary buttons, diagram "ink", info callouts).
- **Amber = attention** (the one hero word, active nav, "hot"/changed values, warning callouts).
- **Gold = highlight** (the single most important value in a chart, the primary action inside a
  dark widget, progress accents).
- **Violet/blue/green** appear **only inside diagrams** (e.g. input/hidden/output node colours,
  positive/negative edges). Don't use them for page chrome.

### Dark code & dark widgets

Code blocks and the stepped "demo" widget invert to a deep teal so they read as a different
surface:

```css
--code-bg:#0E2A28;  --code-border:#15403c;  --code-text:#CFE9E4;
/* syntax: keyword #F2A0C0 · function #7FD1E8 · string #E7E08B
   · number #F2C879 · comment #6f9a94 · operator #E58B5F */
/* dark-widget gradient: linear-gradient(160deg,#0E5C58,#093F3C) */
```

---

## 4. Base typography & layout

```css
*{box-sizing:border-box}
html{scroll-behavior:smooth;scroll-padding-top:90px} /* so anchors clear the sticky bar */
body{
  margin:0;background:var(--paper);color:var(--ink);
  font-family:var(--body);font-size:18px;line-height:1.72;
  -webkit-font-smoothing:antialiased;
  /* two faint corner glows */
  background-image:radial-gradient(circle at 12% -10%, #FCEBDD55, transparent 40%),
                   radial-gradient(circle at 100% 0%, #DCEBE955, transparent 45%);
  background-attachment:fixed;
}
/* the paper grain — a tiny tiled SVG noise at very low opacity */
body::before{content:"";position:fixed;inset:0;pointer-events:none;z-index:1;opacity:.5;
  background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='2'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.03'/%3E%3C/svg%3E");}

h1,h2,h3,h4{font-family:var(--disp);font-weight:600;line-height:1.12;color:var(--teal-deep);letter-spacing:-.01em}
a{color:var(--teal);text-decoration:none}
code,kbd,.mono{font-family:var(--mono)}

/* two-column reading layout: sticky TOC + content. Collapses to one column on small screens. */
.layout{position:relative;z-index:2;max-width:var(--maxw);margin:0 auto;
  padding:20px clamp(20px,5vw,48px) 80px;display:grid;grid-template-columns:240px 1fr;gap:48px;align-items:start}
@media(max-width:1000px){.layout{grid-template-columns:1fr}.toc{display:none}}

main{min-width:0}
section{margin-bottom:60px;scroll-margin-top:90px}
section h2{font-size:clamp(28px,4vw,40px);margin:6px 0 20px}
section h3{font-size:23px;margin:34px 0 12px;color:var(--teal)}
p{margin:0 0 18px;color:var(--ink-soft)}
.lead-in{font-size:20px;color:var(--ink-soft)}        /* first paragraph of a section */
```

**Key feel-setters:** body `font-size:18px` + `line-height:1.72`, fluid headings via
`clamp()`, and `z-index:2` on real content so it sits above the `::before` grain (`z-index:1`).

---

## 5. The hero

Structure: a small mono **eyebrow**, a big Fraunces **title** with exactly one italic amber
word, a **lead** sentence, two **buttons** (solid + ghost), and a **visual** on the right.

```html
<header class="hero">
  <div>
    <div class="eyebrow reveal">SECTION · LABEL · IN CAPS</div>
    <h1 class="reveal">Plain words, <span class="em">one</span> accent.</h1>
    <p class="lead reveal">One or two sentences on what the reader will get.</p>
    <div class="cta reveal">
      <a class="btn" href="#first">Start reading</a>
      <a class="btn ghost" href="#demo">Try the live demo</a>
    </div>
  </div>
  <div class="hero-fig reveal"><!-- inline SVG visual --></div>
</header>
```

```css
.hero{max-width:var(--maxw);margin:0 auto;padding:140px clamp(20px,5vw,48px) 30px;
  display:grid;grid-template-columns:1.05fr .95fr;gap:40px;align-items:center;position:relative;z-index:2}
@media(max-width:900px){.hero{grid-template-columns:1fr;padding-top:120px}}
.eyebrow{font-family:var(--mono);font-size:13px;letter-spacing:.22em;text-transform:uppercase;color:var(--amber);margin-bottom:18px}
.hero h1{font-size:clamp(38px,6vw,72px);font-weight:600;margin:0 0 18px}
.hero h1 .em{font-style:italic;color:var(--amber)}
.hero p.lead{font-size:20px;color:var(--ink-soft);max-width:34ch;margin:0 0 26px}
.cta{display:flex;gap:14px;flex-wrap:wrap}
.btn{font-family:var(--mono);font-size:13px;letter-spacing:.04em;padding:13px 22px;border-radius:999px;
  border:1px solid var(--teal);background:var(--teal);color:#fff;cursor:pointer;transition:transform .2s,box-shadow .2s,background .2s}
.btn:hover{transform:translateY(-2px);box-shadow:var(--shadow-lg)}
.btn.ghost{background:transparent;color:var(--teal)}
.btn.ghost:hover{background:var(--teal-soft)}
```

---

## 6. Indexing: contents rail, top nav, numbering, scrollspy

The page is navigable three ways. **Keep all three label sets in sync.**

### 6.1 Section numbering convention

- Each `<section>` opens with a mono number: `<div class="sec-no">03</div>` then the `<h2>`.
- Numbers are **two-digit, zero-padded** (`01`, `02` …). Insertions use a star suffix to avoid
  renumbering everything, e.g. `07★`.
- Sub-examples inside a section use `h3` titled `6.1 — …`, `6.2 — …`.

```css
.sec-no{font-family:var(--mono);font-size:13px;color:var(--amber);letter-spacing:.1em}
```

### 6.2 Sticky contents rail (left)

```html
<aside class="toc">
  <h5>Contents</h5>
  <a href="#idea">1 · The big idea</a>
  <a href="#qkv">3 · Query, Key, Value</a>
  <!-- one link per section; sub-items indented by convention -->
</aside>
```

```css
.toc{position:sticky;top:96px;font-family:var(--mono);font-size:12.5px}
.toc h5{font-size:11px;letter-spacing:.2em;text-transform:uppercase;color:var(--muted);margin:0 0 14px}
.toc a{display:block;color:var(--ink-soft);padding:6px 0 6px 14px;border-left:2px solid var(--line);transition:.2s;line-height:1.4}
.toc a:hover{color:var(--teal)}
.toc a.active{color:var(--teal-deep);border-left-color:var(--amber);font-weight:600} /* set by scrollspy */
```

### 6.3 Sticky top bar (brand + horizontal nav)

```html
<div class="topbar" id="topbar">
  <div class="brand"><span class="dot"></span> Page · Notebook</div>
  <nav class="topnav"><a href="#idea">Idea</a><a href="#demo">Demo</a><!-- … --></nav>
</div>
```

```css
.topbar{position:fixed;top:0;left:0;right:0;z-index:50;display:flex;justify-content:space-between;align-items:center;
  padding:14px clamp(16px,4vw,40px);backdrop-filter:blur(8px);
  background:linear-gradient(var(--paper),rgba(246,241,231,0));transition:background .3s,box-shadow .3s}
.topbar.solid{background:rgba(246,241,231,.9);box-shadow:0 1px 0 var(--line)} /* toggled after scrolling */
.brand{font-family:var(--disp);font-weight:600;font-size:18px;color:var(--teal-deep);display:flex;align-items:center;gap:10px}
.brand .dot{width:11px;height:11px;border-radius:50%;background:var(--amber);box-shadow:0 0 0 4px var(--amber-soft)}
.topnav a{margin-left:22px;font-family:var(--mono);font-size:12.5px;letter-spacing:.06em;text-transform:uppercase;color:var(--ink-soft)}
.topnav a:hover{color:var(--amber)}
@media(max-width:820px){.topnav{display:none}}
```

The scrollspy that lights up the matching links lives in Section 7.3.

---

## 7. Animation system

All motion is plain CSS transitions/keyframes driven by a little JS. Five techniques cover the
whole site. **Always end the stylesheet with the reduced-motion guard (7.6).**

### 7.1 Reading-progress bar + sticky-bar solidify

```html
<div id="progress"></div>
```
```css
#progress{position:fixed;top:0;left:0;height:4px;width:0%;z-index:60;
  background:linear-gradient(90deg,var(--teal),var(--gold),var(--amber))}
```
```js
const prog=document.getElementById('progress'), tb=document.getElementById('topbar');
function onScroll(){const h=document.documentElement,m=h.scrollHeight-h.clientHeight;
  prog.style.width=(m>0?h.scrollTop/m*100:0)+'%'; tb.classList.toggle('solid',h.scrollTop>40);}
document.addEventListener('scroll',onScroll,{passive:true}); onScroll();
```

### 7.2 Scroll-reveal (fade-up on enter)

Add `class="reveal"` to any block that should fade in.

```css
.reveal{opacity:0;transform:translateY(26px);
  transition:opacity .7s cubic-bezier(.2,.7,.2,1),transform .7s cubic-bezier(.2,.7,.2,1)}
.reveal.in{opacity:1;transform:none}
```
```js
const io=new IntersectionObserver(es=>es.forEach(e=>{if(e.isIntersecting)e.target.classList.add('in');}),{threshold:.12});
document.querySelectorAll('.reveal').forEach(el=>io.observe(el));
```

### 7.3 Scrollspy (highlight current TOC link)

```js
const tl=[...document.querySelectorAll('.toc a')];
const spy=new IntersectionObserver(es=>es.forEach(e=>{if(e.isIntersecting){const id=e.target.id;
  tl.forEach(a=>a.classList.toggle('active',a.getAttribute('href')==='#'+id));}}),
  {rootMargin:'-28% 0px -60% 0px'});
document.querySelectorAll('main section[id]').forEach(s=>spy.observe(s));
```

### 7.4 "Flowing signal" beams (for hero diagrams)

A short dash sliding along a long gap reads as a pulse travelling down a line.

```css
.beam{stroke-linecap:round;fill:none;stroke-dasharray:10 200;animation:flow 2.8s linear infinite}
@keyframes flow{from{stroke-dashoffset:210}to{stroke-dashoffset:0}}
/* stagger each beam with inline style="animation-delay:.3s" etc. */

.pnode{animation:breathe 3.4s ease-in-out infinite;transform-box:fill-box;transform-origin:center}
@keyframes breathe{0%,100%{transform:scale(1)}50%{transform:scale(1.07)}}
```

### 7.5 Stepped walkthrough widgets (the centrepiece pattern)

This is the reusable engine behind every "press Next / Play" animation. The idea:

1. Lay out the visual once (SVG or an HTML "board") with elements that have **stable IDs**.
2. Define a `STEPS` array; each step lists the **full visual state** (which elements get which
   class, which groups become visible). Listing the *full* state — not a diff — makes Prev/Next
   reliable.
3. `apply(i)` clears all state classes, then applies step `i`'s classes; Play just calls
   `apply(i+1)` on a timer.

```js
function makeWalkthrough({steps, clearAll, render, els}){
  let i=0, timer=null;
  const dots=els.dots, n=steps.length;
  for(let k=0;k<n;k++) dots.appendChild(document.createElement('i'));
  function apply(){ clearAll(); render(steps[i], i);
    [...dots.children].forEach((d,k)=>d.classList.toggle('on',k===i));
    els.prev.disabled=i===0; els.next.disabled=i===n-1; }
  function stop(){ if(timer){clearInterval(timer);timer=null;els.play.textContent='▶ Play';} }
  function go(k){ i=Math.max(0,Math.min(n-1,k)); apply(); }
  els.next.onclick=()=>{stop();go(i+1);};
  els.prev.onclick=()=>{stop();go(i-1);};
  els.play.onclick=()=>{ if(timer){stop();return;} if(i===n-1)go(0);
    els.play.textContent='⏸ Pause'; timer=setInterval(()=>{ i>=n-1?stop():go(i+1); },1900); };
  apply();
}
```

Two visual flavours of widget reuse this engine:

- **Dark "demo"** — teal-gradient card; SVG nodes/edges get `.on` (teal glow) or `.onb` (amber
  glow) classes; used for "watch the signal flow."
- **Light "math board"** — white card; an HTML grid whose cells carry `.mx-s1 … .mx-s4` and
  reveal cumulatively (`.show`), with animated weight bars (`width:0` → `var(--w)`).

Element state classes follow a convention: **teal = forward / normal, amber/gold = the active or
"hot" thing this step.** Bars animate by transitioning `width`; nodes by transitioning `fill` +
`filter:drop-shadow(...)`; numbers by transitioning `opacity`.

```css
/* animated bar inside a step-gated cell */
.mx-bar{height:16px;border-radius:4px;background:var(--teal);width:0;
  transition:width .65s cubic-bezier(.2,.7,.2,1)}
.mx-cell.show .mx-bar{width:var(--w)}   /* --w set inline per bar */
.mx-bar.peak{background:var(--gold)}    /* the single most important value */
.mx-cell{opacity:0;transition:opacity .45s}.mx-cell.show{opacity:1}
```

Tabbed variant: render several examples in the DOM, show one, and reset the stepper to 0 when a
tab is clicked — see the self-attention page's "Run the math" section for the full pattern.

### 7.6 Accessibility guard (always include, last in the stylesheet)

```css
@media (prefers-reduced-motion: reduce){
  *{animation:none !important;transition:none !important}
  .reveal{opacity:1;transform:none}
  html{scroll-behavior:auto}
}
```

---

## 8. Reusable content components

### Section shell
```html
<section id="idea" class="reveal">
  <div class="sec-no">01</div>
  <h2>The big idea</h2>
  <p class="lead-in">The one-sentence hook.</p>
  <p>Supporting prose…</p>
</section>
```

### Callouts (info / warning / note)
```css
.note{background:var(--teal-soft);border-left:4px solid var(--teal);border-radius:10px;
  padding:16px 20px;margin:22px 0;font-size:17px;color:var(--teal-deep)}
.note.amber{background:var(--amber-soft);border-left-color:var(--amber);color:#8a3f1e}
.note.violet{background:#ECEAFB;border-left-color:var(--violet);color:#3d3380}
```

### Intuition cards (the "three ways to picture it" row)
```css
.icards{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:16px;margin:22px 0}
.icard{background:var(--card);border:1px solid var(--line);border-radius:16px;padding:22px;box-shadow:var(--shadow);
  transition:transform .25s,box-shadow .25s,border-color .25s}
.icard:hover{transform:translateY(-4px);border-color:var(--amber);box-shadow:var(--shadow-lg)}
.icard .emoji{font-size:26px;margin-bottom:10px}
.icard h4{font-size:19px;color:var(--teal-deep);margin:0 0 6px}
.icard p{font-size:15.5px;color:var(--ink-soft);margin:0}
```
Use **one emoji per card** as a friendly anchor (🏔️ 🍲 🎛️ 🗂️ 🎧 🍹). Keep it to three cards.

### Figure (inline SVG in a white card)
```css
.fig{background:#fff;border:1px solid var(--line);border-radius:var(--radius);padding:20px;margin:24px 0;box-shadow:var(--shadow)}
.fig svg{display:block;width:100%;height:auto;max-width:760px;margin:0 auto}
.fig figcaption{font-family:var(--mono);font-size:12px;color:var(--muted);text-align:center;margin-top:14px;letter-spacing:.02em}
```
**Diagram colour convention** (so every diagram reads the same): input = blue `--blue`,
hidden/middle = gold `--gold` or amber, output = teal/green, bias/aux = grey `#95A5A6`;
positive edge = green `--green`, negative edge = amber `--neg (#D9663B)`; always give a small
legend.

### Table
```css
table{width:100%;border-collapse:collapse;margin:22px 0;font-size:15.5px;background:#fff;border-radius:12px;overflow:hidden;box-shadow:var(--shadow)}
thead{background:var(--teal);color:#fff;font-family:var(--mono);font-size:12px;letter-spacing:.04em}
th,td{padding:10px 14px;text-align:left;border-bottom:1px solid var(--line)}
tbody tr:nth-child(even){background:var(--paper2)}
td.mono,th.mono{font-family:var(--mono)}
```

### Code block (dark) + output line
```css
pre{background:#0E2A28;color:#CFE9E4;border-radius:14px;padding:20px 22px;overflow-x:auto;
  font-family:var(--mono);font-size:13.5px;line-height:1.7;box-shadow:var(--shadow);border:1px solid #15403c;margin:18px 0;position:relative}
pre::before{content:attr(data-lang);position:absolute;top:9px;right:15px;font-size:10.5px;color:#5f8a84;letter-spacing:.2em}
code.inl{background:var(--teal-soft);color:var(--teal-deep);padding:2px 7px;border-radius:6px;font-size:14px}
.out{background:var(--paper2);border:1px solid var(--line);border-left:3px solid var(--gold);
  border-radius:0 10px 10px 0;padding:10px 16px;font-family:var(--mono);font-size:12.5px;color:var(--ink-soft);white-space:pre;overflow-x:auto;margin:-8px 0 18px}
```
Set the language tag with `<pre data-lang="python">`. Hand-highlight syntax with spans
(`.c-kw .c-fn .c-str .c-num .c-com .c-op`) using the dark-code colours from Section 3.

### Keybox (a single hero equation/result)
```css
.keybox{background:linear-gradient(180deg,#fff,#FBF7EF);border:1.5px solid var(--gold);border-radius:14px;
  padding:8px 18px;margin:18px auto;text-align:center;box-shadow:var(--shadow)}
.eqcard{background:var(--paper2);border:1px dashed var(--line);border-radius:12px;padding:6px 18px;margin:18px 0;overflow-x:auto}
```

---

## 9. Math (MathJax)

Include once in `<head>` and write LaTeX inline with `$…$` or display with `$$…$$`
(matrices via `\begin{bmatrix}`).

```html
<script>
  window.MathJax = {
    tex:{inlineMath:[['$','$'],['\\(','\\)']], displayMath:[['$$','$$'],['\\[','\\]']]},
    svg:{fontCache:'global'}
  };
</script>
<script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js" id="MathJax-script" async></script>
```
Wrap display equations in an `.eqcard` (dashed) or `.keybox` (gold) for emphasis. When a page
has heavy step-by-step numeric arithmetic, render those as **mono text inside `.math` boards**
rather than MathJax — it animates more easily and reads fine.

---

## 10. Footer

```css
footer{position:relative;z-index:2;background:var(--teal-deep);color:#BFD8D4;
  padding:48px clamp(20px,5vw,48px);text-align:center;font-family:var(--mono);font-size:13px}
footer .big{font-family:var(--disp);font-size:24px;color:#fff;margin-bottom:10px}
footer .sub{opacity:.7;margin-top:10px;font-size:12px}
```
End with one memorable line (the page's "thesis" as a formula or phrase), a one-line summary,
and a faint credit/source line.

---

## 11. Final checklist before shipping

- [ ] `:root` tokens pasted; no raw hex anywhere except inside the tokens and diagram SVGs.
- [ ] Three fonts loading; headings Fraunces, body Spectral, labels/code JetBrains Mono.
- [ ] Exactly **one** italic amber word in the hero title.
- [ ] Every `<section>` has an `id`, a `.sec-no`, an `<h2>`, and the `reveal` class.
- [ ] TOC, top nav, and section numbers all agree.
- [ ] Progress bar, scroll-reveal, and scrollspy JS present and running.
- [ ] At most one feature per section; diagrams in `.fig` cards with a legend + mono caption.
- [ ] Interactive widgets list the **full state per step** and have Prev / Next / Play + dots.
- [ ] Gold used only for the single most important value; amber only for emphasis/active.
- [ ] `prefers-reduced-motion` guard is the **last** rule in the stylesheet.
- [ ] MathJax included if there's math; code blocks have `data-lang` and an `.out` for results.
- [ ] Page works opened directly from disk (only external needs: Google Fonts + MathJax CDN).

---

### One-paragraph summary

Warm paper background (`--paper #F6F1E7`) with a faint SVG-noise grain; Fraunces headings,
Spectral body, JetBrains Mono labels. Deep teal is the ink; amber and gold are the only loud
colours — amber for emphasis/active, gold for the single most important value. White cards with
soft shadows and 18px radii. A sticky numbered contents rail plus a sticky top nav, kept in sync,
with a scrollspy. Motion is a reading-progress bar, fade-up-on-scroll reveals, and one or two
"press Next/Play" stepped widgets whose every step declares its full visual state. Always close
the CSS with a `prefers-reduced-motion` guard.