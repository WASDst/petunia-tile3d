# Application, Commands, ToolSession, Undo/Redo

## Limites

`EditorSession` é transitório (active tile, camera, UI focus, selection, current tool, snap). `Document` é autoral (meshes, tilesets, objects, materials, project metadata). `DocumentSession` faz cache/jobs/recovery. **Single-writer:** UI/worker/renderer não mutam Document diretamente.

## Modelo de Command (alvo)

```text
TilePickIntent
  → TileToolSession(preview)
  → AddTileFacesCommand(validated patch)
  → DocumentTransaction(apply)
  → UndoRecord(inverse patch)
  → DomainEvents(revision, dirty regions)
  → Renderer extraction / GUI status
```

Exemplos de comandos: `AddTileFaces`, `RemoveFaces`, `AttachFaceToEdge`, `ReplaceTileBinding`, `MoveSelection`, `TransformUV`, `ImportTileset`, `RelinkTileset`, `CreatePart`, `GroupParts`, `ExportSnapshot` (read-only).

Um Command não chama outro Command recursivamente para ocultar mutações. Batches transacionais são explícitos. Erros tipados (InvalidRegion, LockedObject, RevisionConflict, UnsupportedTopology, MissingTexture). Retorno contém IDs das entidades alteradas, contagem, warning e invalidation events.

## Gestos

- Preview é imutável em relação ao Document.
- Commit uma única vez ao finalizar click/drag/click-move-click; Esc cancela.
- Undo restaura geometry + TilePlacementBinding + seleção quando semanticamente aplicável.
- Digitado: largura/ângulo/posição validam antes de aplicar.
- Long drag: incremental preview em memória provisória; snapshot + commit único.

## Async/Jobs

Decodificação externa, thumbnail, export e raster podem executar worker em snapshot com CancellationToken e revision; nenhuma thread aplica mudanças no documento sem Command serializado. Descartar resposta de revision antiga. Não permitir salvar project parcial durante transaction sem snapshot coerente.

## Camada query

Queries de snap/hover/pick retornam DTOs sem ponteiros Rust de duração maior que a revisão. Serviços devem ser independentes de Slint. RenderInfo pode cachear revision para reduzir trabalho.

## Padrão de falha

Nenhuma operação silenciosa destrutiva. Erro deve preservar estado anterior e informar causa + caminho de recuperação; status/toast não é único local para problema grave.

## Gates

Testar preview/cancel, Undo/Redo, 100 repetições, zoom/HiDPI, stale response, invalid indices, load/save roundtrip e partial failure rollback.

## Commands/queries das funcionalidades aprovadas

| Feature | Query/preview transient | Mutation | Undo |
|---|---|---|---|
| [Surface Tile Stamp](../05-tools/surface-tile-stamp.md) | `PlanFaceStamp` por FaceId/TileRegion e modo | `StampSurfaceCommand` atualiza FaceCornerUV + binding | estado anterior completo |
| [Replace Similar](../05-tools/replace-similar-tiles.md) | `FindTileOccurrences` (TileId + scope + revision) | `ReplaceTileBatchCommand` | todo lote, nunca por face |
| [Pixel Density Doctor](../05-tools/pixel-density-doctor.md) | `AnalyzePixelDensity` (read-only) e preview fix | `ApplyDensityFixCommand` somente após confirmação/precondições | geometry/UV afetadas exatas |
| [Tile Variations](../05-tools/tile-variations.md) | escolha estável de variante por seed/key | `PlaceVariantTile` / `RerollVariantsBatch` | mesmos TileIds/UV/seed |
| [Wall/Roof Brush](../05-tools/smart-wall-roof-brush.md) | `PlanPatternGeometry`, limite de faces e atlas | `AddPatternGeometryCommand` | todo pattern/parts/UV |

IDs devem ser estáveis; se documento alterou desde preview, recalc + reconfirmação. Queries não alteram Document. `MissingTexture`, `InvalidRegion`, `LockedFace`, `StaleRevision`, `UnreachableDensity`, `UnsupportedTopology` têm mensagens acessíveis com recuperação. Nenhum worker escreve diretamente no Document.
