# Stack de interface — Dear ImGui em Odin, do MVP às ferramentas futuras

> **Especificação de arquitetura 11/10/2026.** O usuário aprovou **Dear ImGui + docking completo desde o início** e pediu uma stack auxiliar capaz de sustentar a evolução da GUI. Este documento escolhe a **organização da stack e os pontos de extensão**; **não** afirma que bibliotecas auxiliares foram integradas, que o editor já existe. **Atualização de 11/10/2026:** SDL3 + OpenGL 3.3 Core foram aprovados.

## Princípio: stack completa ≠ incluir dezenas de dependências na PoC

Reservamos contratos e interfaces para tecnologias com alto potencial, mas só linkamos as extensões quando existe funcionalidade/fixture com valor observável. A compatibilidade é uma matriz de versões: Odin, Dear ImGui, dear_bindings/CImgui, backends, C shims, extensões e toolchain de C++ devem poder ser reproduzidos em Windows/Linux.

**Status:**
- **A — Aprovado**: direção de arquitetura, requisito de produto.
- **R — Recomendado**: seleção técnica para primeira prova; condicionada a build e verificação.
- **E — Extensão prevista**: futura, contrato planejado; nenhuma implementação.
- **P — Pendente**: decisões que exigem análise adicional.

## A. Plataforma, shell, comportamento de janela

