# SDL3 + OpenGL3 — matriz de compatibilidade e verificação

> **Estrutura de testes proposta, não testes executados.** Decisão de backend aprovada. Preencher status com PASS / FAIL / BLOCKED / NOT RUN, versão de driver, SO, Odin, SDL3, ImGui e commit. CI documental não equivale à validação GPU.

## Plataformas

| Sistema | Janela principal | OpenGL 3.3 Core | Docking interno | multi-viewport SO | HiDPI/IME | Status |
|---|---|---|---|---|---|---|
| Windows 10/11 — Intel GPU | Esperado | Exigir query real de GL | Obrigatório | Futuro opcional | Obrigatório | NOT RUN |
| Windows 10/11 — AMD/NVIDIA | Esperado | Query real | Obrigatório | Futuro opcional | Obrigatório | NOT RUN |
| Linux X11 — Intel/AMD Mesa | Esperado | Query real | Obrigatório | Não prometer estabilidade | Obrigatório | NOT RUN |
| Linux X11 — NVIDIA | Esperado | Query real | Obrigatório | Não prometer estabilidade | Obrigatório | NOT RUN |
| Linux Wayland (SDL3) — Intel/AMD Mesa | Esperado | Query real/EGL conforme SDL | Obrigatório | **Não suportado como requisito inicial** | Obrigatório | NOT RUN |
| Linux Wayland (SDL3) — NVIDIA | Esperado, validar driver | Query real | Obrigatório | **Não suportado como requisito inicial** | Obrigatório | NOT RUN |
| Mesa llvmpipe/software | Diagnóstico | Query real | Se estável | N/A | Básico | NOT RUN |

Não alegar compatibilidade por haver suporte teórico à API. GL3.3 Core, contexto principal e dinâmica de DPI devem ser vistos em dispositivo real. Sem GL3.3, oferecer erro claro, não fallback fixo GL2.

## Verificação por componente

| Teste | Fixture | Aceitação | Status |
|---|---|---|---|
| Boot GL3.3 | janela SDL3 + query version/profile | contexto real >=3.3 Core | NOT RUN |
| Backends ImGui | demo + DockSpace | menu, tabs, splits, floating interno persistente | NOT RUN |
| Font atlas 1.92 | PT-BR, tema, resize | glyphs/UTF-8 e scale corretos, textura atualizável | NOT RUN |
| FBO color/depth | quad + grid + clipping | GL_FRAMEBUFFER_COMPLETE, não aparece fora do panel | NOT RUN |
| Resize | 100 ciclos 1×1 → 1920×1080 | sem crash, FBO desatualizado, leak crescente | NOT RUN |
| Alpha/gamma | golden png transparência e palette | comparação visual documentada, sem halo/double gamma | NOT RUN |
| Pixel art atlas | fixture tile com bordas contrastantes | nearest sem bleeding em zoom/rotação e UV flip | NOT RUN |
| InputRouter | click inspector, slider, overlay, gizmo, camera | sem click-through, focus e undo corretos | NOT RUN |
| ImGuizmo | move/rotate/scale em duas cameras | matriz, pivot, local/world e snapping corretos | NOT RUN |
| Keyboard | Tab/ShiftTab/F6/Escape/Enter | fluxo crítico sem mouse | NOT RUN |
| DPI | 100%,150%,200%, monitor cross | pick preciso e tipografia não minúscula | NOT RUN |
| Alt+Tab/minimize | zero pixel, restore | recursos reconstituídos e sem GL inválido | NOT RUN |
| GPU pacing | vsync1/off e idle | medir frame time sem 100% CPU idle desnecessária | NOT RUN |
| Performance | 100/1.000/10.000 faces, 1–2 cameras | registrar CPU/GPU ms e RAM/VRAM, comparar meta 60fps | NOT RUN |
| Build/ship | release Windows/Linux sem rede | dependências/licenças corretas, execução em clean machine | NOT RUN |
| A11y semântica | NVDA/Orca | registrar funcionamento ou limitação explicitamente | NOT RUN |

## Critérios de saída

**F0**: boot Windows+Linux, SDL3/GL3.3, ImGui DockSpace, textura FBO, DPI básico, layout.ini, screenshot visual, relatório de falhas sem crash. **F1**: atlas PNG, 1 quad, camera/picking, nearest, resize, imgui input capture, ImGuizmo integrável, Undo por gesture. **F2**: contexto e múltiplas viewports internas, memória controlada, testes de regressão.

Para Wayland, janelas nativas extra continuam fora do escopo obrigatório: [ImGui multi-viewports](https://github.com/ocornut/imgui/wiki/Multi-Viewports) aponta limitações estruturais. Não misturar janelas do SO com janelas ImGui.

## Registros obrigatórios para cada teste

~~~text
system:
  os / kernel / win-version:
  window-system: X11 | Wayland | Win32
  gpu / driver / mesa:
  gl-vendor / gl-renderer / gl-version / glsl-version:
  SDL3 version:
  Odin version:
  ImGui / Capati / C bridge commit:
  application SHA:
  UI scale / window pixel density / monitor config:
  PASS/FAIL/BLOCKED/NOT RUN:
  screenshot/log/reproduction:
~~~

## Fontes

[SDL3 High DPI](https://wiki.libsdl.org/SDL3/README-highdpi) · [SDL3 Context Creation](https://wiki.libsdl.org/SDL3/SDL_GL_CreateContext) · [ImGui SDL3/OpenGL3 example](https://github.com/ocornut/imgui/blob/master/examples/example_sdl3_opengl3/main.cpp) · [ImGui Multi-Viewports](https://github.com/ocornut/imgui/wiki/Multi-Viewports).
