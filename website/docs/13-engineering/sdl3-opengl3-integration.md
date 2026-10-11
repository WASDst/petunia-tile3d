# SDL3 + OpenGL 3.3 Core — contrato da plataforma e do renderizador

> **DECISÃO APROVADA EM 11/10/2026: T3D-023**. Odin como linguagem principal, SDL3 para plataforma, OpenGL 3.3 Core Profile como alvo inicial e Dear ImGui docking via backends SDL3/OpenGL3. O binding [Capati/odin-imgui](https://github.com/Capati/odin-imgui) continua **candidato preferencial sujeito a build**, não dependência comprovada. Esta é uma especificação para implementação futura, **não código executável testado**.

## Por que OpenGL 3.3 Core?

- Suficiente para texturas de atlas, VAO/VBO/EBO, shaders GLSL 330, depth test, picking CPU e framebuffer offscreen.
- Maior chance de funcionar em desktops e GPUs mais antigas do que um backend exclusivamente Vulkan/WGPU, sem prometer compatibilidade universal.
- O exemplo oficial [example_sdl3_opengl3](https://github.com/ocornut/imgui/blob/master/examples/example_sdl3_opengl3/main.cpp) demonstra a integração da plataforma e renderer ImGui.
- Excluir OpenGL 2.x fixed pipeline e recursos de GL 4.x do caminho obrigatório. Recursos de GL mais recentes serão capacidades opcionais.
- SDL_GPU3 e WGPU deixam de ser alternativas iniciais; permanecem possibilidades futuras justificadas por necessidades medidas, **não uma migração garantida**.

## Pacotes e dependências

~~~text
src/
  main/                   # bootstrap SDL3, loop, shutdown e configuração
  platform/sdl3/          # window/events/clipboard/IME/DPI/GL context
  graphics/opengl/        # GL proc loader, resource registry, shaders, framebuffer
  graphics/render_scene/  # mapeia RenderSnapshot -> GPU, passes 3D e overlays
  graphics/asset_cache/   # atlas, tileset, loaders, texture ownership, budgets
  ui/imgui_adapter/       # binding Dear ImGui + SDL3/OpenGL3 backends
  ui/viewport/            # ViewportRect, camera, editor panels, presented texture
  ui/gizmo/               # bridge cimguizmo opcional, per-viewport context
  ui/components/          # toolbars/inspector/feedback/palette
  application/            # ToolSession, Commands, Undo e selection
  geometry/               # TileRegion, mesh, UV e IDs; SEM GL/SDL/ImGui
~~~

Layout ilustrativo para futuros pacotes Odin: dividir somente quando existirem módulos reais. **Não** criar crates Rust nem um motor genérico de renderização especulativo.

Dependências candidatas: Odin \`vendor:sdl3\`, Odin \`core:gl\` (conferir assinatura da versão pinada), Capati/odin-imgui e seus backends \`sdl3\` + \`opengl3\`, loader de funções GL suportado por Odin/SDL3. C/C++ build da lib ImGui e backends só no adaptador FFI. [SDL_GL_GetProcAddress](https://wiki.libsdl.org/SDL3/SDL_GL_GetProcAddress) é a fonte canônica para funções GL onde aplicável.

## Bootstrap: ordem e falha explícita

1. Verificar SDL3 e Odin suportados, arquitetura x86_64 inicialmente; coletar informação de sistema sem telemetria remota.
2. \`SDL_Init(SDL_INIT_VIDEO ...)\`; tratar erro de retorno, liberar parcialmente alocados.
3. **Antes da criação da janela**: \`SDL_GL_SetAttribute\` para major 3, minor 3, profile **CORE**, double-buffer, depth/stencil conforme necessidade; sRGB e debug context devem ser escolhas verificadas, não suposições.
4. Criar janela redimensionável com \`SDL_WINDOW_OPENGL\` e \`SDL_WINDOW_HIGH_PIXEL_DENSITY\` quando disponível; prever tamanho mínimo acessível, janela não fullscreen.
5. \`SDL_GL_CreateContext(window)\`; garantir current no thread gráfico; consultar versão/profiling real com \`SDL_GL_GetAttribute\` e \`glGetString\`, rejeitar context abaixo do mínimo.
6. Carregar entradas OpenGL **após contexto corrente**, checar VAO/FBO/shader APIs e capabilities; não chamar GL por ponteiro nulo; não depender de símbolos modernos estáticos no Windows.
7. \`SDL_GL_SetSwapInterval(1)\` inicialmente; oferecer settings 0/1 e -1 adaptativo só quando suportado, com fallback para 1.
8. Criar contexto Dear ImGui **uma vez**, ligar \`NavEnableKeyboard\`, \`DockingEnable\`; \`ViewportsEnable\` **desligado por padrão**. Inicializar \`imgui_impl_sdl3\` para OpenGL e \`imgui_impl_opengl3\` com GLSL \`#version 330 core\` conforme API do binding exato.
9. Preparar ThemeTokens, font atlas/texture updates **compatíveis com ImGui 1.92**, PanelRegistry, DockSpace persistente e viewport resources. Exibir \`ShowDemoWindow\` em builds de desenvolvimento.
10. Inicializar shaders + textura de checkerboard + malha de dois triângulos + depth FBO. Deixar o primeiro frame mostrar erro recuperável caso o renderer offscreen falhe; não declarar aplicação funcional sem testes.

API pseudoesquemática (não código Odin compilável):

~~~text
on_start()
  platform = SDL3.init_video()
  window = SDL3.create_gl_window(request = GL3_3_CORE, high_dpi = true)
  context = SDL3.create_gl_context(window)
  gl = load_gl_symbols(SDL_GL_GetProcAddress)
  validate_gl3_3_and_fbo(gl)
  imgui = ImGui.create_with_backends(SDL3, OpenGL3)
  configure_docking_and_keyboard(imgui)
  renderer = GLRenderer.create(gl)
  app = Application.create()

each_frame()
  events = SDL3.poll_all()
  ImGuiSDL3.process_events(events)
  platform.consume_window_and_dpi_events(events)
  update_input_router_from_events(events)
  app.update_without_mutating_document_from_ui_callbacks()

  ImGuiOpenGL3.new_frame()
  ImGuiSDL3.new_frame()
  ImGui.new_frame()

  build_main_dockspace()
  for viewport in active_viewports:
    rect = viewport.measure_content_rect_and_dpi()
    renderer.resize_or_reuse_offscreen(viewport.id, rect.pixel_size)
    renderer.render_snapshot_to_fbo(viewport.snapshot, viewport.camera)
    viewport.draw_imgui_texture_from_color_attachment()
    viewport.draw_gizmos_and_ui_overlays()
    viewport.dispatch_ui_intents_after_input_validation()
  draw_panels_and_status()

  ImGui.render()
  SDL3.make_primary_context_current()
  gl.bind_default_framebuffer()
  gl.viewport(window.drawable_pixel_width, window.drawable_pixel_height)
  gl.clear_backbuffer()
  ImGuiOpenGL3.render_draw_data()
  SDL3.swap_window()

on_shutdown()
  cancel_tool_sessions_and_flush_user_project_if_requested()
  destroy_gizmos_and_custom_gpu_resources_with_context_current()
  shutdown_imgui_renderer_then_platform_then_context()
  SDL3.destroy_gl_context_and_window()
  SDL3.quit()
~~~

**A ordem exata do backend e a nomenclatura das funções Odin devem ser confirmadas nos bindings pinados.** Não copiar esse pseudocódigo como fonte compilável.

## Renderizador GL 3.3: contrato mínimo

- \`GLRenderer\`: init/resize_viewport/update_mesh/upload_atlas/render_scene/pick_cpu/teardown e warnings.
- Ownership de VAO/VBO/EBO/textures/shaders/program/FBO/renderbuffers concentrado no renderer/AssetCache com IDs geracionais e invalidação determinística.
- RenderSnapshot é imutável durante o frame, revisionado por DocumentId e geração de geometria. Dirty flags diferentes para geometry/UV/material/visibility/selection/camera.
- Mesh upload usa interleaved/planar layout justificado por profiler; agrupar draw por material+atlas e reutilizar cache entre viewports; não fazer uma draw-call por face.
- GLSL 330 programável; validar compile/link diagnostics e reportar logs. Nada de fixed function, tessellation, compute ou persistent mapped buffers como dependência mínima.
- OpenGL roda no thread proprietário de contexto. Jobs CPU podem preparar buffers, **não** fazer GL sem contexto compartilhado definido/testado.
- Nenhum handle GL entra no projeto serializado, Undo, Geometry ou Export; AssetCache é reconstruível do formato autoral.
- Guardar/recuperar estados (program, VAO, FBO, viewport, blending, depth/scissor, active textures) nos limites de passes e plugins. ImGui/OpenGL backend é responsável pelos seus estados internos, mas plugins não devem depender disso.
- Graceful degradation para resolução de viewport e MSAA; não baixar resolução silenciosamente em pintura nearest-neighbor sem indicador.

## Cor, filtro e UV

- Alvo simples: color attachment RGBA8 + depth24-stencil8 **se suportado**. Offscreen FBO deve ser \`GL_FRAMEBUFFER_COMPLETE\`; alocação deve abortar com aviso quando falhar.
- Definir pipeline explícito para amostras PNG/sRGB e framebuffer para não escurecer/lavar pixel-art; antes de ativar \`GL_FRAMEBUFFER_SRGB\`, testar textura, default surface e screenshot contra imagem de referência. **Não assumir RGB linear/sRGB automaticamente.**
- Tileset/atlas: \`GL_NEAREST\` em MAG/MIN e \`GL_CLAMP_TO_EDGE\` onde adequado; \`mipmaps\` desativados no baseline pixel-art. \`GL_CLAMP_TO_EDGE\` não impede vazamento entre células do mesmo atlas: padding, gutters ou insets UV e sampling precisam de teste.
- Textura UI de preview e render target podem exigir política diferente de sampler da textura de tileset (normalmente linear para resolução final do viewport, nearest apenas para pixels art sem suavização).
- Usar \`GL_UNPACK_ALIGNMENT\` correto para linhas de upload e restaurar estado após; validar orientação do eixo Y em \`Image()\`.

## IME, entrada, threading, CPU/GPU pacing

- Eventos SDL3 devem passar ao backend ImGui antes de serem consumidos pelo editor. \`WantCaptureMouse\`/\`WantCaptureKeyboard\` são pistas relevantes, **não** substituem hit-test e prioridade modal.
- InputRouter estabelece prioridade: dialog/modal → edição texto → gizmo → ferramentas 3D → câmera → atalhos globais condicionados.
- Rotina ociosa deve evitar reconstruir malha/textura; medir frame pacing; swap interval disponível pode falhar em alguns drivers.
- Render thread inicial único. Não criar acesso OpenGL concorrente sem desenho explícito de context ownership.

## Distribuição e detecção de falha

- Windows e Linux x86_64; drivers Intel/AMD/NVIDIA e Mesa a verificar. Testar X11, Wayland, escala e monitores diferentes.
- Se GL 3.3 Core não estiver disponível: mostrar erro específico com GPU/driver e recomendações; **não** cair silenciosamente para GL2/compatibility. Software Mesa é alternativa de diagnóstico, não promessa de desempenho.
- Detectar resize, zero-size, minimização, contexto perdido e reconstrução de recursos. Nunca emitir \`glTexImage2D(0,0)\`.
- Empacotar dependências SDL3/ImGui e licenças, com versões e checksums; sem CDN/rede em runtime. Considerar exigências de Linux (glibc, distribuição, window backend).
- C/C++ da biblioteca ImGui é parte da stack nativa; o **código de aplicação continua Odin**, não "Odin puro sem FFI".

## Fontes upstream

- [ImGui SDL3/OpenGL3 reference](https://github.com/ocornut/imgui/blob/master/examples/example_sdl3_opengl3/main.cpp)
- [Capati Odin imgui](https://github.com/Capati/odin-imgui)
- [SDL3 OpenGL context](https://wiki.libsdl.org/SDL3/SDL_GL_CreateContext)
- [SDL3 context attributes](https://wiki.libsdl.org/SDL3/SDL_GL_SetAttribute)
- [SDL3 window HighDPI](https://wiki.libsdl.org/SDL3/README-highdpi)
- [ImGui texture changes](https://github.com/ocornut/imgui/blob/master/docs/FAQ.md)

Ver [Framebuffer/viewport](../07-ui/viewport-framebuffer-contract.md), [ImGuizmo](../07-ui/imguizmo-integration.md), [matrix de testes](../11-quality/graphics-compatibility-matrix.md).
