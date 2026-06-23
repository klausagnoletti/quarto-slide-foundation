# Slide Foundation — Public Component API

Stable, documented surface for the `slide-foundation` Quarto reveal.js base
format extension. Any producer (a human author, the Google-Slides converter, a
future tool) targets THIS contract. The L0 token names and the L1 element
classes below are the API; they are additive-only (see `contract.lock.json`,
enforced by `gates.ts`).

## How a theme consumes it

1. Add the extension: `quarto add <owner>/slide-foundation@vX` (vendored copy
   lands in `_extensions/slide-foundation/`).
2. Use the format: `format: slide-foundation-revealjs`.
3. Skin it: supply a `<style>:root{ … }</style>` via `include-in-header` that
   overrides the L0 role tokens. Set tokens ONLY — zero component selectors
   (enforced by the skin-conformance gate, ISC-36).

A skin that sets the role tokens re-skins the ENTIRE element library at once.
Light and dark are the same mechanism: flip `--surface`/`--ink` (+ adjust
`--shadow`). Every element is obligated to render correctly in both (ISC-21).

## L0 — the adaptation contract (role tokens)

| Token       | Role                              | Kind      | Default            |
|-------------|-----------------------------------|-----------|--------------------|
| `--surface` | background / card fill            | colour    | `#15131f`          |
| `--ink`     | body + heading text               | colour    | `#efeafd`          |
| `--accent`  | emphasis: borders, nodes, big-num | colour    | `#d6b25a`          |
| `--muted`   | labels, attributions, secondary   | colour    | `#9a93b5`          |
| `--radius`  | corner rounding                   | treatment | `6px`              |
| `--edge`    | border / rule thickness           | treatment | `3px`              |
| `--shadow`  | depth                             | treatment | `none`             |
| `--pad`     | internal padding of boxes         | treatment | `0.7em 1em`        |
| `--gap`     | grid/flex gap                     | treatment | `0.8em`            |

Colour tokens must pass WCAG AA against `--surface` (ink/muted ≥ 4.5:1, accent
≥ 3:1 as it is large-text only) — enforced by `contrast.ts` (ISC-33).

## L1 — element library (classes)

All authored with Pandoc fenced divs `::: {.class}`. Elements consume ONLY role
tokens; they hold zero hex and zero font-family (ISC-15/30, gated).

| Class           | Markup shape                                                        | Notes |
|-----------------|---------------------------------------------------------------------|-------|
| `.lockup`       | `::: {.lockup}` with `[…]{.title}` + `[…]{.subtitle}`               | title block + accent underline |
| `.divider`      | `::: {.divider}` TEXT `:::`                                          | section rule with centred label |
| `.card`         | `::: {.card}` … `:::`                                                | bordered surface panel |
| `.callout`      | `::: {.callout}` … `:::`                                             | accent-tinted note (uses `color-mix`) |
| `.feature-grid` | `::: {.feature-grid}` + bullet list                                 | 3-column themed cards (one per `<li>`) |
| `.quote`        | `::: {.quote}` text `[…]{.quote-attr}`                               | accent left-bar pull-quote |
| `.timeline`     | `::: {.timeline}` + bullet list, each `[…]{.t-label}`               | nodes on a connector line |
| `.bignum` / `.bignum-label` | `::: {.bignum}` N `:::` then `::: {.bignum-label}` … | oversized stat + caption |
| `.icon-slot`    | `[…]{.icon-slot}` wrapping inline `<svg>`                            | single-fill, `currentColor` = accent |
| `.emblem`       | `::: {.emblem}` + `<svg>` using part classes                        | MULTI-fill (see below) |

### Multi-fill SVG convention (`.emblem`, ISC-39)

Inside `.emblem` the SVG parts bind distinct role tokens via these classes, so a
token flip recolours each part independently:

| SVG class | Fill / stroke      |
|-----------|--------------------|
| `.e-bg`   | `fill: --surface`  |
| `.e-ring` | `stroke: --accent` |
| `.e-body` | `fill: --accent`   |
| `.e-inner`| `fill: --surface`  |
| `.e-dot`  | `fill: --muted`    |
| `.e-bar`  | `fill: --ink`      |

## Toolbox theming (v0.2.x)

The foundation also themes common Quarto/reveal toolboxes from the same role tokens.

### Icons (shortcode)
`{{< icon set:name >}}` inlines an Iconify icon offline as a `currentColor` SVG.
Optional `role=accent|muted|ink`; omit to inherit the surrounding text colour.
Consumer must install the set(s): `bun add @iconify/utils @iconify-json/tabler @iconify-json/lucide`.
House sets: Tabler (`tabler:*`), Lucide (`lucide:*`).

### Diagrams
- **Mermaid**: themed via Quarto's `--mermaid-*` vars (bound to role tokens) plus
  `.nodeLabel`/`.edgeLabel` bindings. Do NOT set a baked `mermaid.theme` (e.g.
  `neutral`) — it overrides the vars.
- **Graphviz `dot`**: node/edge/text/background recoloured from tokens (scoped to `g.graph`).

### Layout
- `::: {.rows}` containing `::: {.row}` blocks — vertical counterpart to `.columns`.
- `## Title {.vcenter}` — vertically centre a sparse slide (flow content only, not floats).

### Code windows
`mcanouil/quarto-code-window` chrome (container, titlebar, filename, line gutter)
binds to role tokens. Add it with `quarto add mcanouil/quarto-code-window` and the
`code-window` filter; pick a `highlight-style` matching the skin for the code body.

### Motion + JS token helper
`.fragment.fx` is a token-driven reveal fragment (reads `--motion-dur`/`--motion-ease`).
`window.SlideFoundation` exposes `token(name)`, `tokenColor(name)`, `tokenMs(name)`
so JS libraries (charts, Lottie recolour, animation engines) can read role tokens.

## Verification entry points (for CI)

| Tool          | Covers                          |
|---------------|---------------------------------|
| `gates.ts`    | ISC-35 additive-only, ISC-36 skin-conformance, ISC-15/30 no literals |
| `contrast.ts` | ISC-33 WCAG AA across every token set |
| `regress.ts`  | ISC-23 screenshot-diff regression (`--bless` to set baselines) |
| `shoot.ts`    | headless per-slide screenshots (used by regress) |

## Stability

The L0 token names and L1 class names are the public API and change only
additively. New tokens earn their place by the rule of three (needed by ≥2
independent skins; ISC-31). Removing/renaming any locked token fails CI.
