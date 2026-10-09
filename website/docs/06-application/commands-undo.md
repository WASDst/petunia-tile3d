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
