# Workflow: NewSkin — author a skin

A skin sets ONLY `:root` role tokens. No component or geometry selectors — that
is what makes it a skin and not a fork. (The extension's contract gate rejects a
skin that contains any non-`:root` CSS.)

## 1. Write a token head file

`my-skin.html` (a `<style>` block, nothing else):

```html
<style>
:root{
  --surface:#000000;   /* background / card fill        */
  --ink:#ffffff;       /* body + heading text           */
  --accent:#ED1C24;    /* emphasis, borders, big numbers */
  --muted:#9aa0aa;     /* labels, attributions, edges    */
  --radius:4px;
  --edge:2px;          /* stroke weight: borders + diagram strokes */
  --shadow:0 2px 12px rgba(0,0,0,.4);
  --pad:0.7em 1em;
  --gap:0.8em;
  --motion-dur:0.4s; --motion-ease:ease;
  --weight-strong:800; --weight-bold:700; --weight-mid:600;
}
</style>
```

Set only tokens from the contract. Light/dark is the same file with
`--surface`/`--ink` flipped. To make diagrams/elements heavier or lighter,
tune `--edge` and the `--weight-*` tokens; the whole deck follows.

## 2. Wire it into the deck

```yaml
format:
  slide-foundation-revealjs:
    include-in-header:
      - my-skin.html
```

`include-in-header` merges across the format and the document, so the skin's
`:root` block lands after the extension defaults and overrides them.

## 3. WCAG-check the colours (mandatory)

Contrast requirements on `--surface`:
- `--ink` and `--muted` text: ratio ≥ **4.5:1**
- `--accent` (used for large text / borders / data): ratio ≥ **3:1**

Quick check (bun, sRGB relative luminance / WCAG 2.x):

```bash
bun -e '
const L=h=>{const c=h.replace("#","").match(/../g).map(x=>parseInt(x,16)/255).map(v=>v<=.03928?v/12.92:((v+.055)/1.055)**2.4);return .2126*c[0]+.7152*c[1]+.0722*c[2]};
const R=(a,b)=>{const x=L(a),y=L(b);const[hi,lo]=x>y?[x,y]:[y,x];return ((hi+.05)/(lo+.05)).toFixed(2)};
const surface="#000000";
for(const[n,c]of[["ink","#ffffff"],["muted","#9aa0aa"],["accent","#ED1C24"]])
  console.log(n.padEnd(7), c, R(surface,c)+":1");
'
```

Fix any token below threshold before shipping. (The extension repo also has
`contrast.ts` if you want the batch checker.)

## 4. Verify

Render and run the **Verify** workflow. A skin is not done until you have looked
at every slide and confirmed elements AND toolboxes read as one system.

## Picking accessible values

Use Open Props or Radix Colors to choose values, then assign them to these role
tokens. Do not invent ad-hoc hexes per element.
