---
name: puente-generacional-deck
description: Build or extend an HTML slide deck using the exact visual format of "Propuesta Programa Intergeneracional" (warm paper background, terracotta+teal twin-circle brand mark, Raleway type, hand-drawn animated infinity/ring diagrams). Use whenever the user asks for a new deck "in this same format/style", references this presentation, or wants a deck for Municipalidad de Providencia / Oficina de la Juventud or a similar warm-institutional, animation-rich look.
---

# Puente Generacional Deck Style

This captures the exact design system of `propuesta-programa-intergeneracional.html` so it can be reused for new decks without re-deriving it from scratch. It is a specialization of the `frontend-slides` skill's fixed-16:9-stage architecture — read that skill first for the base mechanics (stage scaling, `.slide`/`.active`/`.visible`, keyboard/touch/wheel nav). This skill only adds the specific look, palette, typography, and signature components the user liked, plus the two real bugs found and fixed while building the reference deck.

## When to use this

- The user asks for a deck "like the other one", "in this same format", or names this presentation.
- The user wants a warm, paper-textured, editorial deck for a municipal/institutional/social-program audience, with hand-drawn-feeling SVG animations rather than generic slide-transition effects.

## Quick start

1. Copy [base-template.html](base-template.html) as the starting file for the new deck.
2. Swap the `:root` palette (see below) and the `<title>` / org names in `.top-row .org` if the brand differs — everything else (stage, kicker mark, reveal stagger, inline editor, progress dots) works unchanged.
3. Build content slides using the components in [components.md](components.md) — pick whichever pattern fits each slide's content, don't force every slide into the same shape.
4. Verify every slide with the Playwright overflow-check workflow below before calling anything done.

## Palette (the exact institutional colors from the reference deck)

```css
:root{
    --paper:#F3ECE1;       /* warm cream page background */
    --paper-2:#EAE0D0;     /* slightly darker paper, for card/panel fills */
    --ink:#2B2420;         /* near-black warm brown, primary text */
    --ink-soft:#5B5147;    /* softer body-copy brown */

    /* brand palette — a primary triad + secondary accents used sparingly */
    --c-blue:#3E96B4;
    --c-turq:#03C2AE;
    --c-green:#8BB844;
    --c-orange:#F7931E;
    --c-yellow:#FFCC33;
    --c-pink:#ED1E79;
    --c-purple:#662D91;
    --c-royal:#3B6AF4;

    /* the deck's two "brand duo" colors are aliases onto the primary triad —
       swap these two lines to re-skin the whole deck without touching component CSS */
    --terracotta:var(--c-blue);
    --terracotta-soft:#D6E9ED;
    --teal:var(--c-turq);

    --rule:rgba(43,36,32,.16);   /* hairline dividers on top of --paper */
    --stage-bg:#1c1712;          /* letterbox color outside the 16:9 stage */

    --font: 'Raleway', sans-serif;
    --ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1);
    --duration-normal: 0.7s;
}
```

Rules for reusing this palette on a new brand:
- Keep `--terracotta` / `--teal` as *aliases*, not hard-coded hex, everywhere in component CSS — re-skinning then means changing two lines.
- The primary triad (blue/turq/green) cycles through numbered items (índice entries, chip colors, `principle`/`idx-n` numbers) — keep at least 3 hues in the triad so that cycle still reads as intentional, not random.
- `--paper`/`--paper-2` should stay a close, warm pair (card fills sit on `--paper-2` inside a `--paper` page) — a stronger contrast here starts to look like generic "card on white" rather than a warm editorial page.

## Typography

