# Projeto nativo, recursos e salvamento

> Modelo alvo, formato físico ainda a congelar depois do primeiro vertical slice. Não reutilizar diretamente `.petunia` do Petunia3D: os domínios possuem contratos próprios.

## Dados persistidos

Project: `format_version`, app compatibility, objects/mesh, face-corner UV, textures/tileset metadata, TileRegion collection, TileBindings, palettes, groups, optional resource paths, project settings (pixels/world unit, grid units). Não persistir Camera e active selection como verdade autoral; workspace/layout preferences são locais da sessão.

**Escolha preliminar:** pacote `.ptile3d` versionado e portable; decisão entre container zip + manifest e diretório de projeto será tomada após POC de IO simples. Evitar custom binary format complexo antes de medir tamanho/perf. Se usar ZIP, validar traversal, file sizes, ratio bombs, symlinks e count limits.

## Save

Snapshot imutável → staged write para tmp no mesmo volume → sync/flush quando suportado → atomic replace quando API/OS permitir → backup generation → commit clean revision. Se falhar, preservar arquivo anterior e retornar erro contextual.

Autosave: cópia de recovery fora do caminho principal, controle de generations, recover prompt com comparação de timestamps/versions; nunca substituir projeto normal sem escolha.

## Referências

Embeddable PNG default para portabilidade; external linked tileset como opção explícita com path relative e Relink. IDs independentes do caminho. Linked mode não implica executar watcher/network ou sobrescrever PNG sem autorização. Corrupted/missing resource apresenta placeholder e recuperação.

## Compat

- Migração formal `vN -> vN+1` ou read-only onde não suportado.
- Fixtures de projetos com missing atlas, changed dimensions, disconnected UVBinding, unknown extension fields.
- Não afirmar compatibilidade total com `.petunia` do Petunia3D; formatos não são iguais.
- Export GLB/OBJ serve a aplicações externas; preservar dados proprietários apenas no formato nativo.

## Invariantes

Save after Undo produz a mesma geometria e UV, sem referências quebradas; dados de cache/thumbnails não serializados; ids estáveis ao renomear/mover; payload externo validado antes de alocar excessivamente.

## Evolução de formato para os diferenciais aprovados

[Select/Replace Similar](../05-tools/replace-similar-tiles.md) usa `TileBinding` persistente e `FaceId` estável; [Tile Variations](../05-tools/tile-variations.md) requer `VariantGroup` versionado e salva TileId resolvido por face + UV final; [Wall/Roof](../05-tools/smart-wall-roof-brush.md) salva mesh convencional, com recipe somente metadata opcional. [Surface Stamp](../05-tools/surface-tile-stamp.md) persiste UV e provenance explicitamente; [Density Doctor](../05-tools/pixel-density-doctor.md) armazena preferências no EditorSession (não o relatório transitório) e modificações confirmadas como UV/Geometry normais.

**Nenhuma alteração no schema é exigida nesta rodada documental.** Campos novos entram apenas quando o primeiro slice for implementado, com version bump ou `serde(default)` compatível conforme formato adotado e fixtures de reopen, Undo, renderer e export.
