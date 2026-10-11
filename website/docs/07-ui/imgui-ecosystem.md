# Ecossistema Dear ImGui — Odin e extensões úteis

> Pesquisa 11/10/2026. **Decisão**: Odin + Dear ImGui com **docking interno completo desde o início**. **Não decidido**: backend SDL3/GLFW, renderer OpenGL/WGPU/Vulkan, multi-viewport nativo, bindings para extensões adicionais. Não há editor Odin implementado no repositório; suporte de addon a Odin só é afirmado quando localizado e documentado.

## Legenda

- **O**: binding/implementação Odin encontrado; requer build/integration test no Tile3D.
- **C**: implementação C/C++ com API C ou intermediário C verificado; binding Odin específico **não identificado**.
- **P**: biblioteca C/C++ potencialmente integrável, porém precisa escrever/validar ponte C (ou portar procedimento).
- **H**: referência histórica ou biblioteca voltada a outras linguagens; evitar tratar como solução pronta Odin.

## Implementações Odin e infraestrutura

| Projeto | Papel | Situação | Uso |
|---|---|---|---|
| [Capati/odin-imgui](https://github.com/Capati/odin-imgui) | Dear ImGui v1.92.9b-docking, gera C API via dear_bindings e binding Odin; SDL3, GLFW, OpenGL3, Vulkan, WGPU, DX11 etc | **O — preferido para spike** | GUI/docking completo |
| [L-4/odin-imgui](https://gitlab.com/L-4/odin-imgui) | Outra implementação Odin de Dear ImGui | **O — comparar ABI, build e manutenção** | Alternativa |
| [foxik0169/imgui-odin](https://github.com/foxik0169/imgui-odin) | Gerador binding de cimgui em Odin | **O/H** | Referência tooling |
| [ThisDevDane/odin-imgui](https://github.com/ThisDevDane/odin-imgui) | Binding Odin anterior citado em análises históricas | **H — audit pendente** | Não baseline |
| [alektron/imgui-odin-backends](https://github.com/alektron/imgui-odin-backends) | Backends escritos em Odin; demonstração documentada para Windows + D3D11/GL3/software experimental | **O, suporte de SO limitado pelo material consultado** | Referência; não substituir SDL3 oficial sem motivo |
| [OdinVulkan/libs/implot](https://hilderin.github.io/OdinVulkan/38_implot.html) | Binding Odin manual para cimplot, libs estáticas Windows/Linux | **O — exemplo real** | Metrics/gráficos após MVP |
| [dear_bindings](https://github.com/dearimgui/dear_bindings) | Gerador API C + metadados para Dear ImGui | **Infraestrutura externa** | Regeneração |
| [cimgui](https://github.com/cimgui/cimgui) | API C + metadados Dear ImGui | **Infraestrutura externa** | FFI e addons |

**Atenção:** plataformas/backends disponíveis no projeto Capati não significam que todos foram testados com docking, janelas flutuantes externas, DPI ou acessibilidade em Linux/Windows. Pin da versão e CI nativo são obrigatórios.

## Extensões para o Tile3D — uso após binding Odin

| Projeto | Capacidade | Nível | Interesse / momento |
|---|---|---|---|
| [ImGuizmo](https://github.com/CedricGuillemet/ImGuizmo) + [cimguizmo](https://github.com/cimgui/cimguizmo) | Gizmo 3D mover/rotacionar/escala | **C** | **ALTA** — spike prioritário após docking |
| [imGuIZMO.quat](https://github.com/BrutPitt/imGuIZMO.quat) + [C API](https://github.com/cimgui/cimguizmo_quat) | Controles de quaternion/orientação | **C** | Opcional |
| [ImViewGuizmo](https://github.com/Ka1serM/ImViewGuizmo) | Navegação/orientação da câmera | **P** | Opcional, ou fazer gizmo simples |
| [ImPlot](https://github.com/epezent/implot) + [cimplot](https://github.com/cimgui/cimplot) | Gráficos e plots avançados | **O (OdinVulkan)** | Performance e diagnósticos |
| [ImPlot3D](https://github.com/brenocq/implot3d) + [cimplot3d](https://github.com/cimgui/cimplot3d) | Plot 3D | **C** | Baixa prioridade; não substitui viewport |
| [imgui-node-editor](https://github.com/thedmd/imgui-node-editor) + [cimnodes_editor](https://github.com/cimgui/cimnodes_editor) | Editor de nós complexo | **C** | Fora da PoC |
| [imnodes](https://github.com/Nelarius/imnodes) + [cimnodes](https://github.com/cimgui/cimnodes) | Editor de nós leve | **C** | Fora da PoC |
| [ImNodes antigo](https://github.com/rokups/ImNodes) + [cimnodes_r](https://github.com/cimgui/cimnodes_r) | Outro editor de nós | **C** | Alternativa futura |
| [ImNodeFlow](https://github.com/Fattorino/ImNodeFlow) | Graph/blueprints | **P** | Fora da PoC |
| [ImGuiFileDialog](https://github.com/aiekick/ImGuiFileDialog) | File browser com thumbnails, filtros, custom panes e C API | **C** | **MÉDIA** — avaliar contra diálogo nativo |
| [ImGuiFD](https://github.com/Julianiolo/ImGuiFD) | File dialog minimalista | **P** | Opcional |
| [nativefiledialog-extended](https://github.com/btzy/nativefiledialog-extended) | Diálogo nativo com API C; SDL3 | **C (não ImGui)** | **ALTA** — alternativa acessível para Open/Save |
| [SDL3 File Dialog](https://wiki.libsdl.org/SDL3/SDL_ShowOpenFileDialog) | Janela nativa de abrir/salvar via SDL3 | **O (vendor SDL3, testar declaração exata)** | **ALTA** se SDL3 |
| [ImGuiTexInspect](https://github.com/andyborrell/imgui_tex_inspect) | Zoom, canais RGBA, inspeção por texel | **P, backend GL3/DX11 próprio** | **ALTA** para atlas/pixel art; validar licença/backend |
| [imgui_zoomable_image](https://github.com/danielm5/imgui_zoomable_image) | Zoom/pan de imagem | **P** | Comparar com palette Odin própria |
| [imgInspect](https://github.com/CedricGuillemet/imgInspect) | Visualizador de texturas | **P** | Diagnóstico |
| [goossens/ImGuiColorTextEdit](https://github.com/goossens/ImGuiColorTextEdit) + [cimCTE](https://github.com/cimgui/cimCTE) | Editor de texto com realce de sintaxe | **C** | Scripts/log futuro |
| [Zep](https://github.com/Rezonality/zep) | Editor de texto incorporável | **P** | Fora da PoC |
| [imgui_test_engine](https://github.com/ocornut/imgui_test_engine) | Automação/testes de interação de Dear ImGui | **P; licença própria** | **ALTA** para desenvolvimento |
| [imgui_memory_editor](https://github.com/ocornut/imgui_club) | Inspector hex de memória | **P** | Devtools |
| [ImGui::Metrics/Debugger](https://github.com/ocornut/imgui) | Inspeção interna, demo, debugging e picking UI | **Incluído no core** | **ALTA** em debug builds |

## Widgets, UX e extensões complementares — C/C++ (binding Odin não verificado)

| Projeto | Uso potencial | Prioridade |
|---|---|---|
| [ImGradient](https://github.com/CedricGuillemet/ImGuizmo) | Gradient editor | Baixa / V1+ |
| [ImCurveEdit](https://github.com/CedricGuillemet/ImGuizmo) | Curves/bezier | Baixa / pós-V1 |
| [ImSequencer](https://github.com/CedricGuillemet/ImGuizmo) | Timeline | Fora da PoC |
| [ImCurve](https://github.com/jsoulier/imcurve) | Curve editor moderno | Fora da PoC |
| [ImAnim](https://github.com/soufianekhiat/ImAnim) | Animações de GUI | Baixa; respeitar reduced motion |
| [imgui_toggle](https://github.com/cmdwft/imgui_toggle) | Toggles refinados | Baixa, componente próprio possível |
| [imgui-knobs](https://github.com/altschuler/imgui-knobs) | Controles tipo knob | Não necessário |
| [imspinner](https://github.com/dalerank/imspinner) | Loading spinner | Baixa |
| [ImGui_Arc_ProgressBar](https://github.com/AnClark/ImGui_Arc_ProgressBar) | Progresso circular | Baixa |
| [imgui_markdown](https://github.com/juliettef/imgui_markdown) | Markdown dentro de panel | Média para Help |
| [imgui_md](https://github.com/mekhontsev/imgui_md) | Markdown via MD4C | Média para Help |
| [ImHTML](https://github.com/BigJk/ImHTML) | HTML em ImGui | Não necessário na PoC |
| [ImSearch](https://github.com/GuusKemperman/imsearch) | Busca imediata | Média, command palette própria |
| [ImGuiTextSelect](https://github.com/AidanSun05/ImGuiTextSelect) | Texto selecionável | Baixa |
| [imgui_keyboard](https://github.com/mgerhardy/imgui_keyboard) | Visualização de teclado | Referência para atalhos |
| [ImRAD](https://github.com/tpecholt/imrad) | Designer GUI com geração C++ | Referência visual; não gera Odin |
| [Dear-Design-Manager](https://github.com/Toikron/Dear-Design-Manager) | Design-system tooling | Referência |
| [ImReflect](https://github.com/Sven-vh/ImReflect) | Inspector por reflexão C++ | Inadequado ao domínio Odin sem port |
| [netImgui](https://github.com/sammyfreg/netImgui) | Debug UI remota | Devtool futuro, rede desabilitada em distribuição |
| [imgui_software_renderer](https://github.com/emilk/imgui_software_renderer) | Software raster | Fallback experimental |

Todos os projetos desta segunda tabela são **candidatos para inspeção**, não componentes aprovados, builds comprovados ou bibliotecas com binding Odin garantido. O catálogo [oficial de extensões](https://github.com/ocornut/imgui/wiki/Useful-Extensions) é a fonte de expansão.

## Estrutura-alvo de UI

~~~text
platform (SDL3 ou GLFW, pendente)
  + renderer GPU (OpenGL3 ou WGPU, pendente)
  + odin-imgui (aprovado: Dear ImGui docking)
        ├── DockSpace persistente (exigido na primeira versão)
        ├── Tileset Palette (componente Tile3D)
        ├── Viewport renderizado a texture/FBO (adapter)
        ├── Parts/Inspector (componentes Odin)
        ├── Tools, menus e command search
        ├── File Dialog (SDL3/native ou ImGuiFileDialog)
        └── Gizmo (ImGuizmo via C FFI: spike, não aprovado)
  + Application (Commands/ToolSession/Undo)
  + Geometry/TileRegion/FaceCornerUV em Odin puro
~~~

## Docking ≠ Multi-viewport OS

**Aprovado agora:** DockSpace com divisões, drag/drop, agrupamento por abas, floating dentro da janela, redimensionar e restaurar layout. **Precisa ser testado separadamente:** soltar um painel como janela **nativa** fora da janela principal (ImGui ViewportsEnable), Wayland/X11, monitores de DPI diferentes, mudanças de contexto OpenGL, foco, IME, mínimo e restauração.

## Gates de adoção por addon

1. Licença auditada: **ImGui Test Engine tem licença especial**; disponibilidade gratuita depende de situação do usuário/empresa e do uso.
2. ABI/versionamento iguais a Dear ImGui/C shim (começar com v1.92.9b-docking, se compilável).
3. Windows/Linux e toolchain C/C++ pinadas; binários externos não são baixados sem provenance/checksum.
4. Raw/FFI bridge pequena, structs de layout C, nenhum tipo de addon vazando para Document.
5. Teste de janela docked/undocked, persistência, teclado, DPI, fontes UTF-8, crash/leak e performance.
6. Acessibilidade: keyboard-navigation **não implica** UIA/AT-SPI/leitor de tela. Sem estratégia real de semantic tree, registrar gap; não declarar conformidade.
7. Priorizar core e 1–2 addons com alto impacto. Sem empilhar extensões de editor de nós, curvas e timeline prematuramente.

## Fases propostas (sem implementação existente)

1. Spike Odin + Capati + backend SDL3/OpenGL3 **como candidato**, DockSpace/menus/painel Palette/Viewport textura, layout.ini e 200% DPI.
2. Persistência e input: movimento/resize/undock interno, atalhos e câmera não capturados por widget, focus.
3. Criação de tool panels e inspeção de textura nearest em Odin puro.
4. Teste ImGuizmo/C shim se o authoring necessitar gizmo; arquivo nativo via SDL3 ou NFD; Test Engine condicionado a licença.
5. ImPlot/diagnostics, addons adicionais somente mediante use-case.

## Fonte e limitações da pesquisa

Pesquisa documental, não execução. Fontes: [Odin ImGui](https://github.com/Capati/odin-imgui), [OdinVulkan/ImPlot](https://hilderin.github.io/OdinVulkan/38_implot.html), [CImGui organisation](https://github.com/cimgui), [ImGui Useful Extensions](https://github.com/ocornut/imgui/wiki/Useful-Extensions), [Docking](https://github.com/ocornut/imgui/wiki/Docking), [ImGui Test Engine license](https://github.com/ocornut/imgui_test_engine/blob/main/imgui_test_engine/LICENSE.txt).

**Não existe garantia de completude literal** de todas as bibliotecas comunitárias para Odin; cada nova descoberta deve registrar URL, commit, binding status, requisitos de compilação e integração, licença e teste. O site documental continua sem editor executável.
