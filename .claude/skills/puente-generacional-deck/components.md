# Component reference

Copy-pasteable patterns from the reference deck, each with the HTML + CSS (+ JS where relevant). All sizes assume the fixed 1920×1080 stage. Pick the pattern that fits the content — don't force a slide into one of these shapes if it doesn't need to be.

## 1. Hairline-divided card grid (2, 3, or 5 columns)

The recurring "row of cards with a 1px rule between them" look (`.r-grid` for 5 cards, `.ctx-grid` for 3, `.cat-grid` for a 2×2). The trick is a `gap:1px` flex/grid container with `background:var(--rule)` showing through the gap, so you get hairline dividers without individual `border-right` bookkeeping.

```css
.my-grid{ display:flex; gap:1px; margin-top:52px; background:var(--rule); border:1px solid var(--rule); }
.my-card{ flex:1; background:var(--paper); padding:36px 32px; }
.my-card .tag{ font-family:var(--font); font-weight:700; font-size:21px; letter-spacing:.08em; text-transform:uppercase; color:var(--teal); }
.my-card h3{ font-family:var(--font); font-weight:700; font-size:38px; margin:14px 0 12px; color:var(--ink); }
.my-card p{ font-family:var(--font); font-weight:400; font-size:25px; color:var(--ink-soft); line-height:1.46; }
```
```html
<div class="my-grid reveal">
  <div class="my-card"><span class="tag">Category</span><h3>Title</h3><p>Body copy.</p></div>
  <div class="my-card"><span class="tag">Category</span><h3>Title</h3><p>Body copy.</p></div>
  <div class="my-card"><span class="tag">Category</span><h3>Title</h3><p>Body copy.</p></div>
</div>
```
For a 2-column version use `display:grid; grid-template-columns:1fr 1fr;` instead of flex — that's all `.cat-grid` changes.

**Sizing tip:** 3 cards at ~575px each comfortably fit 21-25px body copy; going to 5 cards (`.r-grid`) drops body copy to ~20px and title to ~30px to keep breathing room. Check actual rendered width before committing to a font size — don't reuse a size from a different column count without re-checking.

## 2. Chip add/remove (tags)

A row of dark pill "chips" plus a dashed `+ Agregar` button. Click to add an editable chip (auto-focused, auto-selected so the user can type immediately); double-click any chip to remove it; blurring an empty chip removes it automatically.

```css
.chip-row{ margin-top:16px; display:flex; gap:16px; flex-wrap:wrap; align-items:center; }
.chip{ background:var(--ink); color:var(--paper); font-family:var(--font); font-weight:700; font-size:27px; padding:16px 32px; border-radius:8px; }
.chip[contenteditable="true"]{ cursor:text; }
.chip-add{ background:none; color:var(--terracotta); font-family:var(--font); font-weight:700; font-size:27px; padding:15px 30px; border-radius:8px; border:2px dashed var(--terracotta); cursor:pointer; transition:background .2s, color .2s; }
.chip-add:hover{ background:var(--terracotta); color:var(--paper); }
```
```html
<div class="chip-row" id="chipRowX">
  <span class="chip">First tag</span>
  <span class="chip">Second tag</span>
  <button type="button" class="chip-add" id="chipAddX" title="Agregar (doble clic sobre un chip para eliminarlo)">+ Agregar</button>
</div>
```
```js
function wireChipRow(rowId, addBtnId, newChipText) {
    const row = document.getElementById(rowId);
    const addBtn = document.getElementById(addBtnId);
    if (!row || !addBtn) return;
    const selectText = (el) => {
        el.focus();
        const range = document.createRange();
        range.selectNodeContents(el);
        const sel = window.getSelection();
        sel.removeAllRanges();
        sel.addRange(range);
    };
    addBtn.addEventListener('click', () => {
        const chip = document.createElement('span');
        chip.className = 'chip';
        chip.contentEditable = 'true';
        chip.textContent = newChipText;
        row.insertBefore(chip, addBtn);
        selectText(chip);
        chip.addEventListener('blur', () => { if (!chip.textContent.trim()) chip.remove(); });
        chip.addEventListener('keydown', (e) => { if (e.key === 'Enter') { e.preventDefault(); chip.blur(); } });
    });
    row.addEventListener('dblclick', (e) => {
        const chip = e.target.closest('.chip');
        if (chip) chip.remove();
    });
}
// call once per chip row on the deck:
wireChipRow('chipRowX', 'chipAddX', 'Nuevo elemento');
```

