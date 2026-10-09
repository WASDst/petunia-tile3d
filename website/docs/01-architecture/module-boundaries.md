# Arquitetura modular e boundaries explícitos

> **Target design, não implementação existente.** Um produto independente sem copiar toda a arquitetura Petunia3D.

## Graph de responsabilidades

```text
petunia-tile3d (desktop binary / composição)
  ├── tile-ui (Slint declarations + typed UI bridge)
  ├── tile-app (Tools/ToolSessions, Commands, Query, Undo, tasks)
  │    ├── tile-domain (Tileset/TileDefinition/TileRegion/TileBinding)
  │    ├── tile-geometry (Mesh/FaceCornerUV, Workplane, snapping, topology)
  │    ├── tile-project (Document, resource IDs, persistence, assets)
  │    ├── tile-export (GLB and OBJ on snapshots)
  │    └── tile-render API (render/pick DTOs)
  └── tile-render-gl (glow; OpenGL 3.3; Slint texture/FBO adapter)
```

**Regra:** dependências sempre apontam para baixo, de aplicação para serviços/tipos puros; nenhuma crate `tile-domain` / `tile-geometry` depende de Slint, glow, windowing, wgpu, filesystem ou UI tokens.

## Crates sugeridas para começar (sem partir cedo demais)

- `crates/tile-core`: tipos fundamentais e math helper leves, `TileId`, `ObjectId`, `Revision`, `Angle` se relevantes.
- `crates/tile-domain`: atlas bounds, palettes, tile definitions, placements/semantic intent, regras puras de UV e tile variants.
- `crates/tile-geometry`: mesh + topology e workplane, spatial hits; **um único owner da malha**, sem editor paralelo.
- `crates/tile-project`: documento versionado e localização segura de recursos.
- `crates/tile-app`: único mutador de `Document`; Commands + Undo; tools sessions, IO orchestration e queries.
- `crates/tile-render`: DTOs/contratos GPU independentes de backend.
- `crates/tile-render-gl`: implementação de rendering, picking, batching, atlas textures e FBO.
- `crates/tile-ui-slint`: GUI declarativa; shell, atalhos e accessibility adapters.
- `crates/tile-desktop`: binário/composition root.

**Critério para juntar**: se `tile-core`, `tile-domain` e `tile-geometry` tiverem menos de dois consumidores ou boundaries artificiais, preferir fusão com módulos Rust; não introduzir 9 crates numa só PR para "ficar modular". A árvore acima é separação lógica, não meta de arquivos.

## Ownership e fluxo

```text
Keyboard / Pointer / a11y action
 → UI Intent (typed)
 → ToolSession (preview transient, locked Workplane + TileRegion)
 → Command validation
 → Document transaction (single writer)
 → Mesh + face-corner UV and resource references
 → revisions / dirty regions
 → render DTO and accessible UI feedback
```

`Renderer` apenas lê snapshots avaliados; GPU cache nunca é serializado. `TilePalette` é apresentação e seleção de `TileRegion`, não novo armazenamento da imagem. `EditorSession` contém seleção, câmera, tool, active tile; `Document` guarda autoral.

## Reutilização não é dependência transitiva incontrolada

- Não fazer `tile-app -> petunia_core -> petunia_ui_slint -> wgpu`.
- Não adicionar `petunia3d` binário ou `crates/ui-slint` como lib do Tile3D.
- Criar extrações estreitas com API pública mínima e licença auditada. Ser compartilhado é *uma propriedade demonstrada por dois consumidores reais*, não objetivo que justifique framework genérico.
- Preferir `Result<T, Error>` e tipos de ID concretos a `Any`, `Box<dyn ...>` para modelar recurso persistente genérico.
- Não manter segundo UVMesh ou pipeline autoral paralelo para operações geométricas.

## Planejamento gradual

**S0** PoC CPU std Rust (TileRegion -> quad + UV) sem Slint/GL. **S1** GUI/GL Go-No-Go com um quad e picking. **S2** Commands/Undo/Doc e 3 ferramentas. **S3** recurso e formatos. Só extrair crates compartilhadas quando chamadas de uso cruzado e testes comprovaram estabilidade.

## Contratos dependentes

[Shared API](./shared-contracts.md) · [Reuso auditado](../02-reuse/reuse-audit.md) · [Data model](../03-domain/tileset-tilemap-model.md) · [Undo](../06-application/commands-undo.md) · [Visualização](../07-ui/workspaces-and-shell.md)
