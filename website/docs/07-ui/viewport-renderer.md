# Renderer e viewport — OpenGL simples, Slint acessível

> **Condição técnica de Go/No-Go.** A arquitetura Petunia3D propõe Slint + OpenGL 3.3/glow/FBO, mas a integração livre de readback **não foi comprovada no Tile3D**.

## Contratos

O domínio fornece `RenderSnapshot` com matrizes, posições, corner UV, atlas refs e overlay/picking hints. Renderer consome snapshot e revisions. GPU uploads são dirty-only; cache por TextureId/revision e Geometry revision. Nenhum handle GL entra no Document, UI Intent ou serializer.

## Target

- OpenGL 3.3 Core, `glow` se necessário, 1 contexto ownership claro;
- FBO/texture para a viewport compositada no Slint, **sem CPU readback por frame**;
- VAO/VBO/EBO compactos, shader unlit nearest, alpha cutout/blend opcional, backface policy explícita;
- câmera ortho/perspectiva, orbit/pan/zoom mouse e equivalente keyboard;
- grid/workplane, seleção, hovered faces, edge highlight e ghost placement;
- HiDPI coherent: device-pixel ratio, framebuffer resize, picking coordinates, zoom and hit tolerance;
- render on demand por dirty view, com target frame budget apenas após medir.

## PoC que decide stack

1. Construir janela Slint simples, único FBO com um quad texturizado.
2. Comprovar export/uso da textura GPU pelo renderer Slint em Linux e Windows. Registrar API real, versões, context boundaries.
3. Interagir orbit/zoom/pick sobre o quad sem perda de eventos/sem readback.
4. Resize, HiDPI, minimized windows, context recreate, screenshots, error recovery.
5. Medir bins de memória/latência em hardware modesto e comparar com alternativa *mínima*.
6. Se renderização não funcionar no Slint com propriedade de GPU segura, escalar escolha de toolkit com ADR — **não instalar WGPU de fallback silenciosamente**.

## Pixel-art visual contract

`Nearest` default, controle de alpha, sombra desligada por padrão, grid contrast ajustável, sem blur ou AA de textura que altere estilo. Preview de face colinear mantém prioridade de linhas/seleção mesmo em alta densidade.

## Testes importantes

Screenshots diferem por OS/GPU: aplicar tolerância e compare manual quando necessário. Drivers sem GL 3.3 recebem mensagem acessível antes de crash e instrução de compatibilidade. OpenGL errors diagnosticados sem spam de logs.

## Dependências

Não adicionar `wgpu`, `egui`, `eframe`, `assimp`, `bevy` ao MVP. Encapsular `glow`/windowing detrás do adapter. Reusar shader/mesh extraction de Petunia3D apenas após auditoria, sem acoplar `render-gl` que já depende de `egui_glow`, `petunia_project` e `petunia_core`.
