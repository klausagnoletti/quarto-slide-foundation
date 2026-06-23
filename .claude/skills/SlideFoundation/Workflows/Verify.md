# Workflow: Verify — render and look at every slide

Inserting an element is not the same as it rendering correctly. Reveal.js builds
slides client-side and Mermaid renders at load, so static HTML, grep, and
element-counts miss the real bugs. The only valid check is: render, screenshot
every slide after JS runs, and look at the pixels.

Do NOT use Interceptor's screenshot for this — it captures from y=0 and ignores
the reveal slide route. Use headless Chromium navigating to each `#/N` fragment.

## 1. Render

```bash
quarto render deck.qmd
```

## 2. Screenshot every slide headless

`shoot.ts` (bun + puppeteer-core / @sparticuz or system chromium):

```bash
bun -e '
import puppeteer from "puppeteer";
const file = "file://" + process.cwd() + "/deck.html";
const b = await puppeteer.launch({args:["--no-sandbox"]});
const p = await b.newPage();
await p.setViewport({width:1280,height:720,deviceScaleFactor:2});
await p.goto(file,{waitUntil:"networkidle0"});
const n = await p.evaluate(()=>window.Reveal.getTotalSlides());
for(let i=0;i<n;i++){
  await p.evaluate(j=>window.Reveal.slide(j),i);
  await new Promise(r=>setTimeout(r,500));   // let mermaid + fragments settle
  await p.screenshot({path:`shot-${String(i).padStart(2,"0")}.png`});
}
await b.close(); console.log("shot",n,"slides");
'
```

Then **Read each `shot-*.png`** and look. Do not summarise from counts.

## 3. Check each toolbox against its failure signature

| Thing | PASS | FAIL signature → cause |
|---|---|---|
| Icons | real SVG glyph | literal `[set:name]` text → resolver/deps missing (`bun add @iconify/utils @iconify-json/<set>`) |
| Mermaid | surface nodes, ink text, accent borders, muted edges | blue/white text, white boxes → `mermaid.theme` forced or vars bypassed |
| Graphviz | accent-outlined nodes, ink text | black-on-white default → recolour missed |
| Code window | themed titlebar + accent border | OS-white window → chrome not bound (filter/extension not loaded) |
| Elements | bordered, token-coloured | unstyled mono black/white → foundation CSS not loaded |
| `.vcenter` | content vertically centred | pinned to top → centring failed |
| Diagram weight | strokes match deck borders | thin/wispy lines → `--edge` not reaching diagram strokes |
| Diagram type | diagram text in deck font | trebuchet/Times → font inherit overridden |

## 4. Harmony, not just presence

A pass is BOTH: (a) the element inserted, AND (b) it visually matches the deck —
same colour roles, stroke weight (`--edge`), font family, and typographic weight
(`--weight-*`) as everything else on the slide. A mermaid diagram that renders
but has thinner lines or a foreign font than the body text is a FAIL on harmony
even though insertion "worked". The four harmony axes are in the canonical guide §4.

## 5. Report honestly

If a slide is wrong, say which and show the shot. Never claim a visual result you
have not seen. "Renders" is not "renders correctly".
