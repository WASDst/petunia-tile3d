# Ferramentas do MVP — comportamentos e contratos

> Escopo de implementação **proposto**, ainda não executado. Cada feature deve atravessar UI Intent → ToolSession → Command → Geometry → Render e Document sem bypass.

| Tool | Ação primária | Entrada/controle acessível | Resultado/Undo |
|---|---|---|---|
| **Tile Brush** | posicionar quad em workplane | click-move-click, keyboard virtual cursor, numeric width/height | 1 gesture = 1 command |
| **Sticky Edge** | criar quad pela borda de tile existente | arrows alternam edge, angle input, Enter confirma | dedup IDs, seam UV preservada |
| **Block Brush** | desenhar volume texturizado com shell externo | posição/dimensões numéricas, preview de faces, Enter confirma | 1 patch por caixa, sem N meshes |
| **Select** | selecionar Part/Face/Edge/Vertex com scope | click/Tab/F6, Select Similar depois | seleção UI, sem mutation |
| **Transform** | mover, rotacionar, escalar Part/faces | gizmo + campos numéricos + teclado | geometry patch + Undo |
| **Rotate / Flip UV** | escolher orientação do tile, sem alterar world geometry | botões nomeados e shortcut remapeável | atualiza FaceCorners |
| **Eyedrop Tile** | capturar região/palette de face existente | picker contextual/teclado | apenas EditorSession |
| **Erase / Delete Faces** | remover faces selecionadas ou under brush | preview + confirmação em lote relevante | transacional |
| **Replace Tile UV** | aplicar TileRegion a faces selecionadas | palette / inspector; warning linked/baked | atributo UV + metadata patch |
| **Grid / Workplane** | ajustar plano e espaçamento | toggle, presets e números em world units | preferência da sessão |

## Preview contract

- Fica claro **o que vai acontecer**, onde, com qual imagem/orientação, antes da confirmação.
- Valid = cor + forma/ícone/linha; Invalid = causa explícita, sem silêncio.
- Shift tem alternativa de toggle para usuários incapazes de segurar duas teclas; remap inteiro disponível.
- Left/right mouse shortcuts são sugeridos, nunca o único caminho.
- `Esc` cancela estado antes de deselecionar; desfazer atua sobre operações concluídas.

## Tile Brush

Selecionar tile por click no Tileset Palette define `TileRegion` ativo, visível no cursor ghost. Mouse sobre Workplane ou face produz preview retangular orientado. Clicar confirma; arrastar preenche células deduplicadas e um Undo. Rotate 90º e Mirror são ações com labels; múltiplos tiles podem formar um stamp mas não duplicam fonte de geometria.

## Sticky Edge

Em hover, mostrar borda alvo + seta normal da face + quad futuro. Angle 0/90 ou personalizado; se cálculo gerar face invertida ou duplicada, aviso e bloqueio. Qualquer binding à face existente não muda seus UVs automaticamente.

## Block Brush

Criar caixa com seis faces quad orientadas; seleção TileRegion pode atribuir um tile a todas as faces e oferecer set Top/Side/Bottom em V1. Geometria interna inexistente para reduzir faces. Diferença entre bloco discreto e "voxel world" fica explícita.

## Transform/UV

Uma face já deformada mantém UV explícita. Move geometry ≠ Move UV. Toggle "Preservar escala do pixel" (post-MVP) requer indicação. Numeric inputs respeitam decimal/locale, passo e Undo.

## Palette

Grid opcional (8/16/32/custom), seleção por region integer, zoom nearest, pan, contexto rotate/flip, espaço de busca por nome/tag, thumbnails e indicação do atlas faltante. Apresentar informações relevantes sem sobrecarga.

## Definition of Done

Concluir o fluxo real com teclado, Undo, cancel, save-load, asset missing state, export e testes. Ferramenta sem call site até Document não é funcional. Vide [Gates](../11-quality/testing-performance.md).