Raleway, loaded at the weights actually used (300–900):
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Raleway:wght@300;400;500;600;700;800;900&display=swap" rel="stylesheet">
```

Scale (all sizes are at the 1920×1080 design size — never shrink for smaller screens, the stage scales as a whole):

| Role | Selector | Weight | Size | Notes |
|---|---|---|---|---|
| Section kicker | `.kicker` | 700 | 21px | uppercase, `.14em` tracking, teal, brand mark inline |
| Portada title | `h1.title` | 800 | 54px | only on the cover slide |
| Section title | `h2.sec` | 800 | 94px | shared across content slides; `<em>` inside turns terracotta |
| Lede / subhead | `.lede` | 400 | 33px | max-width 1180px so it wraps at 2 lines, not a wall of text |
| Body copy in cards | varies | 400–600 | 18–27px | see components.md per component |
| Footer | `.foot-row` | 500 | 17px | page number in bold terracotta |

**Per-slide overrides are fine and expected.** Several slides in the reference deck scope `h2.sec`/`.lede` down (e.g. `.slide-idx h2.sec`, `.slide-cat h2.sec`) to free vertical space for a content-heavy grid below. Scope the override to a slide-specific class, never edit the shared `h2.sec`/`.lede` rule itself unless the change should apply to *every* slide.

## Signature components (what people actually notice)

### 1. The animated infinity/rings (the one the user singled out)

Five concentric arcs draw themselves in on slide entry, each a different palette color, staggered ~0.22s apart, ending on two labels that fade up. The trick is `pathLength="1"` on each `<path>` plus `stroke-dasharray:1; stroke-dashoffset:1` in CSS, animated to `stroke-dashoffset:0` — `pathLength` normalizes the dash math so it works regardless of the path's real length or shape.

```css
.inf-ring{ fill:none; stroke-width:3; stroke-linecap:round; stroke-linejoin:round; stroke-dasharray:1; stroke-dashoffset:1; }
.slide.visible .inf-ring{ animation:drawInf 3s cubic-bezier(.3,.6,.3,1) forwards; }
@keyframes drawInf{ to{ stroke-dashoffset:0; } }
.inf-label{ font-family:var(--font); font-weight:800; font-size:29px; fill:var(--ink); opacity:0; }
.slide.visible .inf-label{ animation:fadeUp .8s ease 1.3s forwards; }
```
```html
<path class="inf-ring" pathLength="1" style="animation-delay:0s"   stroke="#3E96B4" d="M ... " />
<path class="inf-ring" pathLength="1" style="animation-delay:.22s" stroke="#03C2AE" d="M ... " />
<path class="inf-ring" pathLength="1" style="animation-delay:.44s" stroke="#8BB844" d="M ... " />
<path class="inf-ring" pathLength="1" style="animation-delay:.66s" stroke="#FFCC33" d="M ... " />
<path class="inf-ring" pathLength="1" style="animation-delay:.88s" stroke="#F7931E" d="M ... " />
```

**This technique generalizes beyond an infinity symbol.** Any SVG path (a ring, a wheel, a connecting line, a custom icon outline) can "draw itself in" this way — `pathLength="1"` + the two CSS rules above is the whole recipe. Reuse it whenever a slide has a diagram whose *shape itself* is the point (a cycle of N components, a process arrow, a connections map), not just for literal infinity symbols. See [components.md](components.md) for the word-web / connection-lines variant used on the objectives slide.

### 2. Moving dots over a hero image (portada)

An absolutely-positioned SVG sits over the hero illustration; each dot is a `<circle>` with an SMIL `<animateMotion>` child tracing a bezier path, `repeatCount="indefinite"`. Prefer SMIL `animateMotion` over a CSS `transform`-based path animation on this kind of loose, ambient motion — a CSS-only version needs `offset-path`/`transform-box:fill-box`, which is inconsistent across browsers for this use case; SMIL renders identically everywhere.

```html
<svg class="hero-dots" viewBox="0 0 1000 666" preserveAspectRatio="none">
  <circle r="7" fill="var(--c-blue)"><animateMotion dur="9s" repeatCount="indefinite" path="M10,130 C 200,40 400,150 600,60 S 850,120 990,90"/></circle>
