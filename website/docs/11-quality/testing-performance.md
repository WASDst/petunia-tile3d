# Testes, CI, performance e Definition of Done

> Estado inicial: documentos criados; nenhum teste de aplicativo executado. Estes gates entram em vigor à medida que houver código.

## Gates mínimos por feature

| Área | Verificação |
|---|---|
| Pure Rust/domain | `cargo fmt --check`, `cargo clippy ... -D warnings`, unit + property/regression |
| UV/mesh | triangle winding, zero-area, seams, rect bounds, partial failure rollback |
| Tool input | hover/preview/cancel, click-move-click, keyboard only, Undo/Redo |
| Slint | UI controls accessible tree, focus, theme, resize, shortcut remapping, 200% |
| GL | quad render, context loss/resize, HiDPI, picking, no per-frame CPU readback |
| IO/project | round-trip, missing atlas, corrupt path, atomic writes and recovery |
| Export | GLB validator/engine import fixture, OBJ UV winding, alpha, scale |
| Linux/Windows | build + native smoke on target OS; do not infer from host compilation |

**Status:** `pass/fail/not run/blocked/not applicable`. Link log SHA, command, OS, GPU and result. No fake green.

## Performance baseline — measurement first

- Test documents of 100, 1k, 10k and 50k tile faces; 1–4 PNG atlases; both laptop/desktop.
- Record: input-to-preview ms p50/p95, commit ms, GUI frame ms, GPU memory, atlas upload count, cold start, save/load, export.
- Batching by atlas/material & dirty revisions; no upload on camera-only events, no full image decode on every tile; minimize draw calls.
- Hard numeric budgets only after baseline measured; selecting 60fps by aspiration is not engineering evidence.
- Out-of-memory bounds via import limits; no unbounded buffer allocations from malicious/huge images.

## CI

GitHub workflow to run Markdown/doc integrity immediately, Rust tests once workspace exists, formatting/Clippy, cross-platform compilation, integration screenshots when infrastructure is ready. Docs-only changes must not require GPU runner.

## Unit examples

```text
rect_to_uv: 8×8 at (16,32) within 128×128 -> UV0/UV1 stable
rotations: four quarter-turns -> original mapping
undo: add 50-tile stroke -> undo restores vertices/faces/IDs
sticky: attach to selected edge -> no crack, no duplicate face
missing_atlas: open project -> placeholder + Relink action
read_only_export: export snapshot -> Document revision unchanged
```

## DoD — Implementation Reality

`specified → coded → reachable → exercised → evidenced → verified → accepted → released`. Public documentation must say what stage each feature has reached. A stub or mock is not a finished feature. Review diff for dependency bloat, security, accessibility and architecture before merge.

## End-user testing

Test actual first-session task with artists unfamiliar with 3D. Record instruction prompts, misclicks, terminology confusion, whether users can recover. Treat cognitive load as observable usability, not subjective decorations.

## Matriz de conformidade dos diferenciais aprovados

| Feature | Testes obrigatórios antes de marcar verified |
|---|---|
| [Surface Stamp](../05-tools/surface-tile-stamp.md) | mapping em quads/tri, rotation/mirror, nonplanar reject, custom UV consent, preview=commit, multi-face Undo, export |
| [Replace Similar](../05-tools/replace-similar-tiles.md) | TileId identity, locked/custom skips, scope, missing atlas, stale revision, full rollback, 10k occurrence query |
| [Tile Variations](../05-tools/tile-variations.md) | same seed/id → same tile across OS/reopen/Undo, weights/overflow, preview stable, 1000 variants, no reroll on redraw |
| [Density Doctor](../05-tools/pixel-density-doctor.md) | transform não uniforme, 8/16/32 px, skew/distortion, degenerate face, custom UV, impossible target, query no-write, fix preview |
| [Wall/Roof](../05-tools/smart-wall-roof-brush.md) | grid 1×1/N×M, mirrored frame, roof pitch/overhang, UV seams, duplicate faces, cancel, large atomic Undo, memory cap |

**Ordenação de gates:** primeiro unit de Rust puro (sem Slint/GL), depois Command/Undo + roundtrip, então interação/AT + render, por fim compatibilidade/export/perf. Não adicionar framework de propriedades/geometry/camera automaticamente: somente dependências justificadas e auditadas. Documentar tempo/CPU/arquivo/grafo de dependências antes/depois no vertical slice mais próximo.
