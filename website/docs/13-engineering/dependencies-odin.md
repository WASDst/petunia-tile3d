# Odin — stack mínima e dependências

> **Prova de conceito greenfield, não migração.** Priorizar pequeno código correto em Odin antes de infraestrutura profissional pesada.

## Baseline da PoC

| Necessidade | Escolha | Estado |
|---|---|---|
| Linguagem e build | [Odin](https://odin-lang.org/) e odin build/test | Definido para PoC |
| Janela/câmera/input/3D | [vendor:raylib](https://pkg.odin-lang.org/vendor/raylib/) | Candidato inicial |
| Matemática | core:math, structs e vetores Odin | Preferencial |
| Mesh, TileRegion, UV, Commands | implementação própria | Contrato definido |
| Leitura PNG / textura | raylib no experimento; validar limites | Candidato |
| JSON | core:encoding/json e schema versionado | Candidato |
| OBJ/MTL | writer pequeno próprio com validação | Candidato |
| GUI full/editor | ainda NÃO escolhida | Estudo aberto |
| GLB | vendor:cgltf como referência; writer futuro a avaliar | Pós-PoC |
| GPU moderna | vendor:wgpu / SDL3 conforme necessidade | Alternativa futura |

## Por que raylib?

O compilador Odin distribui bindings para raylib, incluindo renderização 3D, janela, input e carregamento de textura; reduz o tempo até a primeira cena. O adapter raylib fica fora de geometry/document. Não significa que raylib fornecerá docking, accessibility tree ou widgets profissionais.

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

[GUI shortlist](../07-ui/gui-options.md) inclui raylib minimal, Dear ImGui docking, microui, Loom, Skald e SDL3/WGPU. Comparar evidencia e custo para acessibilidade, não escolher por popularidade.
