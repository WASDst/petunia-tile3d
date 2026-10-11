# Malha Tile3D, faces e UV por canto

> Contrato de dados da PoC Odin — sem topologia half-edge universal, sem voxels e sem toolkit gráfico no core.

## Uma face é geometria, não voxel

Um tile de atlas é ligado a uma superfície real (quad) definida por quatro posições 3D e quatro UVs. Uma face pode estar em qualquer plano, não necessariamente em cubo ou grade ortogonal.

~~~text
TileAtlas (PNG + width/height)
  └─ TileRegion (x/y/width/height em px)
       └─ TileBinding (atlasId, regionId, rotação/flip de origem)
            └─ TileFace (faceId, 4 x Position, 4 x CornerUV)
~~~

**FaceCornerUV é a verdade persistida.** TileBinding é procedência e não reescreve UV se o atlas mudar sem comando explícito.

## Modelo Odin conceitual

~~~odin
package geometry

Tile_Region :: struct {
    atlas_id:       u64,
    x, y:           int,
    width, height:  int,
}

Tile_Face :: struct {
    id:       u64,
    position: [4][3]f32,
    uv:       [4][2]f32,
    region:   Tile_Region,
}
~~~

Exemplo de contrato; **não compilado ou inserido num editor existente**. Concluir detalhes de IDs, allocator e estruturas compartilhadas durante implementação.

## UV

- Converter retângulo de pixels para UV normalizada pelo tamanho do atlas; documentar origem vertical da imagem versus API de GPU/export.
- Implementar Rotate 90°/180°/270° por permutação dos quatro cantos; Flip horizontal/vertical por permutação definida, sem deformar geometria.
- Coordenadas de atlas fora de limite são erro; nunca corrigir silenciosamente recorte.
- Nearest filtering; avaliar padding, bleeding/mipmapping, seams e resolução de export.
- Operações geométricas (Move/Rotate/Scale) **não** mudam UV automaticamente, a menos que contrato da operação diga exatamente isso.
- Topologia autoral aceita quads; renderer e OBJ triangulam ou escrevem polígonos conforme capacidade com winding validado.

## Validação

Area zero, vertice duplicado, normal invertida, recorte zero, UV NaN/Inf, dimensões inválidas e textura ausente têm tratamento explícito. Testar faces giradas e atlas assimétrico. Alterações retornam remaps ou IDs novos em operações destrutivas.

## Futuro

Não adotar Manifold, xatlas, Boolean generalista ou UV unwrap na PoC. Face split/weld e malha compartilhada podem ser propostos após Sticky e Wall estarem comprovados.
