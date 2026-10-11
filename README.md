# Petunia Tile3D — Odin proof of concept

**Da imagem 2D à geometria 3D.** Projeto experimental independente da WASD Studio para testar um fluxo de modelagem *tile-first* em Odin.

> Estado em 11/10/2026: **documentação / definição de PoC**. Este repositório contém um website documental; **não há editor Odin funcional publicado ou validado aqui**.

## Direção atual

- **Linguagem do editor:** Odin (decisão de experimento, não migração do Petunia3D).
- **PoC gráfica candidata:** Odin + vendor:raylib. A GUI definitiva **não foi escolhida**.
- **Domínio:** regiões de tileset, faces 3D com UV, workplane, snapping, ferramentas, comandos, Undo/Redo.
- **Primeira entrega:** importar PNG → selecionar TileRegion → criar/editar faces → salvar JSON → exportar OBJ/MTL.
- **Windows e Linux**, hardware acessível, editor offline-first, fluxo completo por teclado e acessibilidade desde a primeira ferramenta.
- Sem ECS universal, sistema de voxels, render PBR, rig, plugins ou importação de todo o Petunia3D na PoC.

## Documentação

O website está em [website/](website/README.md), fonte canônica de produto, contratos, estudos técnicos e diretivas para agentes.

- [Visão e decisões](website/docs/00-product/decision-register.md)
- [Arquitetura Odin](website/docs/01-architecture/module-boundaries.md)
- [Escolha experimental de dependências](website/docs/13-engineering/dependencies-odin.md)
- [Interface — opções em análise](website/docs/07-ui/gui-options.md)
- [Roadmap e gates](website/docs/12-roadmap/milestones.md)
- [Acessibilidade](website/docs/08-accessibility/accessibility-contract.md)
- [Agentes](AGENTS.md)

Website local: python3 -m http.server 8080 -d website, depois abra http://localhost:8080.

## Relação com Petunia3D

Inspiração em conceito e requisitos do [Petunia3D](https://github.com/WASDst/petunia3d) e do histórico do Tile3D. A PoC **não é porte** nem depende do código Rust. Nenhum arquivo do core Rust é presumido reutilizável; eventual port de algoritmo exige análise explícita da licença, proveniência e testes.

## Direitos

O website derivado conserva GPL-3.0-or-later, conforme [website/LICENSE](website/LICENSE) e [NOTICE](website/NOTICE.md). A licença do futuro aplicativo Odin é **decisão pendente**.
