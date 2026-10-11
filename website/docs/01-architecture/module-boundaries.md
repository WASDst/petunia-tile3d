# Arquitetura da PoC — pacotes Odin

> Arquitetura prospectiva. Pacotes representam fronteiras conceituais, **não** um framework nem promessa de arquivos já criados.

## Grafo simplificado

~~~text
main / platform
  ├─ ui          (input, palette, inspector, foco)
  ├─ renderer    (raylib adapter / cache GPU)
  ├─ tools       (Brush, Sticky, Select, Wall; preview)
  │    └─ app    (Command dispatch, Undo, Document writer)
  │         ├─ tile_core   (TileRegion, atlas, UV)
  │         ├─ geometry    (Face, Workplane, placement)
  │         └─ project    (document, IDs, save/load)
  └─ export      (OBJ/MTL sobre snapshot)
~~~

A representação exata pode começar em somente quatro diretórios: core, tools, renderer, app. **Separação física apenas quando melhorar coesão**.

## Regras de dependência

- tile_core e geometry: Odin puro, sem vendor:raylib, GUI, IO de arquivo ou estado de janela.
- tools: preview e intenção; **não implementa algorítmos duplicados**. Tools não chamam tools.
- app: único mutador, transactions e validação; Undo/Redo por gesto, revision incremental.
- renderer: usa snapshot CPU e buffers derivados; não grava UV/Documento.
- ui: apresenta intents e acessibilidade; nunca altera malha diretamente.
- export: recebe snapshot e valida limites, materiais e paths; não usa GPU.
- project: assets, caminho relativos, stable IDs, versão de schema; não grava cache de render.

## Pipeline de um gesto

~~~text
teclado/mouse
 -> ToolSession + preview imutável
 -> Command com parâmetros tipados
 -> validação, commit ou rollback
 -> document revision + changed regions
 -> atualização parcial do cache de render
 -> feedback acessível e um Undo record
~~~

## Dados canônicos

TileRegion descreve pixels do atlas; TileBinding associa face a essa seleção. UV fica sempre armazenada em FaceCornerUV (não recalcular silenciosamente ao editar atlas). Face pode ser quad com vertices[4] na PoC. IDs persistentes e versões permitem evoluir para topologia mais elaborada sem prometer malha half-edge desde o início.

## Memória e concorrência

Preferir dados locais, arrays e alocadores deliberados; documentar ownership, limpeza e vida útil dos buffers. Um mutador do Document inicialmente. Assincronia, workers e batch scheduling somente após profiling.

## Consequências

A PoC pode mudar de raylib para outro backend e manter domínio, ferramentas e serialização. Evitar expor tipos de raylib no formato .json ou na API de comandos.