**Scoping smaller chips inside a narrower card:** rather than a new component, just override under the parent (this is exactly what the reference deck does for tags inside a 3-column `.ctx-card`):
```css
.ctx-card .chip-row{ margin-top:16px; gap:10px; }
.ctx-card .chip{ font-size:19px; padding:10px 16px; border-radius:6px; }
.ctx-card .chip-add{ font-size:19px; padding:9px 15px; border-radius:6px; border-width:1.5px; }
```

## 3. Numbered list add/remove (objectives, questions)

Same idea as chips but as plain numbered rows instead of pills — used for "Objetivos específicos" (zero-padded `01/02/03`) and the self-report questionnaire (plain `1/2/3`). Renumbers automatically on add/remove.

```css
.mylist-item{ display:flex; gap:18px; padding:16px 0; border-bottom:1px solid var(--rule); align-items:flex-start; }
.mylist-item .n{ color:var(--terracotta); font-weight:800; flex:0 0 auto; }
.mylist-item .text{ flex:1; font-family:var(--font); font-size:25px; color:var(--ink-soft); }
.mylist-add{ margin-top:10px; background:none; border:none; color:var(--terracotta); font-family:var(--font); font-weight:700; font-size:19px; cursor:pointer; padding:6px 0; text-align:left; }
.mylist-add:hover{ text-decoration:underline; }
```
```html
<div class="mylist" id="myList">
  <div class="mylist-item"><span class="n">01</span><span class="text" contenteditable="true">First item.</span></div>
</div>
<button type="button" class="mylist-add" id="myListAdd">+ Agregar</button>
```
```js
(function () {
    const list = document.getElementById('myList');
    const addBtn = document.getElementById('myListAdd');
    if (!list || !addBtn) return;
    const renumber = () => {
        list.querySelectorAll('.mylist-item .n').forEach((n, idx) => { n.textContent = String(idx + 1).padStart(2, '0'); }); // drop padStart for plain "1/2/3"
    };
    const selectText = (el) => {
        el.focus();
        const range = document.createRange();
        range.selectNodeContents(el);
        const sel = window.getSelection();
        sel.removeAllRanges();
        sel.addRange(range);
    };
    addBtn.addEventListener('click', () => {
        const row = document.createElement('div');
        row.className = 'mylist-item';
        const n = document.createElement('span');
        n.className = 'n';
        const text = document.createElement('span');
        text.className = 'text';
        text.contentEditable = 'true';
        text.textContent = 'Nuevo elemento';
        row.appendChild(n);
        row.appendChild(text);
        list.appendChild(row);
        renumber();
        selectText(text);
        text.addEventListener('blur', () => { if (!text.textContent.trim()) { row.remove(); renumber(); } });
        text.addEventListener('keydown', (e) => { if (e.key === 'Enter') { e.preventDefault(); text.blur(); } });
    });
    list.addEventListener('dblclick', (e) => {
        const row = e.target.closest('.mylist-item');
        if (row) { row.remove(); renumber(); }
    });
})();
```

## 4. Expandable detail — shown in a shared panel below the grid, not inline

**Don't** expand a card's detail inline inside a multi-column grid — a half-width card forces small text to fit a 7-item list, which reads as cramped no matter how much you shrink line-height. Instead, keep the detail markup hidden inside each card (as a data source only) and render whichever card was clicked into one shared full-width panel below the whole grid. Only one card's detail shows at a time; clicking the open one again closes it.

