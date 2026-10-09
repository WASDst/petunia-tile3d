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
