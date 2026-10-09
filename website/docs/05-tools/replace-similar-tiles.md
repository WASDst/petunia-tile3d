# Select & Replace Similar Tiles — substituição contextual em lote

> **Decisão: incluir na V1 (T3D-017).** Ação deve ser discoverable, reversível, sem alterar geometria além do necessário. **Não implementada**.

## Problema

Uma casa pode possuir dezenas de janelas, telhas e tábuas. Trocar o tile de uma ocorrência hoje implica selecionar cada face e reaplicar UV. O projeto deve oferecer **Selecionar semelhantes** (query read-only) e **Substituir semelhantes** (Command explícito), usando metadados de TileBinding quando presentes.

## Similaridade: sem falsos matches silenciosos

**Default:** similaridade exata por `TileId` **e** referência de atlas/region efetiva, respeitando policy `baked/live-linked` e face customizada. Não usar apenas comparação visual de pixels nem distância numérica de UV como identidade.

Filtros que usuário vê:
- Mesmo tile (default);
- Mesmo tileset + região (quando TileIds diferentes apontam à mesma definição; opcional);
- Mesma tag/coleção (advanced — não default);
- Inclui faces com UV customizadas? default **não**; mostrar contagem separada com opção de selecionar/revisar manualmente.

Escopo: `Seleção / Part / Grupo / Cena`. Bloqueados/ocultos excluídos por padrão; opção explicita incluir visíveis apenas; nunca "replaced all" desconhecido.

## Replace semantics

```text
ReplaceSimilarIntent {source_tile, target_tile, scope, fit_policy, keep_orientation}
   ↓ app Query (stable FaceId + ObjectId, revision; counts/exclusions)
Preview {before/after thumbnails, matched/skipped, warnings}
   ↓ user confirm
ReplaceTileBatchCommand {
  each FaceCornerUV before/after,
  each TileBinding before/after,
  precondition document revision
}
   ↓ one atomic Undo record + invalidation events
```

- **Preservar orientação** de cada ocorrência como default (rotation/mirror), inclusive tiles repetidos em superfícies ortogonais; target region troca tamanho/offset mas não o frame da face.
- Quando tile destino tem proporção/resolução diferente, default `Fit` altera densidade visual, mostrar aviso `pixel scale changes`; modo `Keep Pixel Scale` somente onde atingível, caso contrário **não aplicar** ao subconjunto incompatível sem decisão.
- Faces sem binding válido não são identificadas automaticamente como "semelhantes"; `Select by UV region` futuro é ferramenta separada e mais arriscada.
- Caso a lista de faces mude entre preview e confirmação, recalc + pedir confirmação (revision check); não aplicar seleção stale.
- Undo restaura exatos `TileBindings`, UV, orientação, seleção relevante e contagem; se uma face falha, transação rollback total, salvo se usuário optou por excluir incompatíveis antes.

## UI

- No tileset: menu contextual `Selecionar faces que usam este tile`, `Substituir...`.
- Sobre face: `Selecionar semelhantes` e atalho via Command Search. Inspector mostra **36 encontradas / 2 ignoradas** (número real de query em runtime, não hardcode) e botão revisar.
- Dialog de revisão curto: antes/depois, escopo, opções de orientação/densidade, contagem, preview; Cancel sempre disponível. Para replace múltiplo, status persistente e botão Undo.
- Leitor de tela anuncia `N ocorrências selecionadas`, motivo para faces puladas; painel navegável por teclado, sem depender de cor.

## Testes

Duplicação de part/IDs, atlas copiado/renomeado, UV customizada, mirrored/rotated tile, different ratios, missing atlas, face locked/hidden, ref stale/changed, repeated command, Undo/Redo/Save/Load, 1/100/10k faces e desempenho indexado por TileId conforme necessidade medida. Evitar full-scene scan por frame; executar query on-demand, cache por document revision se medido.

## Dependências

Implementar com `std::collections` (HashMap/HashSet) + loops determinísticos quando necessário; nenhuma engine de busca visual nem shader de análise, nenhuma nova crate por esta feature.
