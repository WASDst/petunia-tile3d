# Decisão de GUI — Dear ImGui + docking completo

> **Aprovada em 11/10/2026.** Este documento substitui a antiga shortlist genérica de toolkits. A avaliação das **bibliotecas auxiliares** não significa instalação ou aprovação automática.

## Decisão fechada

- **Linguagem:** Odin.
- **GUI:** Dear ImGui com docking completo desde a primeira versão.
- **Requisito:** DockSpace, abas, dividir por arrasto, floating interno, reordenar painéis e layouts persistentes.
- **Binding base preferido para prova técnica:** [Capati/odin-imgui](https://github.com/Capati/odin-imgui), Dear ImGui v1.92.9b-docking. Escolha do pacote definitivo condicionada a build real Windows/Linux.
- **Mantemos:** Geometry Core separado de GUI, Tool → Command → Document, teclado, feedback e acessibilidade como obrigações.

## Pendente: decisões não confundidas com GUI

- SDL3 ou GLFW para plataforma.
- OpenGL3 ou WGPU para o renderer; Vulkan poderá ser avaliado, mas não é requisito.
- Multi-viewport nativo em múltiplas janelas do SO e comportamento sob Wayland/X11.
- Biblioteca de gizmo, file browser, texture inspector, gráficos, icon pack e automação.
- Alternativa para acessibilidade semântica; teclado apenas é insuficiente.

## Teste mínimo obrigatório

Renderizar atlas nearest + viewport 3D em textura GPU dentro de DockSpace; criar Palette e Inspector; arrastar, dividir, tabular, redimensionar e persistir/restaurar layout; alternar foco, Tab/F6 e interagir com campo numérico; validar capture de input e 100–200% scale Windows/Linux. Tentar multi-viewport externo separadamente.

[Catálogo de addons e bridges](./imgui-ecosystem.md) · [Arquitetura do viewport](./viewport-renderer.md).
