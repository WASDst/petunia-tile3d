# Biblioteca de prompts — criar Petunia Tile3D

> Use somente o prompt do tipo de tarefa, substituindo `[PLACEHOLDERS]` por paths/criterios reais. Não autoriza copiar código GPL sem auditoria.

## P0 — Construir um vertical slice

```text
Você está implementando o novo software WASDst/petunia-tile3d,
NÃO refatorando Petunia3D. Verifique branch/HEAD.
Leia AGENTS.md, decision-register.md, implementation-protocol.md
e docs relevantes em website/docs/. O repositório começou docs-only.
Objetivo: [BEHAVIOR]. Non-goals: [EXCLUSIONS].
Antes de alterar, produza gap matrix + owner de cada responsabilidade.
Reuse Rust/std quando simples; examine cargo tree/licenças de cada crate.
Implemente uma vertical slice observável: entrada -> ToolSession ->
Command/Undo -> Document -> Render/Export e testes quando aplicável.
NÃO introduza camadas genéricas, ECS, dependências pesadas ou GUI paralela.
Quando UI mudar: keyboard-only, foco, F6, scale, Reduced Motion.
Teste normal, invalido, cancel, Undo, save/load e high-DPI conforme escopo.
Reporte HEAD, arquivos, comandos, resultados e gates não executados.
```

## P1 — Tile Region/UV math

```text
Implementar tipos Rust puros PixelRect, TileRegion e corner UV conversion.
Inputs: atlas WxH, rect X/Y/W/H, quarter turns, flips; output face-corner
UV. Teste overflow, bounds, zero-size, 4 rotations, mirror, UV flip
OpenGL, seams, sampling nearest e degenerate images. Zero Slint/GL/IO
nesta crate. Compare invariantes do Petunia3D sem copiar dependency graph.
```

## P2 — Tile Brush / Sticky

```text
Implementar Tile Brush e Sticky Edge como ToolSession transacional.
Preview read-only, snapping pixel-based, target edge ID, geometry patch,
1 gesto=1 Undo, Esc cancela, click-move-click, numeric angles/lengths.
Teclado seleciona borda/posição sem drag; max tile stamps bounded.
Testar atlas missing, locked object, near-parallel edge, duplicate face,
undo all, no writes from UI. Render só recebe snapshot.
```

## P3 — Slint accessibility

```text
Projetar Shell mínimo BUILD com Palette/Viewport/Properties e nome de
cada ação. F6/Header→Tool→Palette→Viewport→Properties; foco visível,
tooltip por keyboard, High Contrast, Reduced Motion, 100-200% UI scale.
Nenhuma feature se baseia exclusivamente em arrasto/hover/cor.
Slint expõe accessible role/name/value corretos. Não copiar 10k linhas
do app.slint de Petunia3D. Testar com NVDA/Orca quando disponível;
registrar not-run se ambiente real não houver.
```

## P4 — Viewport GL PoC

```text
Conferir docs/07-ui/viewport-renderer.md. Construir Slint + OpenGL 3.3
FBO+texture com um quad do atlas, picking, resize/HiDPI. Zero CPU readback
por frame. Medir latência no hardware real. Não adicionar WGPU/egui por
padrão. Se integração GL falhar, documentar RCA, ADR alternativo e rollback.
```

## P5 — Dependency review

```text
Analisar [CRATE] e seu cargo tree, features, MSRV, licença, manutenção,
security, size, build time, GPU path e ownership. Alternativa Rust/std
somente se não aumentar riscos de parser/protocol/codec. Demonstrar
por que importar petunia_mesh ou render-gl inteiro é insuficientemente
leve no momento. Classificar reuse: borrow concept / extract math /
copy-with-notices / adapt / avoid. Não modificar Petunia3D.
```

## P6 — Feature discovery / pesquisa

