# Registro de decisões — Odin (11/10/2026)

> Rebaselining prospectivo. Mantemos decisões de produto anteriores que são independentes de linguagem; revisamos contratos técnicos que pressupunham Rust/Slint.

| ID | Área | Situação | Decisão / contrato |
|---|---|---|---|
| T3D-001 | Produto | **Aprovado** | Produto independente em WASDst/petunia-tile3d; não migrar Petunia3D |
| T3D-002 | Linguagem | **Aprovado para PoC** | Odin como linguagem principal e única do protótipo |
| T3D-003 | Janela/3D inicial | **Provisório** | vendor:raylib para minimizar setup; medir limites |
| T3D-004 | GUI final | **Pendente de estudo conjunto** | Não fixar ImGui, microui, Loom, Skald ou SDL3/WGPU agora |
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

## O que está implementado?

**Somente o website e documentação.** Não há evidência de uma aplicação Odin executável, viewport, GUI ou testes gráficos neste repositório.

## Como alterar uma decisão

Registrar o problema, alternativas, trade-offs, evidência, implicações de acessibilidade, custo de manutenção, mudança de status, data e riscos de migração. Pesquisa **não é aprovação**.

## Próxima decisão compartilhada

Confrontar bibliotecas de GUI nativas e interoperáveis de Odin com o protótipo de raylib: docking, acessibilidade, input, viewport, distribuição e custo de manutenção. Ver [estudo de GUI](../07-ui/gui-options.md).
