# Workflow: NewDeck — scaffold a deck on Slide Foundation

Goal: a minimal deck that renders on the extension, so authoring can begin.

## 1. Install the extension

From the deck project root (the dir that will hold the `.qmd`):

```bash
quarto add klausagnoletti/quarto-slide-foundation --no-prompt
```

Notes that bite (from experience):
- `quarto add` **always unzips**, so the GitHub tarball must resolve to a `.zip`;
  the `owner/repo` form handles this. Do not hand it a `.tar.gz`.
- `--no-prompt` is required non-interactively or it hangs on the trust prompt.
- It vendors **only** `_extensions/slide-foundation/`. The canonical authoring
  guide `_extensions/slide-foundation/AGENTS.md` lands locally — read it now.

## 2. Read the canonical guide

```bash
cat _extensions/slide-foundation/AGENTS.md
```

That is the source of truth for the token contract, elements, and toolbox
harmony. This skill never restates it.

## 3. Minimal deck

Create `deck.qmd`:

```markdown
---
title: "Deck title"
format: slide-foundation-revealjs
---

## {.vcenter}

::: {.lockup}
[Deck title]{.title}
[Subtitle line]{.subtitle}
:::

## A content slide

::: {.card}
One focal idea. Full sentences go in speaker notes.
:::

::: {.notes}
- keyword cue, not prose
:::
```

The format key is `slide-foundation-revealjs` (extension-dir name + `-revealjs`).

## 4. Pick a skin

The bare deck uses the extension's default dark token values. To set identity,
run the **NewSkin** workflow. Do not write component CSS in the deck.

## 5. Render and verify

```bash
quarto render deck.qmd
```

Then run the **Verify** workflow. Never claim it looks right unseen.

## Toolboxes (optional, add only what the deck uses)

- **Icons**: `bun add @iconify/utils @iconify-json/tabler @iconify-json/lucide`,
  then `{{< icon tabler:shield role=accent >}}`.
- **Mermaid**: built into Quarto reveal.js. Do NOT set a baked `mermaid.theme`
  (it overrides the token bridge).
- **Graphviz**: fenced ```{dot}``` block; recolour is automatic.
- **Code window**: `quarto add mcanouil/quarto-code-window` + the `code-window`
  filter; pick a dark `highlight-style` for dark skins.

Per-toolbox gotchas and the harmony rationale are in the canonical guide §4.
