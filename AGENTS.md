# AGENTS.md — Petunia Tile3D, PoC Odin

> LEIA PRIMEIRO. Este é um **produto independente em descoberta**. Documentação não significa implementação. Decisões de 11/10/2026 substituem a antiga baseline Rust/Slint do Tile3D.

## Autoridade

1. Instrução explícita atual do responsável.
2. website/docs/00-product/decision-register.md e website/docs/00-product/principles.md.
3. Documentação contratual por domínio em website/docs/manifest.json.
4. Este AGENTS.md e website/docs/16-agents/ (processo).
5. Código real e testes para comprovar o que existe.
6. Petunia3D, Prumo e referências de mercado: pesquisa, não fonte automática de requisitos.

## Estado e stack

- **Editor a criar:** Odin nativo, tile-first e offline-first, Windows/Linux. Não é migração do Petunia3D.
- **GUI aprovada 11/10/2026:** Dear ImGui com **docking completo desde o primeiro vertical slice**. Capati/odin-imgui é candidato preferencial aos testes de integração, sem declarar compilação validada.
- **Plataforma/renderer:** ainda não escolhidos; SDL3+OpenGL3 é **recomendação para discussão**, alternativas SDL3+SDL_GPU3 e SDL3+WGPU. Ver website/docs/13-engineering/graphics-backend-decision.md. Raylib não é mais a arquitetura obrigatória da GUI.
- **Stack profissional aprovada como plano, não instalada:** PanelRegistry, UiIntent, InputRouter, ThemeTokens, FocusRegistry e extensões encapsuladas. Ver website/docs/07-ui/imgui-stack-complete.md e ui-component-contracts.md; addons são opcionais e exigem ABI/versão/licença/teste.
- **Não implementado:** não declarar o editor, GUI, geometria, export ou testes concluídos sem código e evidência verificáveis.
- **Licença de aplicativo:** pendente. O website derivado tem GPL-3.0-or-later.

## Regras arquiteturais

- Separar domain/geometry, application/commands, renderer, UI/input e project/export por **pacotes Odin**, não crates Rust.
- Geometry Core não conhece raylib, ImGui, janela ou GPU. Uma única representação canônica de FaceCornerUV; TileBinding indica procedência.
- Documento possui mutador único; Tools criam intents, Commands validam e aplicam operações atômicas; renderer somente lê snapshots / buffers derivados.
- IDs estáveis, revisão de documento, invalidação explícita e Undo por gesto. Cancelamento nunca altera o documento.
- Preferir structs + procedures pequenas, dados claros, contexto e allocators explícitos. Evitar estado global mutável, frameworks, ECS, abstrações especulativas e FFI sem necessidade.
- Não copiar código GPL de Petunia3D para editor de licença ainda indefinida; registrar provenance e obrigações antes de qualquer port/cópia.

## Experiência e acessibilidade

- Fluxo tileset → preview → colocar → Undo → salvar → exportar deve ter percurso inteiro por teclado.
- F6 / Shift+F6 como hipótese de navegação entre zonas; foco visível, labels textuais, alto contraste, 100–200% scale e reduced motion.
- Qualquer gesto arrastar terá alternativa por clique/clique ou entrada numérica. Não depender apenas de cor, mouse ou hover.
- Windows/Linux e hardware de entrada precisam de testes reais. Leitor de tela é **risco aberto** e deve influenciar a escolha da GUI.

## Feature status

- PoC: Tile Brush, Sticky Edge, Block, Select/Transform, Tile Palette, Rotate/Flip UV, Undo/Redo, JSON, OBJ/MTL.
- **Aprovadas para roadmap, não implementadas:** Pixel Density Doctor, Surface Tile Stamp, Select/Replace Similar, Tile Variations (V1) e Smart Wall/Roof Brush (V1+).
- Sem assumir implementação por constar em documentos.

## Processo por mudança

1. Confirmar repositório, branch, SHA e fonte canônica relevante.
2. Mapear observado vs planejado: COMPLIANT, PARTIAL, MISSING, BROKEN e testes.
3. Implementar a menor fatia vertical, sem substituir ou contornar Dear ImGui/docking aprovado.
4. Validar formato/compilação/testes em Odin disponíveis, UV, export e a11y manual. NÃO alegar gates que não rodaram.
5. Atualizar página do domínio, decisão e evidência; revisão, SHA, riscos, próximo passo.
6. Não alterar WASDst/petunia3d nem outros repositórios por implicação.

Guia ampliado: website/docs/16-agents/index.md.
