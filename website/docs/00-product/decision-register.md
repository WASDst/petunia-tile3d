# Registro de decisões — Odin (11/10/2026)

> Rebaselining prospectivo. Mantemos decisões de produto anteriores que são independentes de linguagem; revisamos contratos técnicos que pressupunham Rust/Slint.

| ID | Área | Situação | Decisão / contrato |
|---|---|---|---|
| T3D-001 | Produto | **Aprovado** | Produto independente em WASDst/petunia-tile3d; não migrar Petunia3D |
| T3D-002 | Linguagem | **Aprovado para PoC** | Odin como linguagem principal e única do protótipo |
| T3D-003 | Janela/3D inicial | **Substituído por T3D-023** | Raylib era candidato histórico; baseline agora é SDL3 + OpenGL3 Core |
| T3D-004 | GUI | **APROVADO 11/10/2026** | Dear ImGui com **docking completo desde a primeira versão**, via binding Odin; Capati é candidato preferencial a teste |
| T3D-005 | Dados | **Aprovado** | TileRegion + faces reais + UV por canto + provenance de tile |
| T3D-006 | Estado | **Aprovado** | Single-writer, Commands, Undo transacional, previews isolados |
| T3D-007 | Persistência | **Proposta para PoC** | JSON versionado; save atômico; textures relativas ao projeto |
| T3D-008 | Export | **Proposta para PoC** | OBJ/MTL primeiro; GLB em etapa posterior |
| T3D-009 | Acessibilidade | **Requisito** | Teclado, foco, alto contraste, escala e alternativa para drag |
| T3D-010 | Plataformas | **Requisito** | Windows e Linux; hardware modesto, offline-first |
| T3D-011 | Reuso | **Aprovado** | Reusar conhecimento/contratos; copiar/portar código só após licença+auditoria |
| T3D-012 | Escopo de prova | **Aprovado como direção** | A: Palette/Brush; B: Sticky/Wall/Undo; C: Save/OBJ |
| T3D-013 | Licença | **Pendente** | Website GPL-3.0-or-later; executável Odin não definido |
| T3D-014 | UI declarativa Slint | **Substituída** | Baseline Rust/Slint de 09/10 não é mais a stack pretendida |
| T3D-015 | Pixel Density Doctor | **Aprovada para V1** | Diagnóstico e reparo explícito, não implementado |
| T3D-016 | Surface Tile Stamp | **Aprovada para V1** | Aplicação de atlas diretamente a face, não implementado |
| T3D-017 | Select/Replace Similar | **Aprovada para V1** | Batch com preview/contagem e um Undo, não implementado |
| T3D-018 | Smart Wall/Roof | **Aprovada para V1+** | Wall primeiro; Roof após contratos geométricos, não implementado |
| T3D-019 | Tile Variations | **Aprovada para V1** | Variantes determinísticas versionadas, não implementado |
| T3D-020 | Multi-viewport OS | **Pendente de validação** | Docking interno é obrigatório; painéis em janelas nativas independentes exigem teste SDL3/GL e Wayland |
| T3D-021 | Addons | **Arquitetura planejada, implementação pendente** | Contratos para ImGuizmo, ImPlot, atlas/UV, nodes, timeline e scripting; incluir somente após uso e validação |
| T3D-022 | Stack de UI a longo prazo | **Aprovado como planejamento** | PanelRegistry, UiIntent, InputRouter, ThemeTokens, FocusRegistry e pontos de extensão ImGui, sem dependências instaladas |
| T3D-023 | Renderer + plataforma | **APROVADO 11/10/2026** | SDL3 + OpenGL 3.3 Core como backend inicial; FBO offscreen para viewport; ImGui SDL3/OpenGL3; binding Odin sujeito a build |
| T3D-024 | ImGuizmo | **Recomendado, integração pendente** | cimguizmo, view/projection, snap, ToolSession e único Undo por gesto |
| T3D-025 | Janelas OS independentes | **Não obrigatório na V1** | Docking interno obrigatório; multi-viewports OS opcionais e sujeitos a testes X11/Wayland |
| T3D-026 | Pipeline GL | **Contrato aprovado** | OpenGL >=3.3 Core, GLSL 330, FBO RGBA8+depth quando suportado, atlas nearest, erro explícito sem GL3.3 |

## O que está implementado?

**Somente o website e documentação.** Não há evidência de uma aplicação Odin executável, viewport, GUI ou testes gráficos neste repositório.

## Como alterar uma decisão

Registrar o problema, alternativas, trade-offs, evidência, implicações de acessibilidade, custo de manutenção, mudança de status, data e riscos de migração. Pesquisa **não é aprovação**.

## Próxima decisão compartilhada

A arquitetura da stack futura está detalhada em [Stack completa de UI](../07-ui/imgui-stack-complete.md). **SDL3 + OpenGL3 já aprovado**: [ADR](../13-engineering/graphics-backend-decision.md), [integração](../13-engineering/sdl3-opengl3-integration.md), [viewport](../07-ui/viewport-framebuffer-contract.md) e [gizmo](../07-ui/imguizmo-integration.md). Próxima discussão: coordenadas 3D, eixos, handedness e unidades; acessibilidade continua gate independente.
