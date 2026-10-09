# Roadmap e vertical slices

> Sequência de desenvolvimento sem datas de entrega. O usuário aprovou **incluir cinco funcionalidades** no roadmap (T3D-015..019); o MVP permanece concentrado no básico. Implementação, UX final e cronograma continuam sujeitos aos gates.

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
- [Surface Tile Stamp](../05-tools/surface-tile-stamp.md): aplicar TileRegion a faces com preview, preservando UV e orientação.
- [Select/Replace Similar Tile](../05-tools/replace-similar-tiles.md): localizar ocorrências por binding, substituir em lote, um Undo.
- [Tile Variations](../05-tools/tile-variations.md): variações de tiles com seeds determinísticas e preview estável.
- [Pixel Density Doctor](../05-tools/pixel-density-doctor.md): relatório de densidade e reparos explícitos apenas quando viáveis.
- Rebind manual e UV tool simples permanecem como suporte especializado, sem recriar um UV workspace completo.
- Projeto portátil/recovery, OBJ, relink e atlas consistency.
- Advanced shortcuts/help, tutorial em contexto e pipeline de release.

**Critério:** cenas maiores mantêm boa interação em hardware modesto; problemas export identificáveis e corrigíveis.

## Fase 3 — Construção contextual (V1+ aprovada)

- [Smart Wall/Roof Brush](../05-tools/smart-wall-roof-brush.md): começar por **Wall Strip** de quads com grid/UV previsíveis, seguir com telhado básico em duas águas quando o primeiro vertical slice estiver validado.
- Evitar graph procedural genérico e geometria implícita concorrente com Mesh: resultado é mesh normal + UV explícita, batch Command/Undo.

**Gate:** casa com paredes e telhado texturizados construída com teclado e exportável, sem faces internas/duplicadas, com preview e cancel seguros.

## Fase 4 — Outras hipóteses (ainda não aprovadas)

Autotile contextual de cantos, additional roof presets, sprite export, animated UV tiles, texture editor embutido e outros somente após pesquisa/testes de usuário e nova decisão. [Backlog](../15-ideas/opportunity-backlog.md).

## Explicitamente não priorizar

Rig/skeleton, generalized animation timeline, full PBR/GLTF editor, full Paint stack, fluid simulation, online sharing, marketplace, universal scripting engine e multiuser collaboration. Podem ter valor em outro produto, mas criariam uma superfície desproporcional neste.

## Política de issues

Cada Task: user journey, before/after, source canônica, risk, non-goals, architecture, a11y alternative, tests e acceptance. Mudanças pequenas, verificáveis, com handoff de agents.
