# Viewport dockável — FBO, coordenadas, UVs e ciclo de vida

> Contrato para **SDL3 + OpenGL3** aprovada. Especificação **não implementada**. O viewport é uma janela/área Dear ImGui que exibe textura color de um framebuffer OpenGL criado pelo renderer. Não confundir viewport 3D com **multi-viewport** (janelas nativas independentes do SO).

## Dados independentes do backend

~~~text
ViewportState:
  view_id, document_id, camera, projection_mode
  ui_bounds (min/max em coordenadas ImGui, após layout)
  pixel_bounds (w,h do render target, nunca zero)
  framebuffer_scale / pixel_density
  hover/focus/interaction_mode/tool_preview
  scene_revision, style_scale, render_dirty
ViewportRenderTarget (privado GLRenderer):
  fbo_id, color_tex_id, depth_stencil_id
  width_px,height_px,context_generation,attachment_generation
  optional_msaa_target + resolved_color_tex
ViewportImageHandle (UI adapter):
  typed ImTextureRef temporário, não persiste em Document
~~~

A câmera pertence ao ViewId/prefs e não ao documento. Múltiplas vistas do mesmo documento podem ter projeções e escalas independentes; as alterações no Document invalidam snapshots/meshes compartilhados, não a câmera do outro painel.

## Renderização offscreen

