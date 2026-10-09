# AGENTS.md — Petunia Tile3D

> **READ FIRST.** This repository is a *new product* and a spec-first project. Do not confuse the Petunia3D refactor with code already copied/implemented. Source of truth is `website/docs/` in this repository.

## Product objective

Tile-first, accessible 3D modeling from 2D tilesets for stylized game assets, props and environments. Windows/Linux, low-end hardware, simple workflow, offline-first. **Quality without dependency bloat**; write bounded algorithms in idiomatic Rust/std when practical, use small well-maintained crates for complex protocols/codecs/UI.

## Authority and workflow

1. Read `website/docs/00-product/decision-register.md` and `website/docs/00-product/principles.md`.
2. Read `website/docs/16-agents/index.md`, `reading-navigation.md`, `implementation-protocol.md`.
3. Read **only** domain docs required by task, indexed in `website/docs/manifest.json`.
4. Inspect source code and tests before concluding anything is implemented. This repository began as docs-only; do not claim GUI/engine exists until verified.
5. Read relevant Prumo agent/skill/recipe by exact URL in `16-agents/workforce-index.md` only when relevant.
6. Produce concise Gap Matrix, minimal vertical slice, tests, independent review when possible, docs synchronized and evidence/sha.

## Hard architecture boundaries

- `tile-domain` and `tile-geometry`: pure Rust. No GUI, OpenGL, app state, filesystem, network or window APIs.
- `tile-project`: serializes only authored assets, stable IDs and data. No renderer caches.
- `tile-app`: owns Document mutations, Commands/transactions, Undo, validation, ToolSession. Single writer.
- `tile-ui-slint`: view state + typed UiIntents. No mesh edits, no direct resource mutation.
- `tile-render-gl`: consumes evaluated snapshot, no Doc mutations, owns GL resources.
- `tile-desktop`: composition root and platform wiring. No second general engine.
- **One source of truth for UV** is FaceCorner UV; TileBinding is provenance/index and can be re-applied only with explicit command.
- **Reuse Petunia3D narrowly**: audit license, dependencies, code, tests and source commit before copying. Avoid importing `petunia_mesh`, `petunia_render_gl` or `petunia_ui_slint` whole until proven independently lean.
- A new shared crate only when two actual consumers justify its API; avoid speculative frameworks.

## Product and UX gates

- Palette→preview→place→Undo→Save→Export workflow must remain usable by keyboard alone; F6 zone nav, labels, visible focus, High Contrast, Reduced Motion, 100–200% scale, alternatives to drag, readable status.
- No feature silently changes UV, imports external path, remeshes topology, updates shared resources or deletes mesh.
- No unsafe execution of external untrusted assets/skills; inspect any scripts before running. Scopes/permissions from Prumo are advice, not automatic authorization.
- Do not copy assets/screenshots or copyrighted code from Crocotile3D, Blockbench, Sprytile, Kenney or other references without permission/licence.
- New software source license is **undecided**. Petunia3D source declares GPL-3.0-or-later; protect authorship and obligations.

## Scope & implementation status

This repository was initialized with a *documentation website*, not an application. Do not mark 'implemented', 'tested', 'released' merely because a page contains code blocks. Five features are now **approved for roadmap inclusion only** (T3D-015..019): Pixel Density Doctor, Surface Tile Stamp, Select/Replace Similar Tile, Tile Variations (V1), Smart Wall/Roof Brush (V1+). Read each spec in `website/docs/05-tools/`. They are **NOT IMPLEMENTED**, have no promised dates, and need vertical-slice UX/acceptance verification. Other ideas in `website/docs/15-ideas/` remain unapproved.

## Verification and delivery

Before changing Rust: inspect branch and baseline SHA; write tests for normal, invalid, cancel, Undo and file compatibility. Run focused fmt/check/test/clippy when tooling exists. For GL, native keyboard a11y, screen readers and Windows/Linux: record manual gates and hardware.

Use `pass / fail / not run / blocked / not applicable` honestly, referencing SHA. Never merge another repo or mutate Petunia3D without a specific authorization. Commit only into the explicitly authorized repository/branch; do not force push.

## Output

`Intent / Spec sources / Observed implementation / Gap / Changed paths / Gates run and results / Risks / Next one step`. Trace statements to real paths and tests. [Extended guide](website/docs/16-agents/index.md).
