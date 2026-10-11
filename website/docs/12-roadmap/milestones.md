# Roadmap — Odin Tile3D PoC

> Sem prazos prometidos. Todas as etapas são **planejadas**; o website não comprova implementação do editor.

## A — A primeira face

**Entrada:** novo projeto e PNG. **Saída:** câmera e plano 3D, região de atlas, preview e criação de face com UV correta.

- Odin toolchain documentada, projeto mínimo, janela com Dear ImGui + DockSpace, render de um quad por backend a validar.
- TileRegion em pixels, palette nearest, resize, grid/workplane e preview valid/invalid.
- Seleção e apagamento de tile, Rotate/Flip UV, câmera orbit/pan/zoom.
- Testes CPU de UV e bounds, primeira validação manual de input.

**Go/No-Go:** abrir um tileset de referência e criar dez faces com UV sem distorcer escala.

## B — Construção fluida

**Saída:** criar parede e volume de forma previsível, editar, cancelar e desfazer.

- Sticky/Edge Snap, Wall/Rectangle e Block Brush.
- Select, transformações, snapping numérico, tool sessions.
- Command dispatcher e Undo/Redo por gesto.
- Casos extremos: normal invertida, face degenerada, overlap e uso por teclado.

**Go/No-Go:** criar uma casa low-poly simples sem editor UV.

## C — Um arquivo de verdade

**Saída:** fechar/abrir documento e exportar asset em outro editor.

- JSON versionado com IDs persistentes, textures relativas, save atômico e missing file.
- OBJ/MTL com UV/materiais, nomenclatura e escala testadas fora do Tile3D.
- Render cache por atlas/material; benchmark 100/1k/10k faces no hardware registrado.
- Checklist Windows/Linux, 1366×768, alta escala e comandos acessíveis.

## Estudo independente da GUI

**Dear ImGui com docking completo está aprovado**. O spike deve decidir SDL3/GLFW, OpenGL3/WGPU e binding Odin prático, comprovando split/tab/floating interno, restore layout, viewport-texture, keyboard focus, escala, Linux/Windows. Addons e janelas externas do sistema seguem pendentes de validação.

## V1 — Funcionalidades já aprovadas

- Pixel Density Doctor.
- Surface Tile Stamp.
- Select / Replace Similar.
- Tile Variations.

## V1+ — Construção assistida

- Smart Wall/Roof Brush com paredes primeiro e telhados básicos depois.
- Outros itens permanecem propostas, não compromissos.

## Fora de escopo

Half-edge universal, UV unwrap automatizado, renderer PBR, rigging, animação complexa, scripting/plugins/MCP, multiplayer e voxel world. Somente reabrir com caso de uso comprovado.

## Expansão da infraestrutura UI

A [stack de interface de longo prazo](../07-ui/imgui-stack-complete.md) fica especificada desde o início. F0 entrega DockSpace, PanelRegistry, FocusRegistry, ThemeTokens, UiIntent, viewport-texture e persistência; F1 acrescenta ferramentas/ImGuizmo se validado; F2 pode acrescentar ImPlot/TexInspect; F3 reserva interfaces para Nodes/Curves/Timeline/Scripting, **sem depender delas no MVP**. Separadamente, [plataforma e renderer](../13-engineering/graphics-backend-decision.md) continuam decisão pendente.