1. Medir \`GetContentRegionAvail()\` após \`Begin("Viewport")\`; calcular área efetiva descontando toolbar/overlays. Guardar \`ImageRect\` real após desenhar para hit-test.
2. Converter tamanho da **área exibida na unidade de coordenadas ImGui** para pixels físicos do render target com escala da plataforma efetiva. Conferir \`ImDrawData.FramebufferScale\`, \`SDL_GetWindowPixelDensity\` e \`SDL_GetWindowSizeInPixels\`; **não multiplicar automaticamente display scale + pixel density**, são grandezas diferentes.
3. Arredondar para dimensões válidas; zero-size/minimized oculta render e preserva recursos temporariamente. Definir limite por \`GL_MAX_TEXTURE_SIZE\`, orçamento VRAM e avisar ao reduzir.
4. Só (re)alocar FBO quando tamanho físico alvo/contexto mudarem; opcional debounce/histerese em resize rápido; **nunca** alocar toda frame.
5. Criar \`GL_TEXTURE_2D\` color RGBA8 e depth/stencil renderbuffer ou depth texture; anexar ao FBO e verificar \`glCheckFramebufferStatus == GL_FRAMEBUFFER_COMPLETE\`.
6. Guardar FBO/viewport/GL state anterior, bind offscreen, \`glViewport(0,0,w,h)\`, limpar color+depth, render grid/geometry/selection overlay, restaurar GL state e framebuffer principal.
7. Opcional MSAA só se recurso e custo justificarem; para MSAA usar renderbuffer/texture multisample e \`glBlitFramebuffer\` para uma color texture **sampleable** não-multisample antes de entregar à UI. Não tentar mostrar diretamente a textura multisample ao ImGui.
8. Converter GLuint do color attachment para \`ImTextureID\` e \`ImTextureRef\` conforme **API real 1.92+**; \`Image()\` recebe ImTextureRef nesse ImGui moderno. Validar \`ImTextureRef_FromID\` ou equivalente **no binding Odin pinado**, não fazer cast de ponteiro arbitrário ou passar endereço temporário expirado.
9. Configurar UV vertical da \`Image\` de forma consistente (comum em GL: \`uv0=(0,1)\`, \`uv1=(1,0)\`); manter o mesmo contrato para picking, overlay e screenshots; screenshots podem inverter eixo na leitura.
10. Após a \`Image\`, calcular \`ImageRect\` real (\`GetItemRectMin/Max\`) para transformar mouse. Desenhar ImGuizmo e auxiliares com clip rect apropriado.
11. Se o GL texture id for reciclado após resize, atualizar ImTextureRef no mesmo frame; não guardar ID GL antigo no UI state ou layouts. Nunca liberar textura ainda referenciada por draw data não consumido; considerar fence/contexto na futura implementação multi-viewport.

## Transformações de coordenadas

~~~text
SDL window/pointer coordinates
  ↕ SDL+ImGui event mapping
ImGui global screen coords (x,y)
  − ImageRect.min
local_display_px_or_units = [0, width) × [0, height)
  × effective_density
physical_fbo_px (x,y; invert y para OpenGL se requerido)
  ÷ render_width,height
normalized UV viewport
  → NDC(-1..1) → inverse(projection*view) → ray
  → grid/workplane, triangle, face, edge, tile region
~~~

**Proibição:** utilizar \`SDL_GetWindowDisplayScale()\` como se fosse igual a \`SDL_GetWindowPixelDensity()\`. O primeiro mede escala de conteúdo para legibilidade, o segundo a razão pixels/janela, e variam de modo distinto em Windows vs Wayland. Refazer matriz e ray no resize/DPI.

Picking: CPU ray/triangle + spatial acceleration progressiva no MVP para face/edge/vertex. GPU ID picking somente se dados reais provarem necessidade; readback síncrono \`glReadPixels\` pode bloquear o frame. Testar prioridade gizmo/overlays para não selecionar geometria quando tool manipula handle.

## Pixel art e tilesets

- Atlas nearest filtering e UV por canto versionadas; \`GL_NEAREST\` nos samples de atlas para texturas de autoria.
- Dar feedback de seleção/pixel grid em escala fracionária e inteira; em pan/zoom não desenhar linhas de grade que mudem de grossura de maneira incoerente.
- \`GL_CLAMP_TO_EDGE\` evita borda externa, mas não vazamento entre sprites vizinhos do mesmo atlas; gutters/padding e sampling offsets devem ser testados com fixture de cores contrastantes.
- Tile preview/UV editor podem usar **FBO ou desenho UI próprio** dependendo de escala; a decisão é por requisitos de zoom, selection e performance, não unificar obrigatoriamente.
- Textura PNG com alpha: cuidado com straight vs premultiplied alpha. Definir premultiplicação no shader/blending e checar golden image. Não misturar origens e espaços de cor sem testes.

## Visibilidade, resize e suporte futuro

- Pausar GPU scene pass quando painel invisível/colapsado, mas manter estado de câmera/documento; quando voltar invalidar tamanho/projection.
- Reutilizar GPU cache de malhas e atlas através de ViewIDs; cada viewport tem um color/depth target próprio e matriz de câmera.
- Recursos têm gerações para contexto reset; todas as referências GL são descartáveis/reconstruíveis.
- Em multi-viewport OS opcional no futuro, seguir context sharing + restore e renderer resource lifetime; docking interno é obrigatório agora e independente disso.
- Tool preview, câmera e gizmos não produzem Undo por frame. Somente commit validado por Command.

## Testes de aceitação

- Redimensionar DockSpace/painel 100 vezes sem crash/VRAM crescente; 0×0/minimizar/restore.
- Arrastar com DPI 100% e 200% e entre monitores; comparar mouse local → ray → face.
- Checkerboard, quads com UV assimétrico, atlas com gutters, imagem flip vertical, alpha e gamma.
- Abrir duas viewports com câmeras independentes; alternar abas sem carregar a cena duas vezes.
- Recriar FBO após perda/recriação de contexto, texturas sem ids obsoletos.
- Desenho ImGui não perde scissor/blend/GL viewport quando FBO for renderizado.

## Fontes

[SDL3 High-DPI](https://wiki.libsdl.org/SDL3/README-highdpi), [SDL_GetWindowSizeInPixels](https://wiki.libsdl.org/SDL3/SDL_GetWindowSizeInPixels), [ImGui FAQ textures](https://github.com/ocornut/imgui/blob/master/docs/FAQ.md), [ImGui OpenGL3 backend](https://github.com/ocornut/imgui/blob/master/backends/imgui_impl_opengl3.cpp).
