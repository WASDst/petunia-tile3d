# Componentes UI Odin — identidade, estado e arquitetura ImGui

> Futuro da UI previsto desde a fundação, **nenhum componente implementado**. Este contrato usa Dear ImGui como backend de apresentação e deixa o documento 3D independente de ImGui.

## APIs entre pacotes

~~~text
app (Document, Selection, Commands, ToolSession)
  ↓ AppSnapshot
ui_model (PanelId, UiIntent, Focus, Preset, CommandRegistry, Theme)
  ↓
ui_components (TilePalette, Viewport, Outliner, Inspector, History...)
  ↓ ImGui adapter / widget wrappers (Odin/C FFI)
platform + graphics (SDL3, renderer ainda pendente)
~~~

A GUI só recebe ponteiros temporários do estado de apresentação; estado documental e commands devem ser referenciados por IDs estáveis e revisões, não ImGuiID.

## Identidade

**PanelId** deve ser estável e independente do nome exibido. Mudar o idioma ou renomear um painel não destrói layout guardado. **WidgetKey** estável por painel+tipo+resource ID; evitar depender de posição em array. **ViewId** diferencia múltiplas vistas do mesmo documento. **DocumentId** é único para save/export. GUI state (painel aberto, search, scroll, zoom preview) não é document state.

## UI Intent

~~~text
UiIntent =
  ActivateTool(tool_id)
  SetToolParam(tool_id, field, value)
  PlaceTile(placement_preview_id)
  SelectEntity(object_or_face_id)
  TransformSelection(delta, coordinate_space)
  OpenPanel(panel_id)
  ApplyPreset(workspace_id)
  Undo | Redo | Save | Export ...
~~~

Esquema conceitual, não sintaxe Odin compilável. Todos os intents recebem validation/error, nenhum widget chama diretamente Geometry mutável. Um click/drag/ação numérica deve produzir um Command ao finalizar; cancel sem commit.

## PanelRegistry

- DockSpace root e presets Build, Edit, UV e Review, preservando posição da viewport utilizável.
- Dock add/tab/split/undock interno, resize, hide/restore, reset via menu e command search.
- Versão de layout para migrações; não gravar dock IDs voláteis no projeto; arquivo usuário separado.
- Ao restaurar layout inválido ou monitor removido, recair no layout padrão e apresentar feedback.
- Barra superior e acesso a painel continuam alcançáveis quando todas as abas são fechadas.

## ComponentRegistry

| Componente | Estados | Keyboard | A11y / label |
|---|---|---|---|
| ToolButton | hover, active, selected, disabled, busy | Tab/Space/Enter/remap | nome da ferramenta + atalho + estado |
| NumericField | idle, hover, editing, invalid, disabled | focus, type, step, commit/cancel | valor, range, unidade e erro |
| DockablePanel | focused, pinned, hidden, floating, collapsed | F6, Tab, context menu | título e papel do painel |
| TilesetPalette | empty, importing, loaded, missing, selected, preview | arrows, Enter, +/- zoom, region coordinates | tile e origem anunciáveis |
| Viewport | focused, camera, tool preview, error, dragging | keyboard cursor / numeric placement | modo, workplane, foco e ações |
| Tree/Outliner | hover, active, selected, multi-select, disabled | arrows, Home/End, Shift, Enter | breadcrumb e quantidade selecionada |
| Dialog | open, validating, error, success, cancellation | Escape, Tab trapping, Enter | título/foco inicial/retorno |
| Toast | info, success, warning, error | dismissal/focus action | causa, consequência, ação |

## InputRouter e coordenação com ImGui

1. Atualizar eventos SDL3 e IO do ImGui; verificar keyboard focus, mouse capture, IME, drag-drop, clipboard.
2. Global commands não roubam caracteres enquanto usuário digita, salvo combinações conscientemente globais.
3. Pointer somente aciona ferramenta 3D quando dentro do ViewportRect e sem widget/modal capturando input.
4. Converter coordenada da tela para coordenada local da viewport, depois framebuffer × DPI, ray/picking.
5. Ao redimensionar/mover dock panel, cancelar drag preview inválido ou manter sob contrato explícito.
6. Mouse-wheel no inspector não muda valor do campo sem foco explícito; scroll no canvas só afeta canvas quando ativo.
7. F6 navega zonas persistentes; Shift+F6 reverso, com foco visível, no Windows/Linux.

## Design system visual

Tokens agrupam Palette, Typography, Density, Spacing, Radius, Animation, Focus, Indicator e Elevation. Tipografia e ícones com escalonamento 100–200%; não usar somente desenho/cor para diferenciar estado. Temas light, dark, high contrast; assinatura visual sóbria, inspirada em editores profissionais mas com UI própria.

Atalhos e command names registráveis; suporte a leituras simplificadas para neurodivergência, reduced motion e densidade reduzida. **Não presumir suporte a NVDA/Orca sem camada semântica real.**

## Contratos de testes

Snapshot de PanelRegistry sem documento, restoring fallback, widgets disabled, numeric locale pt-BR, focus F6, pointer capture, modal, drag cancellation, undo sequence, DPI200, perf em cena e screenshot regressivo. Test Engine é candidato; aprovação de licença e binding/test harness requeridas.