```css
.expandable{ cursor:pointer; position:relative; }
.expandable .exp-toggle{ position:absolute; top:32px; right:34px; width:32px; height:32px; border:1px solid var(--rule); border-radius:50%; display:flex; align-items:center; justify-content:center; font-weight:700; transition:transform .3s ease, color .2s ease, border-color .2s ease; }
.expandable.open .exp-toggle{ transform:rotate(45deg); color:var(--terracotta); border-color:var(--terracotta); }
.my-detail{ display:none; } /* data source only — never shown inline */
.my-expand{ max-height:0; overflow:hidden; opacity:0; transition:max-height .45s ease, opacity .35s ease; }
.my-expand.open{ max-height:340px; opacity:1; margin-top:16px; }
.my-expand-inner{ background:var(--paper-2); border:1px solid var(--rule); padding:18px 36px; }
.my-expand-label{ display:block; font-weight:700; font-size:20px; letter-spacing:.08em; text-transform:uppercase; color:var(--terracotta); margin-bottom:8px; }
.my-expand-inner ul{ margin:0; padding:0; list-style:none; display:grid; grid-template-columns:1fr 1fr; gap:8px 56px; } /* 2-col so a 6-7 item list stays short */
.my-expand-inner li{ font-size:21px; line-height:1.3; color:var(--ink-soft); }
```
```html
<div class="my-grid reveal">
  <div class="my-card expandable" tabindex="0" role="button" aria-expanded="false">
    <span class="exp-toggle">+</span>
    <span class="tag">Category</span><h3>Title</h3><p>Teaser text. Toca para ver el detalle.</p>
    <div class="my-detail"><ul><li><b>1 · Item</b> — description.</li></ul></div>
  </div>
  <!-- more .my-card.expandable siblings -->
</div>
<div class="my-expand" id="myExpand"><div class="my-expand-inner" id="myExpandInner"></div></div>
```
```js
(function () {
    const expand = document.getElementById('myExpand');
    const inner = document.getElementById('myExpandInner');
    if (!expand || !inner) return;
    const cards = document.querySelectorAll('.my-card.expandable');
    let activeCard = null;
    cards.forEach((card) => {
        const label = card.querySelector('.tag').textContent;
        const detailHTML = card.querySelector('.my-detail').innerHTML;
        const toggle = () => {
            const willOpen = activeCard !== card;
            cards.forEach((c) => { c.classList.remove('open'); c.setAttribute('aria-expanded', 'false'); });
            if (willOpen) {
                card.classList.add('open');
                card.setAttribute('aria-expanded', 'true');
                inner.innerHTML = '<span class="my-expand-label">' + label + '</span>' + detailHTML;
                expand.classList.add('open');
                activeCard = card;
            } else {
                expand.classList.remove('open');
                activeCard = null;
            }
        };
        card.addEventListener('click', toggle);
        card.addEventListener('keydown', (e) => { if (e.key === 'Enter' || e.key === ' ') { e.preventDefault(); toggle(); } });
    });
})();
```
**Budget check before shipping this:** measure the tallest detail list's rendered height (`max-height` here is just a transition cap, not a real constraint) against the gap between the grid's closed-state bottom and the footer. The reference deck's 7-item list needed the panel's padding/gap trimmed twice before it cleared the footer with real margin — always verify with the longest list, not the shortest.

## 5. Animated connection-lines / word-web diagram

A generalization of the infinity-rings technique for "N labeled points connect to one hub" content (used for objective keywords → "Oficina de la Juventud" hub). Static dotted lines plus small dots at each label; the hub is the same twin-circle brand mark scaled up. This one doesn't animate a draw-in by default in the reference deck (it's a static diagram) — add the `pathLength` draw-in technique from the SKILL.md's rings section to `.pi-link` if you want the connectors to draw in on `.slide.visible`, staggering each `<path>`'s `animation-delay` like the infinity rings do.

