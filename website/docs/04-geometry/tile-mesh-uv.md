# Mesh, topology, FaceCorner UV e Pixel Art

> Fundamental para Tile3D: **desenhar tiles produz superfícies 3D reais**, não sprites sobrepostos fora de topologia.

## Representação alvo

```rust
struct FaceCorner {
    vertex: VertexId,
    uv: [f32; 2],
}
struct Face {
    id: FaceId,
    corners: Vec<FaceCorner>, // 3 ou 4 na baseline; n-gon futuro
    material_id: MaterialId,
}
struct EditableMesh {
    vertices: Vec<Vertex>,
    faces: Vec<Face>,
    revision: u64,
}
```

Código ilustrativo: IDs precisam resolver estabilidade/remapping ao excluir faces, não só indices `Vec`. UV está ligada a cada face-corner, nunca só vertex global. Essa estrutura evita `face.verts.len() == face.uv.len()` mantidos manualmente; preservar seams.

## Operações base

- Quad from plane frame + dimensions + orientation.
- Append Face to compatible edge with dedup vertices.
- Remove Face, move vertex, translate/rotate/scale geometry or UV.
- Flip winding versus Mirror UV são ações distintas.
- Split quad to triangles com interpolação UV deterministic.
- Select vertex/edge/face e weld com tolerância configurável.
- Validate geometry: nonfinite, degenerate, self intersection, invalid indices/UV, duplicate faces; não auto-fix silencioso sem explicação.

## Pixel density

Configurar `pixels_per_world_unit` de projeto. Painel mostra indicador de densidade relativa e warnings de faces esticadas. `Keep Pixel Scale` recalcula UV somente quando usuário escolhe política, nunca transiciona aleatoriamente; ao deformar, oferecer Stretch vs Preserve Density.

## Filter e alpha

MVP: nearest sampling default para pixel art, transparent alpha handling, dark/light preview, no forced mipmaps blur. Texturas com atlas bleeding usam inset e, quando necessário, gutters explícitos. `clamp` vs `repeat` por material configura acesso fora de atlas; não presumir que todo atlas é power-of-two.

## Performance

Compartilhar vertex buffers/material por atlas e **batch por cena/objeto/textura**; não instanciar GPU object por face. Não exigir mesh manifold para painéis/decorativos, mas diagnóstico não-manifold deve existir para export, colisão e edição.

## Testes

- UV orientation para 0/90/180/270 e horizontal/vertical flips;
- winding + normals sob mirror e face reversal;
- attach 100 times sem fendas e duplicação silenciosa;
- denormal/NaN, zero-area, invalid width/height;
- UV seam independente em borda compartilhada;
- Undo/replay idempotent por comando;
- raw GL extraction mantém correspondência face-corner ao triangulate.

## Semântica de novos fluxos aprovados

[Surface Tile Stamp](../05-tools/surface-tile-stamp.md) trabalha apenas em atributos UV da face escolhida: conservar winding, normais, topologia e seams. Para n-gons não planares/deformados não fingir projeção perfeita; preview de distorção ou rejeição. [Replace Similar](../05-tools/replace-similar-tiles.md) reusa aplicação de UV com orientação individual persistida.

[Pixel Density Doctor](../05-tools/pixel-density-doctor.md) mede escala a partir de **geometria em mundo + pixels de atlas + UV face-corner**; não inferir densidade somente por rect width ou grid. Escala não uniforme, faces oblíquas e atlas anisotrópico exigem análise dos eixos tangentes; faces degeneradas são reportadas como não mensuráveis. UV não pode escapar de `TileRegion` para fabricar densidade inexistente. Auto fix com limites é distinto da query read-only.

[Wall/Roof](../05-tools/smart-wall-roof-brush.md) produz GeometryPatch com orientação e UV coerentes, sem gerador universal paralelo. [Tile Variations](../05-tools/tile-variations.md) só escolhe TileId determinístico a cada placement e fixa UV, não ativa shader random.

**DoD transversal:** preview/cancel não mutam malha; Commands atualizam geometry/UV/binding em uma única transação, validam revision e permitem Undo integral. Testar export roundtrip visual sem exigir que GLB/OBJ carregue os metadados exclusivos do editor.
