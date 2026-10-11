# Design system e comportamento acessível

A interface deve se parecer com uma ferramenta criativa séria, calma e compreensível, com pouco ruído visual. A identidade visual do **website** é apenas referência editorial; não define a biblioteca do **editor desktop**.

## Componentes conceituais

- AppHeader, WorkspaceSwitch, ToolButton, ToolGroup, TilesetPalette, RegionPicker.
- ViewportSurface, WorkplaneGuide, ToolPreview, SnapHint, SelectionOutline.
- PartsTree, ContextInspector, NumberField, Toggle, CommandSearch, Toast, Dialog, StatusMessage.
- HelpHint, KeyboardOverlay, AccessibleNotification.

Cada componente declara: purpose, label, focus, keyboard interaction, states, empty/error/disabled, density, scale and reduced motion behavior.

## Semântica e foco

- Não depender de ícones isolados sem label/tooltip.
- Exibir foco distinguível de seleção/hover; não trocar papel semanticamente só por CSS.
- Não usar cor como única informação de erro ou estado.
- Sliders e drag numérico também possuem input digitável.
- Um painel recolhido preserva a seleção do documento e devolve foco previsivelmente.
- Toda ferramenta com manipulação por arrasto oferece alternativa keyboard/click-to-click.

## Eventos e feedback

Painéis não executam mutações diretamente. Consumir UiIntent → Command. Error feedback inclui motivo, impacto e ação. Toast reversível não substitui anúncio de erro persistente. Animações discretas e desabilitáveis.

## Gates

Verificar em 1366×768 e com 200% de UI Scale. Teclado-only e teclado remapeado. Leitor de tela deve ser demonstrado ou permanecer registrado como lacuna do Dear ImGui; não prometer compatibilidade que o toolkit não fornece.

## Implementação futura em Odin

Tokens semânticos light/dark/high-contrast, ícones Phosphor de subconjunto local e tipografia licenciada com fallback Unicode. Componentes são wrappers Odin registrados no [contrato de componentes](./ui-component-contracts.md), não widgets isolados espalhados pelos painéis. Usar [stack completa](./imgui-stack-complete.md) como referência. Acessibilidade de leitor de tela continua lacuna técnica a ser validada.
