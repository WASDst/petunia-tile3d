# Petunia Tile3D — construção de produto

**Crie pequenos mundos tridimensionais a partir dos seus tilesets.** Uma ferramenta desktop especializada em desenhar superfícies e cenários 3D diretamente de texturas pixel art, sem obrigar o usuário a dominar UV mapping e modelagem complexa.

> **Status:** produto novo, especificação inicial em construção (2026-10-09). A arquitetura-base deste caderno é recomendação para implementação; as funcionalidades diferenciadoras da pesquisa são **hipóteses para debate**, não promessas.

## Cinco compromissos

1. **Tile-first:** escolha uma região de imagem e construa, não crie mesh primeiro para depois mapear.
2. **Cognitive-first:** comandos descobríveis, baixo ruído, ações reversíveis, teclado completo, feedback explícito, sem ambiguidade.
3. **Lean Rust:** resolver algoritmos pequenos com Rust/std e estruturas claras, evitando crates pesadas quando não são necessárias.
4. **Shared math, separate product:** aproveitar interfaces e algoritmos verificados do Petunia3D; não copiar editor inteiro nem exigir Petunia3D instalado.
5. **Interoperabilidade honesta:** exportar meshes/texturas em formato comum; preservar o modelo tile-autoral dentro de formato próprio.

## Três caminhos essenciais

- **Construir:** importar tileset → selecionar região → pintar quad em workplane → conectar pela borda → salvar.
- **Reutilizar:** transformar grupo em peça reutilizável → compor diorama → exportar GLB/OBJ quando implementado.
- **Aprender:** abrir projeto exemplo → seguir interação guiada → usar somente 2–3 ferramentas → expandir controles ao solicitar.

## Leitura orientada

- [Visão de produto e objetivos](./00-product/principles.md)
- [Decisões, limites e itens pendentes](./00-product/decision-register.md)
- [Boundary map](./01-architecture/module-boundaries.md) e [API do motor](./01-architecture/shared-contracts.md)
- [O que reaproveitar do Petunia3D](./02-reuse/reuse-audit.md)
- [Domínio tileset e autoria](./03-domain/tileset-tilemap-model.md)
- [Geometry e UV](./04-geometry/tile-mesh-uv.md)
- [Ferramentas MVP](./05-tools/mvp-tools.md) e [fluxos UX](./07-ui/workspaces-and-shell.md)
- [Acessibilidade e neurodivergência](./08-accessibility/accessibility-contract.md)
- [Formatos e pipeline](./09-project/project-format.md)
- [Qualidade, testes e performance](./11-quality/testing-performance.md)
- [Roadmap](./12-roadmap/milestones.md)
- [Pesquisa concorrencial](./14-research/competitive-landscape.md) e [backlog de hipóteses](./15-ideas/opportunity-backlog.md)
- [Guia para code agents](./16-agents/index.md)

## Fora de escopo por padrão

Nem clone do Blender, nem engine de jogo completa, nem sistema geral de materiais PBR, bones, animação complexa, marketplace ou MCP server na primeira versão. O fato de Crocotile3D ou Petunia3D oferecerem algo não o torna obrigatório aqui.

**Não confundir o caderno com código pronto.** Gates só ficam verdes quando executados e comprovados.
