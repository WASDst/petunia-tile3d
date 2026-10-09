# Petunia Tile3D

**Modelagem 3D por tiles, acessível desde o primeiro clique.**

Novo produto da [WASD Studio](https://wasd.lat), tecnicamente relacionado ao [Petunia3D](https://github.com/WASDst/petunia3d), mas com UX, escopo, releases e aplicação próprios.

**Estado:** specification / product discovery. O repositório contém documentação de produto e website; **não existe editor funcional implementado aqui neste momento**.

## Documentação

- [Início do caderno](website/docs/home.md)
- [Site estático](website/README.md) — `python3 -m http.server 8080 -d website`
- [Princípios e decisões](website/docs/00-product/principles.md)
- [Arquitetura e limites](website/docs/01-architecture/module-boundaries.md)
- [Integração com Petunia3D](website/docs/02-reuse/reuse-audit.md)
- [Ferramentas e fluxos](website/docs/05-tools/mvp-tools.md)
- [Acessibilidade](website/docs/08-accessibility/accessibility-contract.md)
- [Research + ideias para discussão](website/docs/14-research/competitive-landscape.md)
- [Code agents](AGENTS.md)

O site usa HTML/CSS/JavaScript sem bundler e Markdown registrado em `website/docs/manifest.json`. Hospedagem pública ainda precisa ser configurada; a existência dos arquivos não implica deploy.

**Licenciamento:** licença da nova aplicação ainda será decidida; reutilização de código GPL-3.0-or-later de Petunia3D exige preservação de obrigações e avaliação antes de copiar qualquer código. Consulte [estratégia de licenças](website/docs/02-reuse/license-provenance.md).

## Licença

A estrutura do **website/** e seus documentos derivados são disponibilizados sob GPL-3.0-or-later — ver [LICENSE](website/LICENSE) e [NOTICE](website/NOTICE.md). O licenciamento do aplicativo Rust que ainda será desenvolvido permanece **pendente de decisão**.