| Componente | Tecnologia | Status | Razão e contrato |
|---|---|---|---|
| Linguagem, domínio e estado | Odin + core/vendor | A | Single-writer Document, Commands, Undo; sem ImGui no geometry |
| GUI/docking | Dear ImGui docking | A | DockSpace, janelas/abas/divisões/floating interno, salvar e restaurar layouts |
| Binding Odin | [Capati/odin-imgui](https://github.com/Capati/odin-imgui) | R | Declara ImGui 1.92.9b-docking, dear_bindings e backends SDL3, OpenGL, WGPU, Vulkan |
| Geração FFI | [dear_bindings](https://github.com/dearimgui/dear_bindings), [cimgui](https://github.com/cimgui/cimgui) | R | Um conjunto consistente de ABI e gerações; não misturar versões |
| Plataforma, eventos, clipboard, IME | [vendor:sdl3](https://pkg.odin-lang.org/vendor/sdl3/) | A | Compatível com backends oficiais e diálogos nativos; validar Linux/Windows |
| GPU/renderer | OpenGL 3.3 Core / GLSL 330 | A | FBO offscreen + color texture mostrada no ImGui |
| Layout persistence | ImGui ini + schema Tile3D | A | Separar dock layout do projeto autoral; versionar presets; recovery seguro |
| Multi-viewport OS | ImGui ViewportsEnable + backend | P | Requer validação real sob X11/Wayland, monitores DPI heterogêneos e GL context |
| Janelas e diálogos | SDL3 Native Dialog | R | Preferir UI nativa; [NFD Extended](https://github.com/btzy/nativefiledialog-extended) como fallback |
| Docking UX | wrappers Odin da aplicação | A | Default layout reprodutível, reset e save, indicadores de drag, foco, tabs |

**Docking interno já está aprovado e é diferente de multi-viewport do SO.** O último não pode ser presumido no Wayland; manter fallback sempre dentro da janela principal. [Upstream multi-viewport](https://github.com/ocornut/imgui/wiki/Multi-Viewports).

## B. UI e componentes profissionais que serão nossos

Não expor APIs C/ImGui diretamente aos módulos de domínio. Criar pacote **ui_components/** em Odin, com contratos testáveis e widgets normalizados:

| Família | Conteúdo necessário | Realização |
|---|---|---|
| Shell | MenuBar, DockSpace, WorkspaceSwitch, ToolRail, Sidebar, StatusBar | Odin + ImGui core |
| Containers | Panel, ResizableArea, Tabs, TreeView, VirtualList, Table, ContextMenu | Odin + ImGui core |
| Input | NumericField, Vector2/3 Field, Rotation, Slider, ScrubInput, ShortcutRecorder, SearchSelect, Toggle | Odin + ImGui core, sem addon obrigatório |
| Feedback | Tooltip, Toast, PersistentBanner, Progress, ErrorDialog, EmptyState, Loading, Confirmation | Odin + ImGui core |
| Editor | History, Selection Inspector, Parts Tree, Asset List, Favorites, Command Palette, Property Grid | Odin, usando Commands + UiIntents |
| Tile domain | TilesetPalette, RegionSelect, PixelGrid, TilePreview, UVCanvas, UVTransform, SnappingOverlay | Odin + renderer texture |
| 3D | ViewportPanel, GizmoControl, ViewCube, WorkplaneControl, OrientationWidget | Odin + renderer + ImGuizmo opcional |
| Acessibilidade | FocusRegistry, AccessibleName, NarrationEvent, KeyboardRoute, HighContrastTheme, ReducedMotion | Parte própria; adapter semântico ainda em pesquisa |

Contrato detalhado: [UI components and states](./ui-component-contracts.md).

## C. Extensões 3D, tiles e edição

| Lib | Ponte Odin | Status | Adoção |
|---|---|---|---|
| [ImGuizmo](https://github.com/CedricGuillemet/ImGuizmo) via [cimguizmo](https://github.com/cimgui/cimguizmo) | C API identificada, gerar binding Odin | R | MVP: mover/girar/escalar com preview e valores numéricos |
| [ImViewGuizmo](https://github.com/Ka1serM/ImViewGuizmo) | Ponte C própria não confirmada | E | ViewCube/câmera; comparar a um widget Odin pequeno |
| [imGuIZMO.quat](https://github.com/BrutPitt/imGuIZMO.quat) via [cimguizmo_quat](https://github.com/cimgui/cimguizmo_quat) | C API identificada | E | Orientação por quaternion se surgir necessidade |
| [ImGuiTexInspect](https://github.com/andyborrell/imgui_tex_inspect) | Ponte C/integração com renderer a criar | E | Avaliar para pixel inspect, RGBA, zoom, texture UV |
| [imgui_zoomable_image](https://github.com/danielm5/imgui_zoomable_image) | Ponte C própria a estudar | E | Referência para zoom/pan do atlas |
| SDL3/SDL3_image | Odin vendor | R | Load PNG seguro, nearest filtering, atlas source |
| UV/pixel density | Odin próprio | A | Region, sampling, corner UV, seleção e diagnóstico são domínio, não widget terceirizado |
| GPU debug + picker | Odin próprio | A | ID picking / raycast, depth, selection outlines, gizmo capture |

**ImGuizmo é altamente recomendado, não obrigatório para a primeira tela;** validação do ABI/câmera/coordenação do input antes de incluir. ImGuiTexInspect não substitui nossa lógica autoral de tileset/UV.

## D. Ferramentas profissionais e futuras workspaces

| Sistema | Extensões candidatas | Status e regras |
|---|---|---|
| Gráficos de desempenho, profiler, telemetry local | [ImPlot](https://github.com/epezent/implot) via [cimplot](https://github.com/cimgui/cimplot); binding demonstrado no [OdinVulkan](https://hilderin.github.io/OdinVulkan/38_implot.html) | E, V1; binding precisa versão compatível |
| Gizmos e curvas de edição | ImCurveEdit, ImGradient do [ImGuizmo](https://github.com/CedricGuillemet/ImGuizmo), [ImCurve](https://github.com/jsoulier/imcurve) | E, pós-MVP |
| Nós visuais e grafos | [imnodes](https://github.com/Nelarius/imnodes) + [cimnodes](https://github.com/cimgui/cimnodes) | E; preferir para nós simples; domínio de graph Odin independente |
| Nós complexos | [imgui-node-editor](https://github.com/thedmd/imgui-node-editor) + [cimnodes_editor](https://github.com/cimgui/cimnodes_editor) | E, apenas se imnodes não atender |
| Sequencer/timeline/animation | ImSequencer (ImGuizmo) ou sequencer Odin específico | E, condicionado a feature de animação real |
| Editor de código/scripting | [ImGuiColorTextEdit](https://github.com/goossens/ImGuiColorTextEdit) + [cimCTE](https://github.com/cimgui/cimCTE), [Zep](https://github.com/Rezonality/zep) | E, implementação não precisa existir agora |
| Help Markdown | [imgui_md](https://github.com/mekhontsev/imgui_md), [imgui_markdown](https://github.com/juliettef/imgui_markdown) | E, considerar viewer HTML externo se a11y superior |
| Rich content | [ImHTML](https://github.com/BigJk/ImHTML) | E, evitar Web engine desnecessária |
| Busca | Odin CommandRegistry + fuzzy search própria | A para arquitetura; backend de busca a decidir |
| Gradients/colors/materials | ImGradient / color picker core | E, quando existir painting real |
| UI remote debug | [netImgui](https://github.com/sammyfreg/netImgui) | E dev-only, desabilitado em builds distribuídos |
| Debugger/memory | [imgui_club](https://github.com/ocornut/imgui_club), Metrics/Debugger nativo ImGui | R, ferramentas de desenvolvimento |
| Widgets cosméticos | [imgui_toggle](https://github.com/cmdwft/imgui_toggle), [imspinner](https://github.com/dalerank/imspinner), [imgui-knobs](https://github.com/altschuler/imgui-knobs) | E / geralmente dispensáveis: preferir UI própria consistente |

O [catálogo oficial Useful Extensions](https://github.com/ocornut/imgui/wiki/Useful-Extensions) é a lista de descoberta para novas bibliotecas. Para cada uma: licença, commit, API C, compatibilidade ImGui, SOs e teste de build antes de mover de E a R.

## E. Sistema visual de produto

**Identidade proposta: Phosphor Icons** ([MIT](https://github.com/phosphor-icons/core)) com seleção enxuta e **ícones próprios de edição 3D/UV** criados em SVG/licença clara. Empacotar como atlas ou fonte de ícones, com mapeamento semântico; nenhum CSS/CDN em runtime. [Lucide](https://github.com/lucide-icons/lucide) é alternativa ISC para desenho monocromático; não misturar estilos arbitrariamente.

Fontes UI: escolher família permissiva legível com licença auditada; registrar fallback de Unicode, diacríticos pt-BR, glyph ranges, render DPI, emoji não obrigatório e custo de atlas. **Não embutir fonts ou assets de licença indeterminada.**

Design tokens no Odin: spacing, typography, color, semantic states, radius, shadow quando necessário, density (compact/default), UI scale 100–200%. Criar temas Light/Dark/High Contrast com tokens; serializar apenas prefs. **Sem dependência obrigatória de ImRAD**, que gera layouts C++ e é referência de prototipação, não binding Odin.

## F. Interações que evitam dívida técnica

1. **UiIntent** é único elo UI → Application. Callback de ImGui não altera mesh/doc diretamente.
2. **ToolSession** mantém preview não destrutivo; Command único para commit/Undo. Drag/keyboard/numeric executam o mesmo contrato.
3. **InputRouter** decide ordem de captura: modal → item ativo → ImGui → viewport → shortcuts globais. Respeitar WantCaptureMouse/Keyboard, textos/IME e hit-test.
4. **FocusRegistry** mapeia painel/componente/foco e tecla F6 entre zonas. Independente de ID interno gerado por ImGui quando possível.
5. **PanelRegistry** com stable panel IDs e layout migration. Suportar split, tabs, dock, floating, move, reset, persist e auto-recovery.
6. **RenderTexture** explicitamente controla color/depth/samples, tamanho, DPI, alocação, resize e destruição; jamais serializar handle GPU.
7. **AssetPreview** é cache descartável; texto, tooltip e preview não escondem erro crítico.
8. **Undo / clipboard / batch operations** guiados por interface única entre painéis.
9. **CommandPalette** busca commands, panels, ferramentas e configurações; permissões e estado disabled explícitos.
10. **SettingsRegistry** guarda preferências usuário e não cria dependência invertida do domínio para UI.

## G. Testes, A11y e licença

- **[imgui_test_engine](https://github.com/ocornut/imgui_test_engine)**: candidato para testes de docking/GUI. A licença [v1.04](https://github.com/ocornut/imgui_test_engine/blob/main/imgui_test_engine/LICENSE.txt) é **especial**; gratuita se pelo menos uma condição se aplicar (pessoa física, entidade sem fins lucrativos, educação, software derivado open source, ou entidade faturamento < US$2m último ano); nos outros casos licença paga após período de teste. Verificar cenário concreto e preservar notices.
- CI unit de Odin e testes de docs não substituem screenshot de GPU, teste de teclado nem teste de acessibilidade.
- **Dear ImGui não possui, por padrão, árvore semântica equivalente a frameworks a11y nativos.** Pesquisar FFI com [AccessKit](https://github.com/AccessKit/accesskit) (framework multi-idioma sobretudo Rust) ou ponte UIA/AT-SPI; não afirmar binding Odin existente.
- Validação: NVDA/Windows, Orca/Linux, keyboard-only, focus, label, 100%–200% DPI e X11/Wayland. Se não funcionar, marcar **BLOCKED** e planejar alternativa nativa especializada.
- Licenças: projeto web GPL-3.0-or-later, executável Odin pendente. Cada extensão mantém copyright e termos próprios, inclusive fontes, ícones e test engine.
- Não introduzir execução remota de código, webviews ou plugins na GUI sem limites e sandbox próprios.

## H. Adoção em fases

| Marco | Dependências e integração real | Contratos preparados para crescer |
|---|---|---|
| F0 — Shell | Odin + Capati/odin-imgui + **SDL3/OpenGL3 aprovados** (binding a testar); docking, demo, viewport-texture, atlas dummy, focus e INI | UiIntent, PanelRegistry, InputRouter, ThemeTokens, RenderTexture |
| F1 — Modeling MVP | ImGuizmo **se validado**, SDL3 file dialog, PNG, atlas nearest, numeric inputs, panels/command palette | TileRegion, UV, Tools, Undo, keyboard support |
| F2 — Tile V1 | ImGuiTexInspect **se poupar código**; overlays e diagnósticos Odin; opcional ImPlot | Tile variations, Similar, Pixel Density Doctor, UI sem novo workspace obrigatório |
| F3 — Professional | Node graph, curves, timeline, scripting editor **apenas sob demanda** | GraphDomain, AnimationDomain, ScriptDomain independentes de ImGui |
| F4 — Delivery | ImGui Test Engine conforme licença, CI Windows/Linux, accessibility bridge e perf suite | Controles regressivos em todos os workspaces |

**Não existe obrigação de incluir cada addon.** Contratos e seleção futura fazem parte da stack, dependências instaladas só entram após verificação.

## I. Decisão fechada e evolução

**SDL3 + OpenGL3 aprovado**, não compilado. Ver [ADR](../13-engineering/graphics-backend-decision.md), [bootstrap GL3](../13-engineering/sdl3-opengl3-integration.md), [FBO](./viewport-framebuffer-contract.md) e [ImGuizmo](./imguizmo-integration.md). Próxima discussão: coordenadas, handedness, unidades, câmera e regras de snapping.
