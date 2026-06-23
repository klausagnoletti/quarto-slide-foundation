# Slide Foundation

A theme-agnostic Quarto reveal.js slide **foundation layer**: one role-token
contract plus a library of elements authored purely against it. A theme becomes
nothing more than an implementation of the contract: set the role tokens and the
entire element library renders in that theme's identity. Two different token sets
produce element sets no one would guess share a foundation.

## Install

```bash
quarto add klausagnoletti/quarto-slide-foundation
```

`quarto add` vendors a copy into your project's `_extensions/`. To pin a
version: `quarto add klausagnoletti/quarto-slide-foundation@v0.2.0`.

(Also installable from a Codeberg mirror archive if you prefer:
`quarto add https://codeberg.org/klausagnoletti/slide-foundation/archive/v0.2.0.zip`,
using the `.zip`, since `quarto add` always unzips the download.)

## Use

```yaml
---
format: slide-foundation-revealjs
---
```

Skin it by overriding the L0 role tokens via `include-in-header`:

```yaml
format:
  slide-foundation-revealjs:
    include-in-header: my-skin.html   # <style>:root{ --surface:…; --accent:…; }</style>
```

Set tokens only, never component selectors. Light and dark are the same
mechanism: flip `--surface`/`--ink` and adjust `--shadow`.

## Themed toolboxes (v0.2.0)

Beyond the element library, the foundation themes common Quarto/reveal toolboxes
from the same role tokens:

- **Diagrams**: Mermaid (do not force a `mermaid.theme`; the foundation drives
  Quarto's `--mermaid-*` vars) and Graphviz `dot` both reskin from tokens.
- **Icons**: a `{{< icon set:name role=accent >}}` shortcode inlines Iconify
  icons offline as `currentColor` SVG (role = `accent|muted|ink`, or omit to
  inherit text colour). Requires the icon set(s) installed locally:
  `bun add @iconify/utils @iconify-json/tabler @iconify-json/lucide`.
- **Code windows**: `mcanouil/quarto-code-window` chrome themes from tokens
  (`quarto add mcanouil/quarto-code-window`); pick a matching `highlight-style`
  (e.g. a dark one for dark skins) for the code body.
- **Layout**: a `.rows` / `.row` primitive (vertical counterpart to `.columns`).
- **Motion**: reveal fragments and a `.fx` fragment driven by `--motion-dur` /
  `--motion-ease`; a `window.SlideFoundation` JS helper (`token`, `tokenColor`,
  `tokenMs`) lets chart/animation libraries read role tokens.

## API

The stable public surface (L0 tokens, L1 element classes, the multi-fill SVG
convention, CI entry points) is documented in [API.md](API.md).

## Mechanism note

The foundation ships as **plain CSS** via the format's `css:`, not an scss
theme. Quarto's scss colour-variable analyzer chokes on a `:root{ --custom-props }`
block and drops the compiled theme; plain CSS bypasses it. Role tokens are CSS
custom properties (runtime-themeable); a skin overrides `:root` via
`include-in-header`.

## License

MIT
