# AGENTS.md — Slide Foundation

The full authoring guide lives **inside the extension** at
[`_extensions/slide-foundation/AGENTS.md`](_extensions/slide-foundation/AGENTS.md).

It is kept there on purpose: `quarto add` vendors only the `_extensions/<name>/`
subtree, so placing the guide there means it travels to every consumer who
installs the extension. Repo-root docs (this file, `README.md`, `API.md`) do
**not** reach consumers.

## The model in one paragraph

A **skin** sets role tokens on `:root` (via `include-in-header`). Every element
and every themed toolbox (icons, diagrams, code windows) reads only those
tokens, so setting the skin reskins the whole deck. You never write component
CSS in a deck; you set tokens and compose elements. Light/dark is the same
mechanism: flip `--surface`/`--ink`.

Read the vendored guide before building a deck. `API.md` is the terse reference.