```css
.pi-web{ flex:1; max-width:720px; display:flex; align-items:center; justify-content:center; }
.pi-web svg{ width:100%; height:auto; overflow:visible; }
.pi-link{ fill:none; stroke:var(--ink-soft); opacity:.35; stroke-width:2.4; stroke-dasharray:1,10; stroke-linecap:round; }
.pi-w{ font-family:var(--font); font-weight:700; font-size:28px; text-anchor:middle; }
.pi-hub-label{ font-family:var(--font); font-weight:600; font-size:15px; fill:var(--ink-soft); letter-spacing:.04em; text-transform:uppercase; text-anchor:middle; }
```
```html
<div class="pi-web">
  <svg viewBox="0 0 680 480" role="img" aria-label="Keywords connected to the hub">
    <path class="pi-link" d="M90,85 L540,400"/>
    <g><circle cx="90" cy="85" r="5" fill="var(--c-blue)"/><text x="90" y="65" class="pi-w" fill="var(--c-blue)">Keyword</text></g>
    <g transform="translate(540,400)">
      <circle r="24" cx="-13" cy="0" fill="var(--terracotta)"/>
      <circle r="24" cx="13" cy="0" fill="var(--teal)" style="mix-blend-mode:multiply"/>
      <text x="0" y="52" class="pi-hub-label">Hub label</text>
    </g>
  </svg>
</div>
```
The SVG has no `width`/`height` attribute, only `viewBox` — combined with `.pi-web svg{ width:100%; height:auto; }` this means **resizing the diagram is just resizing its container** (change `.pi-web`'s `max-width`), every dot/line/label scales together. Use this to fill leftover horizontal space next to a text column instead of leaving it empty — the reference deck grew this from 520px to 720px specifically to use unused space, and everything (text included) scaled up proportionally for free.

## 6. Side-by-side comparison panel (table + companion callout)

Two unequal-width panels in a row — a denser `flex:1.5` table next to a lighter `flex:1` callout panel (used for the observation table + self-report questionnaire proposal). Reuse this whenever a slide needs "the structured thing" next to "the softer companion note" rather than two equal columns.

```css
.compare-row{ display:flex; gap:40px; align-items:flex-start; margin-top:44px; }
.compare-table{ flex:1.5; border:1px solid var(--rule); }
.compare-row-inner{ display:flex; border-bottom:1px solid var(--rule); }
.compare-dim{ width:300px; flex:0 0 auto; background:var(--paper-2); padding:26px 24px; font-weight:800; font-size:26px; border-right:1px solid var(--rule); display:flex; align-items:center; }
.compare-detail{ flex:1; padding:24px 28px; font-size:19px; color:var(--ink-soft); }
.compare-panel{ flex:1; border:1px solid var(--rule); background:var(--paper-2); padding:32px 34px; }
```
Layout only — the callout panel's inner content is whatever fits (plain text, or the numbered-list-add/remove pattern above, as in the questionnaire).

## 7. Pie/donut wedge fan-open animation

Each `<path class="wedge">` in an SVG pie/donut scales and rotates in from a folded state on slide entry — a lighter-weight sibling of the rings technique for chart wedges specifically:

```css
.pie-chart svg path.wedge{ transform-box:view-box; transform-origin:160px 160px; opacity:0; transform:scale(.2) rotate(-14deg); }
.slide.visible .pie-chart svg path.wedge{ animation:fanOpen .6s cubic-bezier(.22,.85,.3,1) forwards; }
@keyframes fanOpen{ to{ opacity:1; transform:scale(1) rotate(0deg); } }
```
`transform-origin` must match the pie's actual center in the SVG's own coordinate space (160,160 for a `viewBox="0 0 320 320"` pie) — get this wrong and wedges fan open around the wrong point instead of the chart's center.

**Label placement inside wedges:** compute each label's `x = cx + r*sin(θ)`, `y = cy - r*cos(θ)` (θ in degrees, clockwise from 12 o'clock) at the wedge's mid-angle, then verify with real geometry, not eyeballing — see the verification workflow in SKILL.md. A label a few px outside its wedge's true radius at that height is a real, reported bug class in the reference deck, not a hypothetical.
