# Estudo de GUI para Odin — DECISÃO PENDENTE

> **Esta página é uma agenda de comparação, não uma seleção final.** A PoC inicial usa raylib como candidata para um viewport simples; a GUI do produto será decidida em conjunto após uma avaliação técnica de bibliotecas e testes reais.

## A necessidade do editor

O Tile3D é um **aplicativo de autoria**, não um jogo. Precisará exibir simultaneamente: atlas 2D com nearest-neighbor, viewport 3D interativo, toolbar, painel contextual, estrutura do projeto, informações de seleção, caixas numéricas, diálogos, command search e feedback acessível.

Uma biblioteca gráfica não oferece automaticamente uma GUI; e uma GUI com docking não implementa automaticamente viewport 3D.

## Shortlist para pesquisa

| Alternativa | Papel potencial | Perguntas críticas | Estado |
|---|---|---|---|
| **raylib + UI mínima** | Base da PoC e medições | Controle de foco e campos? custos de widgets? | Candidata à PoC |
| **Dear ImGui (odin-imgui)** | GUI imediata com docking madura | Bindings, multi-viewport, DPI, acessibilidade semântica? | Avaliar |
| **microui** | UI pequena e nativa via vendor | Árvores/painéis, escala, widgets avançados, teclado? | Avaliar |
| **Loom** | Sistema GUI orientado a editores em Odin | Manutenção, licença, APIs de render, docking real? | Investigar |
| **Skald** | GUI Odin nativa com GPU | Disponibilidade, docking, maturidade e semântica a11y? | Investigar |
| **SDL3 + WGPU + GUI** | Composição de nível mais baixo | Tempo de infraestrutura e estabilidade de glue? | Alternativa técnica |

## Fontes iniciais verificadas

- [Odin vendor collection](https://pkg.odin-lang.org/vendor/) — raylib, SDL3, microui e APIs gráficas.
- [Odin raylib](https://pkg.odin-lang.org/vendor/raylib/) — janela, input e primitivas 3D.
- [Odin WGPU](https://pkg.odin-lang.org/vendor/wgpu/) — renderizador com libs nativas externas.
- [odin-imgui](https://github.com/Capati/odin-imgui) — bindings comunitários para Dear ImGui docking.
- Para Loom e Skald, verificar repositório canônico, licença, exemplos e atividade antes de firmar afirmações.

## Matriz obrigatória de testes

1. **Viewport + atlas:** texturas nearest e picking sem interferência de input.
2. **Docking e layout:** dois painéis redimensionáveis, persistência e restauração.
3. **Campos numéricos:** digitação, keyboard step, drag sensível e validação.
4. **Acessibilidade:** foco visível, Tab/F6, leitor de tela quando suportado, nomes acessíveis, zoom 100–200%.
5. **Feedback:** tooltip, hover, selected, disabled, progress, erro, toast.
6. **Desempenho:** CPU/RAM idle, latência de input, FPS de cena em hardware modesto.
7. **Distribuição:** Wayland/X11, Windows, DPI, bibliotecas dinâmicas, build reproduzível.
8. **Manutenção:** atualizações e wrappers, dependências, permissões/licenças, tooling de testes.

## Regras da decisão

Não comparar apenas screenshots, FPS ou quantidade de widgets. Criar o **mesmo vertical slice** em finalistas e registrar issues reproduzíveis. A GUI escolhida deve se conectar por intents à mesma Application API, sem acoplar Odin Geometry Core ao toolkit.

**Próxima conversa:** pontuar as alternativas por evidência; se nenhuma atender acessibilidade real, considerar composição híbrida ou reduzir escopo — nunca esconder a lacuna.
