---
name: SlideFoundation
description: "Build, skin, and verify Quarto reveal.js decks on the theme-agnostic Slide Foundation extension (a role-token contract + element library + auto-themed toolboxes for icons/diagrams/code). This is the CAPABILITY layer for that extension: it adds Claude-specific procedures (scaffold a new deck, author a skin, run the render-and-verify loop) on top of the extension's own canonical guide. The single source of truth for the model, token contract, elements, and toolbox-harmony decisions is the vendored guide at `_extensions/slide-foundation/AGENTS.md` inside the deck repo — this skill never duplicates it, it routes to it and turns it into concrete commands. USE WHEN building or editing a Quarto reveal.js deck that uses (or should use) the slide-foundation extension, 'scaffold a deck on the foundation', 'make a skin', 'reskin the deck', 'set the role tokens', 'add icons/mermaid/graphviz/code-window and make them match the theme', 'why does the diagram look off / too thin / wrong font', 'verify the deck renders', or 'add slide-foundation to this project'. NOT FOR writing slide COPY or speaker notes (use SlideCraft), pure visual/colour design (use Designer), or building decks that are NOT on this extension."
effort: low
---

# SlideFoundation

The invocable capability layer for the **Slide Foundation** Quarto reveal.js
extension. The extension itself ships its own canonical authoring guide; this
skill is the Claude-specific operating layer that turns that guide into concrete
command sequences and self-activates on the triggers above.

## Single source of truth (read this first)

The model, the L0 role-token contract, the element library, the four
toolbox-harmony axes, and every conscious design decision live in **one file**:

```
_extensions/slide-foundation/AGENTS.md
```

inside the deck repo (it travels there via `quarto add`, since `quarto add`
vendors only the `_extensions/<name>/` subtree). **Always read that file before
authoring.** This skill does NOT restate its contents — duplicating it would let
the two drift. If the extension is not yet installed, run the **NewDeck**
workflow first; it installs the extension, after which the guide exists locally.

`_extensions/slide-foundation/` also holds `API.md`-style terse reference content
in the repo root (`API.md`) for quick token/element lookup.

## The one-paragraph model (orientation only)

A **skin** sets role tokens (`--surface --ink --accent --muted --radius --edge
--shadow --pad --gap --motion-* --weight-*`) on `:root` via `include-in-header`.
Every element and every themed toolbox reads only those tokens, so setting the
skin reskins the whole deck. You set tokens and compose elements; you never write
component CSS in a deck. Everything past this paragraph is in the canonical guide.

## Non-negotiable rule: verify by rendering

Inserting an element is not the same as it rendering correctly. Never claim a
visual result you have not seen. Render, screenshot **every** slide headless,
look at the pixels, and check each toolbox against its failure signature (table
in the guide / `Workflows/Verify.md`). Grep and element-counts do not catch the
bugs this stack actually produces (icon fallback text, mermaid theme override,
`.vcenter` no-op, OS-white code window).

## Workflow routing

| Trigger | Workflow | What it does |
|---|---|---|
| "scaffold a deck", "new deck on the foundation", "add slide-foundation" | `Workflows/NewDeck.md` | Install the extension, wire the format, create a minimal renderable deck |
| "make a skin", "reskin", "set the tokens", "light/dark variant" | `Workflows/NewSkin.md` | Author a `:root` token head file, WCAG-check the colours, wire `include-in-header` |
| "verify the deck", "does it render", "check every slide", "diagram looks off" | `Workflows/Verify.md` | Render, headless-screenshot every slide, diagnose against the failure-signature table |

## Relationship to other skills and to AGENTS.md

- **`AGENTS.md` (in the extension)** is the tool-agnostic portable floor: any AI
  or human, any tool, reads it. This skill is the Claude-specific layer on top.
  They share one source of truth — the guide — by reference, not by copy.
- **SlideCraft** owns slide COPY, speaker notes, and visual-richness tiers. Use
  it for what goes ON the slides; use this skill for the foundation MECHANISM
  (tokens, elements, toolbox harmony, render-verify). They compose.
