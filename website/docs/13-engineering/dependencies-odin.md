# Odin — stack mínima e dependências

> **Prova de conceito greenfield, não migração.** Priorizar pequeno código correto em Odin antes de infraestrutura profissional pesada.

## Baseline da PoC

| Necessidade | Escolha | Estado |
|---|---|---|
| Linguagem e build | [Odin](https://odin-lang.org/) e odin build/test | Definido para PoC |
| Janela e eventos | vendor:SDL3 ou GLFW | **Pendente de teste**; SDL3 é candidato inicial com ImGui |
| Matemática | core:math, structs e vetores Odin | Preferencial |
| Mesh, TileRegion, UV, Commands | implementação própria | Contrato definido |
| Leitura PNG / textura | decoder próprio de plataforma ou vendor adequado | Escolha pendente; manter o domínio isolado |
| JSON | core:encoding/json e schema versionado | Candidato |
| OBJ/MTL | writer pequeno próprio com validação | Candidato |
| GUI editor | [Capati/odin-imgui](https://github.com/Capati/odin-imgui), Dear ImGui docking | **ImGui/docking aprovados**; binding específico condicionado a build |
| GLB | vendor:cgltf como referência; writer futuro a avaliar | Pós-PoC |
| GPU | OpenGL3 ou WGPU via backend ImGui | **Pendente**; OpenGL3 recomendado ao spike inicial |

## Por que Dear ImGui agora?

O escopo passou a exigir **docking completo imediatamente**. Dear ImGui possui essa infraestrutura; raylib sozinho não entrega o mesmo shell. Podemos usá-lo em experimentos independentes, mas a arquitetura da aplicação precisa de um platform/render backend integrado com ImGui. SDL3+OpenGL3 é candidato de menor complexidade, não decisão final. Consulte [estudo de addons](../07-ui/imgui-ecosystem.md).

## Linguagem / padrões

- Pacotes, structs e procedures pequenas e explícitas.
- Ownership de arrays, strings, alocadores e recursos nativos documentados.
- Alocar fora do hot loop quando possível; usar arenas para duração curta, limpar antes de invalidar o ciclo de vida.
- Usar asserts para invariantes internas, validar conteúdo externo e tratar erro nas fronteiras.
- Evitar import por conveniência de engines gerais, ECS, physics, networking e dependency bloat.
- Separar rendering DTO de dados autorais.

## Dependências externas

Antes de adicionar: API/versão instalada, compatibilidade Windows/Linux, licenças, transitive deps, tamanho/build, ciclo de manutenção, testing, descarte e bloqueio de roadmap. Não há obrigação de fazer tudo do zero.

## Compilação inicial sugerida

~~~bash
odin version
odin build ./src -out:build/tile3d
odin test ./src -all-packages
~~~

Esses comandos são **planejamento da estrutura**, não evidência de que o repositório já contenha código compilável. O exato layout de pacotes e flags será atualizado quando a PoC existir.

## Bibliotecas para próxima conversa

[Decisão ImGui](../07-ui/gui-options.md) e [catálogo de addons](../07-ui/imgui-ecosystem.md). Escolher o backend de plataforma e renderer e testar o DockSpace antes de incluir extensões.

## Stack de UI prevista para evolução

Consultar [Stack completa de Dear ImGui](../07-ui/imgui-stack-complete.md) e [contratos de componentes](../07-ui/ui-component-contracts.md). O produto se prepara para ImGuizmo, ImPlot, atlas/UV, nodes, timeline, code editor e testes sem instalar todo addon na PoC. A próxima escolha é [SDL3 + renderer](./graphics-backend-decision.md); **OpenGL3 é recomendação para discussão, não aprovação**.
