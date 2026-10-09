# Evidências e handoff entre agentes

## Status de feature

`proposed`, `approved`, `in progress`, `implemented-unverified`, `verified`, `released`, `blocked`. Um arquivo Markdown não move estado automaticamente.

## Verificação

Cada gate recebe `pass`, `fail`, `not run`, `blocked`, `not applicable`, com motivo. Não declarar unit tests aprovados só porque o CI está configurado.

## Handoff model

```yaml
repo: WASDst/petunia-tile3d
branch: main
head: "<confirmed SHA>"
task: "<ID>"
objective: "..."
canonical_docs: ["website/docs/..."]
scope: ["crates/..."]
implemented: ["actual call paths, not promises"]
tests:
  - command: "cargo test -p ... "
    status: "not run"
    reason: "application crate not yet implemented"
a11y: "not tested / findings with environment"
perf: "not measured / measured with hardware"
risk: ["..."]
deferred: ["..."]
next_step: "one precise action"
```

## Role handoff

Explorer aponta sources e gaps; Architect define minimal boundary; Implementer altera; Tester reproduz/verifica; Reviewer revisa independente; Accessibility Reviewer/Performance/Security entram por risco; Documentation Maintainer atualiza canônico; Human aceita mudanças de produto e risk tradeoffs.

**Stop conditions:** dados concretos, licenças respeitadas, gates honestos, diffs pequenos, nenhum escopo desconhecido introduzido. Preservar links para arquivos do Prumo individualmente no [catalog](./workforce-index.md).
