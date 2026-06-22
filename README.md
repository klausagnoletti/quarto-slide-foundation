# Slide Foundation

A theme-agnostic Quarto reveal.js slide **foundation layer**: one role-token
contract plus a library of elements authored purely against it. A theme becomes
nothing more than an implementation of the contract: set the role tokens and the
entire element library renders in that theme's identity. Two different token sets
produce element sets no one would guess share a foundation.

## Install

```bash
quarto add https://codeberg.org/klausagnoletti/slide-foundation/archive/v0.1.0.tar.gz
```

(Codeberg serves a versioned tarball; `quarto add` vendors a copy into your
project's `_extensions/`.)

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
