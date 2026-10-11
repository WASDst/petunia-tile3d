# T3D-023 — SDL3 + OpenGL3 APROVADO

> **Aprovado pelo responsável em 11/10/2026.** Odin + SDL3 para janela/input e OpenGL **3.3 Core Profile** (GLSL 330) para viewport 3D, integrado ao Dear ImGui docking. Especificação, **não compilado ou testado**. O comparativo a seguir é histórico, não decisão em aberto.

## Resultado e consequências

- Backends ImGui `imgui_impl_sdl3` + `imgui_impl_opengl3`. Candidato Odin: Capati/odin-imgui, condicionado a build no Windows/Linux.
- RenderScene GL3 em FBO (color texture RGBA8 + depth), apresentado por ImGui Image. A partir do ImGui 1.92, respeitar **ImTextureRef**.
- DockSpace completo dentro da janela, layouts salvos. Multi-viewport nativo do SO não obrigatório, especialmente Wayland.
- ImGuizmo/cimguizmo **recomendado**; integração e API C ainda não verificadas em código do projeto.
- Alternativas SDL_GPU3 e WGPU não farão parte da primeira implementação; podem ser estudadas futuramente, sem promessa de migração barata.
- Falha em criar GL3.3 Core gera diagnóstico, não fallback silencioso para GL2.

**Implementação especificada:** [Ciclo SDL3/GL3](./sdl3-opengl3-integration.md) · [Viewport/FBO](../07-ui/viewport-framebuffer-contract.md) · [ImGuizmo](../07-ui/imguizmo-integration.md) · [Compatibilidade](../11-quality/graphics-compatibility-matrix.md).

---

## Comparação histórica que precedeu a aprovação


## Escopo correto

1. **SDL3** fornece janelas, eventos, mouse, teclado, cursor, clipboard, IME, dialogs e surfaces — não é renderer 3D por si só.
2. **Dear ImGui** fornece widgets e docking. Seus backends são pares Platform Backend + Renderer Backend.
3. **Viewport 3D** é um renderer de malha/UV do Tile3D que desenha offscreen numa textura apresentada dentro de janela ImGui. Não usar ImGui para renderizar milhares de faces 3D como primitives UI.
4. **Gizmos** usam view/projection matrices da câmera e coordenadas do viewport; não dependem do renderer final quando contratos matemáticos são estáveis.

## As três opções

| Eixo | SDL3 + OpenGL3 | SDL3 + SDL_GPU3 | SDL3 + WGPU |
|---|---|---|---|
| Caminho até primeira viewport | **Curto** com exemplos oficiais | Médio: command buffers/pipelines/shaders | Maior: device/surface/async init/build |
| Backend ImGui oficial | imgui_impl_sdl3 + imgui_impl_opengl3 | imgui_impl_sdl3 + imgui_impl_sdlgpu3 | imgui_impl_sdl3 + imgui_impl_wgpu |
| Quadro offscreen ImGui | GL texture/FBO | SDL_GPUTexture + render pass | WGPU texture view + render pass |
| Hardware legado | **Frequentemente favorável** via GL3; depende de driver | Vulkan/D3D12/Metal; depende de driver/backend | WGPU implementações modernas; suporte variável |
| Shader/modelo | GLSL + stateful GL | SPIR-V/DXIL/MSL ou SDL ShaderCross conforme backend | WGSL e pipeline WGPU |
| Multi-viewport (janelas do SO) | Documentado nos backends GL, mas Linux tem falhas | Precisa prova end-to-end | Precisa prova end-to-end; não presumir |
| Integração addons ImGuizmo | Render agnóstico, API C | Mesmo princípio | Mesmo princípio |
| Complexidade para desenvolver render 3D | Baixa-média | Média | Alta |
| Modernização / compute futuro | GL compute quando disponível; limitado no GL3 baseline | Moderno, próprio SDL_GPU | GPU moderna, foco em segurança de pipeline/portabilidade |
| Risco principal | Estado global GL, contexto em janelas, drivers | Graphics pipeline/texture lifecycle, shader cross compilation | ABI/toolchain do WGPU e composição nativa/GLB |
| Binding Odin | core:gl / vendor:sdl3 | vendor:sdl3 | vendor:wgpu (toolchain externa) |
| Objetivo mais alinhado | PoC acessível e rápida | Produto moderno SDL-native | Produto GPU moderno independente de SDL GPU |

## Consideração sobre SDL_GPU3

