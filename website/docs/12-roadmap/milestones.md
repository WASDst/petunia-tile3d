# Roadmap e vertical slices

> Sequência de desenvolvimento **proposta**, não compromissos de data. Critérios aceitos pelo usuário devem ser congelados em ADR/issue antes de programar.

## Fase 0 — Docs + protótipos CPU

**Entrega atual:** website documental, estudo e decisões preliminares. **Falta:** aplicação.

- Testar `TileRegion`, `PixelRect`, UV orientation, quads e patch Undo em Rust std sem GUI.
- Auditar Petunia3D por função e licenças; medir `cargo tree` antes de compartilhar crate.
- PoC GL 3.3 + Slint (FBO/texture, Linux/Windows); falha documentada leva à decisão de backend.
- Validar requisitos de UI com 1–3 usuários-alvo antes de inflexibilidade arquitetural.

**Gate:** math tests + PoC funcional com um quad e picking; não construir 30 painéis antes deste gate.

## Fase 1 — Primeira criação real (MVP)

- Criar/abrir/salvar projeto e importar PNG atlas.
- Tileset Palette zoom/grid/selection por mouse e teclado.
- 3D viewport renderiza quad, orbit/pan/zoom, grid e hover ghost.
- Tile Brush e Sticky Edge; Select/Move/Rotate/Delete; Flip/Rotate UV.
- Command Undo/Redo e cancel; export GLB mínimo validado.
- Dark/light, keyboard, F6, Contrast, scale 200%, missing atlas case.

**Critério:** casa/prop pequeno criado sem UV manual, salvo/reaberto e exportado; sem regressão keyboard-only.

## Fase 2 — Produto utilizável (V1 candidata)

- Block Brush robusto com Add/Erase validated; multi-face stamps.
- Groups/Parts e Palette tags/favorites; copies e prefabs leves.
- Explicit Rebind/Replace Tile; UV tool simples, Pixel Density Doctor.
- Projeto portátil/recovery, OBJ, relink e atlas consistency.
- Advanced shortcuts/help, tutorial em contexto e pipeline de release.

**Critério:** cenas maiores mantêm boa interação em hardware modesto; problemas export identificáveis e corrigíveis.

## Fase 3 — Diferenciais priorizados por usuários

Hipóteses: regra simples de autotiling, material variations, template de telhado, sprite export, animated tile UV; só priorizar com evidência de necessidade, performance e complexidade.

## Explicitamente não priorizar

Rig/skeleton, generalized animation timeline, full PBR/GLTF editor, full Paint stack, fluid simulation, online sharing, marketplace, universal scripting engine e multiuser collaboration. Podem ter valor em outro produto, mas criariam uma superfície desproporcional neste.

## Política de issues

Cada Task: user journey, before/after, source canônica, risk, non-goals, architecture, a11y alternative, tests e acceptance. Mudanças pequenas, verificáveis, com handoff de agents.
