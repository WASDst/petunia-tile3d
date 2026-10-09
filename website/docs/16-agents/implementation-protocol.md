# Protocolo de implementação para LLMs

> Produto novo significa implementação incremental, não "copiar Petunia3D e apagar features".

## Entrada

```yaml
task:
  id: "<issue>"
  goal: "<observable behavior>"
  repo: "WASDst/petunia-tile3d"
  branch: "<confirmed target>"
  docs: ["website/docs/..."]
  target_paths: ["crates/..."]
  non_goals: ["all features outside the vertical slice"]
  tests: ["unit + integration + a11y where relevant"]
```

## 1. Explorar

Confirme HEAD/repo; leia [Fonte Canônica](./reading-navigation.md), [Arquitetura](../01-architecture/module-boundaries.md), código/testes existentes. Faça Gap Matrix e levantamento de dependências. **Se não houver código, declare ausência e implemente a partir do menor contrato**, não um framework universal.

## 2. Desenhar a menor vertical slice

- Primeiro `TileRegion -> FaceCornerUV -> Quad` 100% CPU, testável.
- Depois `Place Tile` via ToolSession/Command com Undo/cancel.
- Depois GL/Slint Go/No-Go integrado.
- Depois `Project` persistente, PNG, GLB.
- Sem plugins, ECS, PBR, camera scripting ou async framework prematuros.

## 3. Implementar

Rust std para algoritmos simples, APIs tipadas, sem duplicar fontes de estado. Snapshot/worker apenas por motivo justificado. Integração de Petunia3D depende de auditoria de licenças/transitives e testes de contrato. Sem `unsafe` desnecessário. UI é declarativa; efeitos só em Application. Todo gesto termina em 1 UndoRecord.

## 4. Revisar

- Logic correctness/edge cases; UV x/y orientation, rotation/mirror.
- Erros e cancel sem mutação, IDs e revisions estáveis.
- Teste com PNG estranho/atlas faltante sem panic.
- Accessibility keyboard only e labels/sliders/focus se UI.
- GPU state, resize, HiDPI e compat hardware para GL.
- `cargo tree` e tamanho binário quando adicionar dependência.

## 5. Comprovar e entregar

Run commands + results on exact HEAD; `pass/fail/not run/blocked`; deferred issues e link para mudanças. Feature descrita na documentação com status verdadeiro. Independent reviewer se disponível; não se autoaprovar nem eliminar gate para green artificial.

## 6. Stop criteria

Nenhuma mudança extraneous, docs atualizados, código compilado e tested quando ambiente disponível, fluxo manual real quando GUI mudou. Onde impossível executar, entregar parcial honesto + próximo comando/sistema de teste.
