# Smart Wall / Roof Brush — construção contextual de paredes e telhados

> **Decisão: incluir na trilha V1+ (T3D-018).** Escopo aprovado: **Wall Strip primeiro**, Roof Pattern básico posteriormente. **Não implementado**. Não construir procedural modeling graph, CAD/BIM ou um segundo Geometry Engine.

## Problema e fluxo

Ao montar casas com quads e tiles, repetir parede, janela, beiral e inclinação do telhado consome cliques, aumenta inconsistências UV e dificulta ajustes. Esta ferramenta reduz repetição: selecione padrões de tiles → aponte começo/fim ou informe comprimento/altura → veja uma faixa/estrutura fantasma → confirme → várias faces editáveis e texturizadas são criadas num único gesto.

## Modos incrementais

### 1. Wall Strip (primeiro slice)

**Entrada:** ponto inicial no workplane, direção/segmento, altura, largura de célula, número de colunas/linhas ou comprimento derivado; TileRegion principal, TileRegion opcional para base/topo; alinhamento `inward/center/outward`.

**Comportamento:** gerar uma grade retangular de quads **compartilhando vértices por célula compatível**, com orientation UV consistente e sem faces internas. Não exigir uma mesh por tile; se geometria adjacente tem seam UV, preservar. Ao puxar altura a ferramenta mostra contagem de células/triângulos em preview. Shapes autorais são mesh normais após commit; tool recipe/preset fica como metadata opcional não autoritária.

**Uso teclado:** selecionar `Parede`, informar largura, altura, posição X/Y/Z e direção/ângulo; `Visualizar` → `Criar`. Click-move-click alternativa ao drag; `Esc` cancela.

### 2. Roof Pattern (subslice após Wall Strip)

**Entrada:** selecionar borda/corno da parede ou 2 vértices da cumeeira, altura da cumeeira, overhang, slope angle e tiles `face/top/edge`.

**V1+ inicial:** telhado em duas águas (gable) simétrico sobre base retangular, com subdivisões por tile, pitch e overhang numéricos; faces continuam mesh quads/triangles. Sem general roof solver, arquitetura complexa em L/U ou telhados curvos. Em caso de footprint incompatível, explicar e oferecer modo manual.

**UX:** perfis nomeados `Plano`, `Duas águas` (o tipo plano pode ser resultado do Wall Strip/Surface Stamp); não adicionar uma lista de 20 presets. Preview comunica orientação do tile (seta + fronteira), faces excedentes e escala; explicações `Telhado não encaixa porque...`.

### 3. Smart Edge/Corners (estudo separado)

Após Wall/Roof básico, identificar cantos e sugerir TileRegion específico (corner/edge/center) a partir de regras locais. Não executar automaticamente em topologia arbitrária. **Ainda não aprovado** como solver genérico de adjacência, apenas como hipótese simples em `post-mvp-tools.md`.

## Dados e boundaries

```text
WallRoofIntent {
  mode, workplane, start/end, width, height, cell_size,
  TilePaletteSelection, orientation, alignment, optional recipe
}
  → ToolSession.preview (pure mesh patch + UV + metrics)
  → App.validate (bounds, overlap, locked targets, atlas availability)
  → AddPatternGeometryCommand (one transaction, one Undo)
  → Document.Mesh + FaceCornerUV + TileBindings
```

Os algoritmos de geração (grid, quads, roof triangles, UV assignments) são funções determinísticas Rust/std no domínio geometric. Uma `WallRecipe` é um input de comando e, se persistida no projeto, só metadata rastreável até existir **um gerador paramétrico vivo explicitamente aprovado**. Não manter mesh final e generator concorrendo por verdade nem regenerar ao abrir arquivo.

## Edição, compatibilidade e Undo

- Wall/roof continua editável via Select/Vertex/Edge/Face e Surface Stamp.
- UV não é reconstruída por mudanças em câmera, UI ou atlas. Alterar receitas salvas é nova ação, não mutação retroativa.
- Não gerar quads ocultos/interiores desnecessários; opcional merge entre faces somente com algoritmo provado e UI explícita.
- Lotes muito grandes: limite visível para faces/estimativa memória, preview simplificado, sem UI freeze ou commit parcial.
- Export GLB/OBJ representa geometria real, sem lógica extra de runtime.

## Acessibilidade

Tool com nome literal `Construir parede` / `Construir telhado`, campos numéricos e presets acessíveis, feedback de dimensão real e tiles usados. Para teclado: editar todas as grandezas sem pointer e executar `Preview`/`Confirmar`; anunciar N tiles/faces e erros. Não depender exclusivamente de cor para front/back e roof pitch. Explicações de dois passos, avançado recolhido.

## Critérios e testes

Wall: 1×1, 1×N, N×M, zero dimension, non-integer length, negative normal, world transform, UV seams, atlas 8/16/32, rotation/mirror, variable target pixels/unit, adjacent duplicates, cancellability, save/export, Undo on 500 tiles.

Roof: footprint retangular, pitched angle 0/15/45/80, symmetric ridge, overhang 0/positive, inverted normal, missing tile, triangles at gable ends, orient consistently, constrained memory, exports. Testes garantem que `cancel` nunca deixa faces parciais.

## Trade-offs

Benefício de UX alto, porém maior risco de complexidade que Similar/Variants; fase V1+ impede bloat no MVP. Nenhuma dependência de booleans (`manifold-rust`), gerador procedural universal, sistemas arquitetônicos avançados ou machine learning.