</svg>
```

### 3. Brand mark (twin circles)

The recurring kicker mark — two overlapping circles, one terracotta, one teal with `mix-blend-mode:multiply` so the overlap darkens naturally instead of needing a third manually-picked color:

```css
.mark{ position:relative; display:inline-block; width:34px; height:20px; vertical-align:middle; }
.mark .c{ position:absolute; top:0; width:20px; height:20px; border-radius:50%; }
.mark .c.a{ left:0; background:var(--terracotta); }
.mark .c.b{ left:14px; background:var(--teal); mix-blend-mode:multiply; }
```
```html
<span class="mark"><span class="c a"></span><span class="c b"></span></span>
```

## Fixed chrome every slide shares

- `.slide-content` is inset 96px sides / 64px top-bottom from the 1920×1080 stage.
- Section kicker at the top: brand mark + `NN — Section name`. **Keep kicker numbers in sync with however the deck is actually grouped/ordered** — see the renumbering discipline below.
- `.foot-row` pinned to the bottom: org name (left) + `NN / total` page count (right, bold terracotta). Two slides in the reference deck use the alternate footer text ("Depto. Diversidad e Inclusión") for the cover and closing slide — that's a deliberate bookend, not an inconsistency.
- `.reveal` on top-level content blocks gives the staggered fade-up entrance; only the first 5 `.reveal` children of a slide get a stagger delay (`nth-child(1)`..`(5)`), so don't rely on it for a 6th block — it'll pop in with the 5th's delay instead of getting its own.
- Progress dots (`.deck-controls`) and the `NN / total` counter are generated from `document.querySelectorAll('.slide').length` — never hardcode a total, it's always correct as long as slides use `class="slide"`.

## Renumbering discipline (the two things that break easiest)

This deck's kicker numbers and footer page counts are **not** automatically derived — they're written into each slide's markup by hand. Every time a slide is added, removed, or reordered:

1. Recount every `.foot-row .page` span so it reads `NN / total` sequentially with no gaps, and update `total` on every slide (including the cover and closing slide, which use it too).
2. Recount every `.kicker` number. If the deck's own index/agenda slide *groups* several physical slides under one numbered topic (this reference deck does — e.g. "Las 5R" and "Categorización" are both index item 02), give **grouped slides the same kicker number**, don't just increment per physical slide. Mismatched kicker-vs-index numbers is a real bug users will notice and report (it happened in this deck).
3. If the deck has its own índice/agenda slide, update its item list and renumber it too — merges/reorders there should mirror the physical slide order, not drift from it.

Do this renumbering as one pass with a script (`grep -n` to find every kicker/footer line, then targeted line-number edits) rather than one-by-one string replacements — page numbers repeat in ways that make later edits ambiguous once an earlier edit creates a duplicate value.

## Interactive/editable patterns

The reference deck's audience edits it directly in the browser (municipal staff filling in their own tags/objectives), so several components are either always-editable or grow/shrink via a small add/remove control. See [components.md](components.md) for the exact HTML/CSS/JS for:

- **Chip add/remove** (tags like "Desafíos identificados") — a `+ Agregar` button appends an editable chip, double-click removes one.
- **Numbered list add/remove** (objectives, questionnaire questions) — same idea, renumbers on add/remove.
- **Detail-below-grid on click** — when a card's expanded detail needs more room than half a card can give it (a 2-column grid card is too narrow for a readable list), move the expanded content to a shared full-width panel *below* the whole grid instead of growing the card inline. This was a real fix made mid-project after the inline version forced 16px text into a half-width card; the below-grid version let the same content run at 21px+ at full width.

### The inline editor's autosave has one sharp edge — always include this fix

The global pencil-icon edit mode (`InlineEditor` class) snapshots the *entire* `#deckStage.innerHTML` to `localStorage` whenever edit mode is turned off, and restores that snapshot on every load. This is exactly what you want for a user's own text edits surviving a reload — but it will also silently resurrect a *stale* snapshot forever once the deck's slide count changes (a slide added/removed after the snapshot was taken), because nothing invalidates it. In the reference deck this manifested twice: a newly-added índice slide simply not appearing for the user, and a live workshop activity always showing a previous run's already-filled-in answers instead of starting fresh.

Always ship `restoreFromStorage` with the slide-count guard:

```js
restoreFromStorage() {
    try {
        const saved = localStorage.getItem('frontend-slides-content');
        if (!saved) return;
        const stage = document.getElementById('deckStage');
        const liveCount = stage.querySelectorAll('.slide').length;
        const savedCount = (saved.match(/class="slide[ "]/g) || []).length;
        if (savedCount !== liveCount) {
            // Saved copy predates a slide add/remove — stale, discard it.
            localStorage.removeItem('frontend-slides-content');
            return;
        }
        stage.innerHTML = saved;
    } catch (e) { /* localStorage unavailable — ignore */ }
}
```

And for any slide that's a **live activity** meant to be redone from scratch by a new audience each time (not a one-time user customization like tags/objectives), don't trust the restored DOM state at all — call the widget's own `reset()` on init instead of just re-rendering from current JS state:

```js
resetBtn.addEventListener('click', reset);
reset();   // not render() — guarantees a clean start regardless of any restored DOM
```

## Verification workflow (non-negotiable before calling a slide done)

Every change gets checked in a real headless browser before it's considered finished — "looks right in the editor" is not sufficient, several real bugs in this deck (a CSS specificity bug making two words render 2× their intended size, labels sitting a few px outside their pie wedge) were only caught this way:

1. Launch Playwright, load the file, navigate to the slide (`ArrowRight` × N, or `showSlide`).
2. Read `getBoundingClientRect()` for the content's bottom-most element and for `.foot-row` — the content's `bottom` must stay clearly above the footer's `top` (don't just eyeball it; a few px is not a safe margin at this scale).
3. For anything inside a curved/circular shape (a pie chart, a ring), derive the true geometry (center, radius from the container's rendered box + the SVG's `viewBox`) and check each label's *farthest corner* against it — a simple bounding-box overlap check is not strict enough; it can pass while a label's corner still pokes outside the circle.
4. If interactive, drive the actual interaction (click the expand toggle, click "+ Agregar", answer through a stepper) and re-check layout in the resulting state, not just the resting state.
5. Screenshot and look at it. Geometry checks catch overflow; they don't catch "technically not overflowing but visually cramped" — that needs an eyeball pass too.

## Supporting files

| File | Purpose |
|---|---|
| [base-template.html](base-template.html) | Trimmed, working starter deck — stage, portada, índice, and one example of each signature component. Copy this as the first file of a new deck. |
| [components.md](components.md) | Copy-pasteable HTML/CSS/JS for every reusable pattern: chip add/remove, numbered-list add/remove, expandable-detail-below-grid, animated connection-lines diagram, obs-table style side-by-side comparison. |