```text
Pesquisar demanda verificável para [IDEA] em Crocotile3D, Blockbench,
Sprytile e outros. Separar oficialmente documentado, relatos datados,
inferência e recurso ausente não demonstrado. Propor fluxo UI simples,
algoritmos/estimativa relativos, a11y alternative, testes, tradeoffs
e não objetivos. Registrar SOMENTE como hipótese em docs/15-ideas/,
sem adicionar runtime ou marcar 'approved' até decisão humana.
```

## P7 — Docs for agents / Conformance

```text
Usar documentation-for-llms, cognitive-clarity e lean-progressive-context.
Verificar rota de markdown, manifest.json, llms.txt, links relativos,
no duplicate sources, título estável, no fake tests, pesquisa com URLs.
Produzir matriz status/doc/impl e handoff compacto. Código de aplicação
não é inferido a partir de Markdown.
```

## P8 — Review independente

```text
Atue como reviewer read-only do SHA [COMMIT]. Leia docs canônicos, código,
testes e diff. Encontre bugs, boundary violations, regression, missing
error handling, broken keyboard/a11y, dependency bloat e falhas IO/UV.
Liste P0/P1/P2 com path e evidência, comandos realmente executados,
recomendação mínima e unresolved gates. Não self-approve.
```

## Aplicação

P0 é envelope padrão, com apenas 1–2 prompts especialistas. Sempre registrar non-goals, evitar carregar todos os catálogos Prumo ou o Petunia3D inteiro.

## P9 — Surface Tile Stamp (T3D-016)

```text
Implemente Surface Tile Stamp segundo docs/05-tools/surface-tile-stamp.md
em slice pequeno, após validar FaceCornerUV/selection. O usuário deve
escolher TileRegion, identificar face, ver UV ghost, aceitar Fit/Keep
Pixel Scale e confirmar; sem novo UVMesh ou alteração topológica silenciosa.
UV custom requer decisão explícita. App aplica 1 Command/Undo,
Slint emite UiIntent; testes: face X/Y/Z, flip/rotation, nonplanar,
missing/locked, no mutation on cancel, export.
```

## P10 — Select/Replace Similar (T3D-017)

```text
Implemente read-only query de ocorrências por TileId/binding, scope visível,
exclusão default de UV custom e bloqueados, preview com contagem/causas.
Replace = transação atômica com FaceCornerUV + binding, 1 Undo.
Conflito revision invalida preview. Rust std para index se medido.
Não fazer image similarity nem update automático ao editar TilePalette.
```

## P11 — Pixel Density Doctor (T3D-015)

```text
Primeiro entregue apenas uma query Rust pura para analisar texels por world
unit usando transform/world surface e UV por face-corner, com relatórios
de desvio e degeneração. Não auto-reparar; ao avançar, permitir somente
fixes matematicamente viáveis com preview, confirmação e Undo.
Teste atlas não quadrado, escala não uniforme, UV skew e limites de TileRegion.
Prove que nenhum fix derrama UV para outros tiles do atlas.
```

## P12 — Tile Variations (T3D-019)

```text
Crie VariantGroup com TileIds e pesos, algoritmo inteiro seed/versionado
e choice key por placement ID/cell estável. Preview fixo = commit;
persistir TileId escolhido e FaceCornerUV final. Nunca reroll no render,
abrir arquivo ou export. Sem crate rand salvo justificativa.
Teste cross-platform fixtures, overflow, weighted distribution, Undo e a11y.
```

## P13 — Smart Wall / Roof (T3D-018)

```text
Comece somente com Wall Strip por grid ortogonal de quads usando
Workplane, previews transientes e GeometryPatch/Command atômico.
Dimension numeric + click-move-click, UV orientada, sem faces internas.
Depois que Wall Strip passar testes/a11y, desenvolver gable roof simples;
não adicionar graph procedural/CAD/booleans heavy. Teste cancel,
winding, seams, dimensões degeneradas, export e Undo de grandes lotes.
```
