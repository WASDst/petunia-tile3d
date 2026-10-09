# Dados autorais: Tilesets, TileRegion e TilePlacement

> Contrato de domínio proposto para o MVP. **Tile** significa textura + geometria colocada; não é voxel e não precisa ser um objeto separado para cada face.

## Entidades

- **TilesetResource**: `TilesetId` estável, TextureId, tamanho de imagem em pixels, nome, paths aprovados e decode status.
- **TileRegion**: `TileId` estável, referência TilesetId, `PixelRect{x,y,w,h}`, orientation (rotate quarter/mirror flags), tags e thumbnail derivada.
- **Palette/Collection**: agrupamento/ordem e favoritos; não duplica pixels/Texture.
- **TilePlacementBinding**: opcional `TileId` e parâmetros autorais sobre face(s) criadas; facilita Replace/Select Similar, mas não substitui a UV per-corner como representação visual real.
- **SceneObject**: transform local, geometry source `EditableMesh`, vis/lock/group.
- **Document**: scenes, resources e bindings; seleção ativa/palette/camera/tool pertencem ao EditorSession.
- **GeometryPatch**: mudança mínima e reversível de faces, vértices, corner UV e metadados.

## Pixels e UV

`PixelRect` usa inteiros e origem na esquerda/superior do atlas. Validar `x+w <= atlas.width`, `y+h <= atlas.height` sem overflow. O renderer tem convenção UV explícita; no momento de gerar corner UV calcular transformação + padding optional `pixel inset` para evitar bleeding. Não assume power-of-two nem repete pixels fora de região silenciosamente.

Se a textura mudar de tamanho: manter `TileRegion` em pixels; validar regiões, mostrar avisos/out-of-bounds e oferecer Relink/Repair voluntário. Nunca deslocar tudo em silêncio.

## Bindings vivos (sem duas fontes de verdade)

- `TileBinding` guarda origem, tags, variant ID e politica (linked ou baked).
- A **UV do mesh** é a representação autoral usada para render/export; binding permite re-aplicar ou substituir, mas precisa de transação explícita que atualize corner UV. Não existir segunda UV concorrente.
- Se usuário editar UV manualmente, exibir que binding ficou detached/customized; não reaplicar durante load sem escolha.
- Edges/faces permanecem topologia real; tile não é sempre quad depois de deformações (quad pode virar triangle split com metadata loss parcial).

## Invariantes

Tileset ID único, TileId independente do path, geometria local, FaceCorner UV atômico, referências válidas ou flagged missing, Undo reversível, preview não persistente, export trabalha sobre snapshot, cache GPU/thumbnail reconstruível.

## Partilha e edição

Trocar um TileRegion em palette não repinta automaticamente faces já colocadas sem que o usuário tenha optado por live-linked. V1: default **baked-on-place**, `Replace Tile` explícito; recurso compartilhado não surpreende. Articulamos uma única ação "Replace all matches" mais tarde como hipótese.

## Recortes irregulares

MVP usa regiões retangulares em grid opcional, incluindo 8×8/16×16/32×32 e seleções livres retangulares. Seleção poligonal de sprite e atlas packing/repacking são propostas futuras e não devem contaminar format inicial.