SDL_GPU é uma API moderna independente do OpenGL; usa backends gráficos apropriados ao ambiente (Vulkan, D3D12, Metal). Ela **não** serve de fallback OpenGL3 no mesmo renderer. Se adotarmos SDL_GPU3, o suporte de máquinas antigas precisa ser testado, ou implementaremos outro backend.

O renderer ImGui SDL_GPU3 exige chamar **PrepareDrawData antes do render pass ImGui**; não presumir que é uma troca de nomes de OpenGL. [Código upstream](https://github.com/ocornut/imgui/blob/master/backends/imgui_impl_sdlgpu3.cpp).

## Multi-viewport e Wayland

Docking interno pode e deve funcionar sem janelas OS extras. Janelas nativas flutuantes além da principal são recursos opcionais dos **multi-viewports** e exigem duas coordenações: plataforma e render backend, além de DPI, foco, window positioning, hit-testing, context and OS capabilities.

O upstream [documenta que Wayland não fornece posicionamento arbitrário](https://github.com/ocornut/imgui/wiki/Multi-Viewports); existe investigação em 2026 sobre mitigação com xdg-toplevel-drag e melhorias do backend. Isso **não prova** suporte geral. Nunca bloquear a GUI principal nessa feature.

## Alternativas anteriormente avaliadas

**Proposta A — SDL3 + OpenGL3 na primeira versão:** menor risco para o primeiro viewport e excelente base para testar todos os comportamentos ImGui/docking. Criar RenderBackend interface própria que aceite snapshot, texture/resize e GPU resource lifetime; avaliar SDL_GPU3 ou WGPU depois **somente se exigir benefícios concretos**. Isso não significa que trocar o renderer é gratuito: shaders, buffers, pipelines, sync e formatos de textura exigem implementação independente.

**Proposta B — SDL3 + SDL_GPU3 desde o começo:** escolher uma API mais moderna e uniforme para Windows/Linux, com shaders e GPU pipeline mais explícitos; exige mais testes e adaptação de infraestrutura agora. Pode simplificar a evolução do renderer se o produto vier a exigir efeitos avançados e GPU compute, desde que máquinas alvo suportem o backend.

**Proposta C — SDL3 + WGPU desde o começo:** renderer moderno e portável para projetos com requisitos GPU maiores; integração e distribuição potencialmente mais complexas para uma ferramenta focada em tiles/quads. Não escolher só por capacidade teórica.

## Spike mínimo comparável

- Janela SDL3 e Dear ImGui docking, com Palette/Viewport/Inspector.
- Render real de cena: atlas PNG, quad, câmera orbit/pan, picking, nearest texture, grid e seleção.
- Redimensionar painel 100 vezes, trocar tabs, arrastar dock, mover entre monitores DPI e teste de memory leaks.
- Focar inspector numérico enquanto câmera ou cursor 3D permanece no mesmo viewport; sem conflito de input.
- Save/restore layout após reiniciar; keyboard F6/Tab e alternativa ao drag.
- Janelas nativas externas como gate separado, sobretudo X11/Wayland.
- Medir frame time (CPU/GPU), RAM/VRAM, latência e desligamento correto no hardware de referência.
- Distribuição Windows/Linux, bibliotecas nativas, licenças, versões de compilador e builds reproduzíveis.
- Registrar **PASS/FAIL/BLOCKED/NOT RUN**, versões e SHA. Nunca afirmar performance não medida.

## Requisitos atuais de OpenGL3

O backend GPU poderá evoluir se necessário, mas **o renderer OpenGL3 deve ser um produto completo da PoC**: perf, picking, texture cache, shader contract, partial invalidation, resize e disposal. Não construir uma abstração universal que atrase a primeira face.

## Fontes

- [Capati/odin-imgui](https://github.com/Capati/odin-imgui)
- [ImGui official backends](https://github.com/ocornut/imgui/blob/master/docs/BACKENDS.md)
- [ImGui OpenGL3](https://github.com/ocornut/imgui/blob/master/backends/imgui_impl_opengl3.cpp)
- [ImGui SDL_GPU3](https://github.com/ocornut/imgui/blob/master/backends/imgui_impl_sdlgpu3.cpp)
- [ImGui WGPU](https://github.com/ocornut/imgui/blob/master/backends/imgui_impl_wgpu.cpp)
- [SDL3 Odin vendor](https://pkg.odin-lang.org/vendor/sdl3/)
- [ImGui Multi-Viewports](https://github.com/ocornut/imgui/wiki/Multi-Viewports)
- [Wayland issue 2026](https://github.com/ocornut/imgui/issues/9370)
