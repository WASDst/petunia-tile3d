# Shared Engine API — superfície pública mínima

> Exemplos Rust são **pseudocontratos**, não código compilado e não devem ser copiados sem revisar owners.

## Tipos puros (exemplo)

```rust
struct TileRegion {
    atlas_id: AtlasId,
    pixel_rect: PixelRect, // [x, y, width, height], integer, in source image
    rotation_quarters: u8, // 0, 1, 2, 3; validate
    mirror_x: bool,
    mirror_y: bool,
}
struct TileStamp {
    region: TileRegion,
    workplane: WorkplaneFrame,
    position: Vec3,
    size_world: Vec2,
}
struct PlacedTileMeta {
    tile_id: TileId,
    source_region: TileRegion,
    edit_policy: TileEditPolicy,
}
```

API alvo deve ter:
- `validate_region(atlas_size, rect) -> Result`: integer overflow, bounds e dimensões positivas.
- `build_quad(stamp) -> Result<GeometryPatch>`: determinismo, orientação e UV calculada sem estado global.
- `attach_edge(mesh, edge_hit, stamp, normal/angle) -> Result<GeometryPatch>`: aderência/snap e deduplicação controlada.
- `batch_patches` e `apply_geometry_patch`: um comando atômico com relatório/Undo.
- `uv_for_corner(region, transformed_corner, sampling) -> UV`: atlas texel center/inset, no drift, seams preservadas.
- `project_tileset_to_faces(face_ids, region) -> Command`: alteração de atributo UV sem modificar topologia.
- `extract_render_mesh(snapshot) -> RenderData` e `export(snapshot, ...) -> Result`: avaliação pura, sem dirty no Document.
- `DocumentRevision` nas queries. Worker descarta resposta antiga quando revision mismatch.

## Reuso com Petunia3D

Algoritmos portados ou extraídos devem manter testes de propriedade (orientation, degenerate faces, UV seam); não assumir que crate Rust no mesmo workspace é automaticamente isolada. `petunia_mesh` hoje tem `geo`, `manifold-rust` e `xatlas-rs-v2`, maiores que o núcleo de um tile editor. Compartilhar **algoritmos e invariantes com interfaces pequenas**, não toda a dependency graph.

## Contratos de estabilidade

- Identidade não é posição de vector (nunca persistir `Vec` index como ID de vida longa).
- Geometry autoral sempre é espaço local e uma só fonte.
- Coordenadas de textura: convenção documentada para origin top-left dos pixels versus UV graphics bottom-left.
- UV face-corner; seam significa mesma posição 3D com UV independente por face.
- Undo por gesto, não por cada pixel/mousemove.
- APIs públicas sem handle GPU nem widget Slint.
- Features sugeridas em pesquisas só viram API depois de decisão documentada e vertical slice.

## Interop contract

GLB/OBJ são **formato de entrega**, não preservam semântica completa de TileRegion, VariantRules ou sessões. Arquivo nativo mantém essa semântica. Um objeto convertido no Petunia3D pode perder binding live, preservando visual/UV/material conforme export; representar perda antes de converter.

## Portabilidade e crates

O contrato da biblioteca compartilhada deve compilar para Linux/Windows sem abrir janela, inicializar GPU, acessar env ou filesystem. Invocar `cargo tree` ao integrar; se transitive dependencies explodirem, avaliar reimplementação Rust local do algoritmo limitado.
