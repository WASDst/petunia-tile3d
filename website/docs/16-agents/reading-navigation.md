# Navegação canônica para LLMs

## Autoridade

1. Pedido explícito autorizado do usuário.
2. [Decisões Tile3D](../00-product/decision-register.md) e contratos do domínio.
3. [AGENTS.md](https://github.com/WASDst/petunia-tile3d/blob/main/AGENTS.md) para processo e limites.
4. Código e testes reais para status, nunca para revogar decisões de produto.
5. [Petunia3D refactor](https://github.com/WASDst/petunia3d/tree/refactor/architecture-foundation/website/docs) e [Prumo](https://github.com/poppy-lat/prumo/tree/main/src/prumo/resources/workforce) para referência, não canonicidade.
6. Concorrentes, pesquisas e discussões como evidências com data/URL, nunca como ordem.

## Progressive Context

```text
L0 user goal + AGENTS.md + decisions
L1 selected domain in manifest + relevant section
L2 actual code, callsites, unit/integration tests
L3 affected contracts, dependencies, licences
L4 selected Prumo AGENT.md/SKILL.md/RECIPE.md
L5 related docs only if a conflict demands them
```

Não carregar livros inteiros, `docs/bible` da outra aplicação ou 189 SKILL.md por default.

## O que mapear antes de editar

- Objective medível; non-goals claros.
- Branch/HEAD e estado de trabalho.
- Files, symbols, exposed interfaces e upstream/downstream.
- Requisitos UI/keyboard/UV/Undo/IO envolvidos.
- Source links que comprovam decisões e realidade.
- Testes que confirmarão cada critério.
- Quais dependências seriam novas e por quê.

Se contraditório, registrar ambos os lados; não escolher em silêncio. Se feature ausente, não inventar nome de crate ou API para fingir paridade.

## Gap Matrix

`COMPLIANT`: alcançável e testado; `PARTIAL`: parcialmente funcional; `FUNCTIONAL_BUT_DIFFERENT`: funcional fora de contrato; `STUB`: placeholder; `BROKEN`: falha; `DUPLICATED`: autoridades paralelas; `MISSING`: inexistente; `OBSOLETE`: legado sem consumidor após validação.

Nesta etapa docs-only, runtime do produto é `MISSING`, mesmo que projetos de referência tenham features prontas.
