# Commands, ToolSessions e Undo — Odin

> O editor ainda não está implementado. Este é o contrato prospectivo da camada de aplicação.

## Uma fonte de mutação

UI, teclado e ferramentas produzem **intents**; somente Application Command pode alterar Document e seus recursos. O renderer recebe snapshots e revisões, não ponteiros mutáveis do domínio.

~~~text
ToolSession (preview transitório)
  ├─ begin (document revision, selection, workplane)
  ├─ update(pointer/keyboard/numeric)
  ├─ cancel → nenhum efeito persistente
  └─ confirm → Command → validate → patch → revision → Undo
~~~

## Comandos iniciais

- PlaceTile; PlaceTilesBatch; RemoveFaces.
- ExtrudeTileEdge / ExtendFromEdge.
- DrawWall / DrawBlock.
- SelectFace / SelectObject (pode ser session-only).
- TransformSelection; RotateUV; FlipUV; ApplyTileRegion.
- ImportTileset; SaveProject e Export podem ser services de IO invocados por action específica.

## Invariantes

- Um gesto = um único Undo record; drag preview não empilha comandos.
- Mesmo command consumido por toolbar, keymap e ações de acessibilidade.
- Revision check rejeita preview obsoleto com mensagem e possibilidade de retry.
- Comando valida IDs, dimensões, UV, fronteira do atlas, colisões/duplicatas e regras de escopo antes de mutar.
- Erro ou Esc não deixa documento parcial; transações commit/rollback.
- Undo restaura document revision, geometria, UV, material e provenance afetadas; não deve alterar câmera sem pedido.

## Tipos Odin

Preferir enums e structs com tags explícitas, sem strings de comando soltas no domínio. Resultados distinguem Ok, InvalidInput, StaleRevision, MissingAsset, Conflict e I/O Error. Dados usados por Undo têm ownership e allocator definidos. Evitar estado mutável global compartilhado.

## Serviços de consulta

Picking/Snapping retornam dados por valor ou IDs/revision, não ponteiros persistentes para buffers que podem ser realocados. Tool não chama outra Tool; extrair operação comum para geometry/application.
