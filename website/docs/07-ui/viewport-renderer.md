# Viewport e renderização — hipótese raylib

> Render gráfico **ainda não implementado**. A PoC candidata usa vendor:raylib; não fixamos um backend de produção.

## Render path

~~~text
Document (faces + atlas references)
    ↓ evaluate after revision
RenderSnapshot (triângulos, UV, materiais, seleção)
    ↓ grouping by texture + dirty region
raylib adapter (draw/update GPU buffers)
    ↓
Viewport + overlay de grid/snap/selection + UI
~~~

Não gravar handles raylib, GPU memory, texture IDs ou camera handles no documento. Descartar RenderSnapshot e reconstruir a partir do documento deve preservar o resultado.

## Primeiras exigências

1. PNG importado e validado. Filtragem nearest e sem bleeding por recorte de atlas, sampling e padding.
2. Câmera orbit/pan/zoom com presets frontal, lateral, superior e pseudo-isométrico opcional.
3. Faces visíveis nas duas orientações apenas conforme configuração explícita; normals e winding corretos.
4. Raycast/plane intersection para picking; seleção por edge/face clara; depth test consistente.
5. Cache agrupado por material/atlas, evitando uma draw call por face em produção.
6. Preview, snap, grid e axes desenhados como overlay separado, com testes de legibilidade e occlusion.
7. Medições registradas em cenas com 100, 1.000 e 10.000 faces. Meta de 60 FPS é exploratória, depende de hardware.

## Critérios para trocar renderer

Se a PoC exigir GUI docking profunda, melhor pipeline de GPU, offscreen viewport ou integrações específicas, medir alternativas SDL3 + WGPU (Odin vendor) e backends OpenGL sem comprometer as estruturas de geometry/app.

## Anti-objetivos

Não introduzir materiais PBR, sombras complexas, ECS, physics, animação ou ray tracing antes de conseguir a primeira parede texturizada e exportável.
