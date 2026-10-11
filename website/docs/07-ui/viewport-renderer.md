# Viewport e renderização — integração Dear ImGui

> Render gráfico **ainda não implementado**. Dear ImGui com DockSpace é obrigatório; renderer e plataforma **aprovados: SDL3 + OpenGL 3.3 Core**.

## Render path

~~~text
Document (faces + atlas references)
    ↓ evaluate after revision
RenderSnapshot (triângulos, UV, materiais, seleção)
    ↓ grouping by texture + dirty region
renderer OpenGL3 (GLSL 330, VAO/VBO/EBO/FBO; GPU buffers)
    ↓
Viewport + overlay de grid/snap/selection + UI
~~~

Não gravar handles da GUI, OpenGL/WGPU, GPU memory, texture IDs ou camera handles no documento. Descartar RenderSnapshot e reconstruir a partir do documento deve preservar o resultado.

## Primeiras exigências

1. PNG importado e validado. Filtragem nearest e sem bleeding por recorte de atlas, sampling e padding.
2. Câmera orbit/pan/zoom com presets frontal, lateral, superior e pseudo-isométrico opcional.
3. Faces visíveis nas duas orientações apenas conforme configuração explícita; normals e winding corretos.
4. Raycast/plane intersection para picking; seleção por edge/face clara; depth test consistente.
5. Cache agrupado por material/atlas, evitando uma draw call por face em produção.
6. Preview, snap, grid e axes desenhados como overlay separado, com testes de legibilidade e occlusion.
7. Medições registradas em cenas com 100, 1.000 e 10.000 faces. Meta de 60 FPS é exploratória, depende de hardware.

## Critérios para trocar renderer

A viewport será desenhada para framebuffer/textura GPU e exibida dentro de uma janela dockável ImGui. Validar resize, DPI, pointer coordinates, captura input, janela flutuante, contextos GL e caching. Reavaliar outro renderer apenas com necessidade comprovada, sem alterar geometry/app.

## Anti-objetivos

Não introduzir materiais PBR, sombras complexas, ECS, physics, animação ou ray tracing antes de conseguir a primeira parede texturizada e exportável.

## Backend independente do painel

RenderSnapshot → RenderBackend → texture GPU offscreen → ImGui::Image dentro do painel dockável; coordenadas de ponteiro convertidas por ViewportRect e DPI. O renderer não conhece Document mutável. **SDL3 + OpenGL3 foi aprovado**; SDL_GPU3/WGPU ficam como possibilidades futuras, não baseline. ImGuizmo requer sincronizar view/projection e input capture, não alterar Document diretamente.

## Contratos detalhados vigentes

- [FBO, ImTextureRef 1.92+, coordenadas e DPI](./viewport-framebuffer-contract.md)
- [Bootstrap SDL3/OpenGL e GL lifecycle](../13-engineering/sdl3-opengl3-integration.md)
- [ImGuizmo, matriz, preview e Undo](./imguizmo-integration.md)
