# Prumo Workforce — catálogo completo de roles e skills

> **Fonte:** [poppy-lat/prumo](https://github.com/poppy-lat/prumo) / [Workforce](https://github.com/poppy-lat/prumo/tree/main/src/prumo/resources/workforce). Inventário conferido em 2026-10-09: **39 agents, 189 skills, 20 recipes**. O catálogo é linkado, não executado automaticamente; a ligação dos manifestos pertence ao repositório externo.

## Como os code agents devem usar

1. Começar pelas [diretivas desta aplicação](./index.md), decisões do projeto e ContextPack pequeno.
2. Escolher um role primário por tarefa e, quando houver riscos, reviewer independente/especialista.
3. Abrir apenas os `AGENT.md`, `SKILL.md`, `RECIPE.md` pertinentes pelos links oficiais individuais.
4. Examinar manifests/scripts antes de rodar. O repositório/usuário deve autorizar operações; manifests não autorizam capacidades sozinhos.
5. Nunca copiar specs de Petunia3D genericamente: usar [reuso auditado](../02-reuse/reuse-audit.md) apenas para arquitetura, código e licenças explicitamente aprovados.

## Agents e recipes

- [**39 agents (cada AGENT.md e manifest)**](./agents-catalog.md)
- [**20 recipes (cada RECIPE.md e recipe.json)**](./recipes-catalog.md)

## Skills — 189 entradas com links individuais

| Especialidade | Catálogo |
|---|---|
| Clean code, Rust, testes, arquitetura, documentação para LLM, github e orquestração | [Skills — foundations](./skills-foundations.md) |
| Design, UX, acessibilidade, neurodivergência, focus, keyboard, screen readers | [Skills — experience](./skills-experience.md) |
| Engine, tiles, GPU, shaders, assets, geometry, realtime | [Skills — engine graphics](./skills-engine-graphics.md) |
| Segurança de projeto, paths, plugins, IO, MCP, supply chain | [Skills — security](./skills-security-delivery.md) |
| Rust, GLSL, Lua, linguagens e special tooling | [Skills — languages](./skills-languages-specialized.md) |

[Inventário machine-readable e versionado](./workforce-catalog.json).

## Seleção preferencial para Tile3D

- **Every change:** `grounded-implementation`, `implementation-reality-verification`, `clean-code`, `testing-quality`, `lang-rust` sob demanda.
- **UI e interação:** `accessibility`, `keyboard-accessibility`, `cognitive-clarity`, `focus-management`, `screen-reader`, `design-system`, `ui-ux-review`, `design-psychology`.
- **Tile domain:** `editor-tooling`, `architecture-quality`, `rendering-3d`, `shaders`, `performance-native`, `asset-pipeline`, `color-science`, `serialization`.
- **Doc/agents:** `documentation-for-llms`, `lean-progressive-context`, `prumo-navigation`, `prompt-engineering`, `orchestration-multi-agent`.
- **Security:** `untrusted-project-security`, `filesystem-security`, `secure-coding`, `supply-chain-security`.

**Não instalar 189 skills indiscriminadamente**, nem usar toda a equipe de agentes em tarefa simples.
