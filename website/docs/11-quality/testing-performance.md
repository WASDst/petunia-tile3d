# Qualidade, testes e performance — Odin

> Nenhum gate do editor foi executado; somente a infraestrutura documental existe.

## Estratégia por camadas

| Camada | Verificações | Evidência exigida |
|---|---|---|
| Geometry / TileRegion | odin test, checks de bounds, UV, normals, degenerate | Saída + SHA |
| Tool / Application | undo/redo, cancel, revisão, IDs obsoletos | Fixtures e diff |
| IO / Export | JSON round-trip, path sanitization, OBJ/MTL importado externamente | Arquivos e resultados |
| Renderer | UV nearest, picking, snapshot, GPU caching e perdas de contexto | Captura native |
| GUI / Accessibility | labels, F6/Tab/foco, 200% UI Scale, keyboard only, leitores de tela | Plataforma/AT |
| Integração | Windows/Linux (Wayland/X11), DPI, 1366×768, device fallback | Checklist manual |
| Performance | latência input, frame time, RAM, cena 100/1000/10000 faces | Hardware + amostras |

## Gates da PoC

- Comandos Odin executados em versão pinada do compilador, registra stdout/stderr.
- Detectar e corrigir warnings e erros de compiler/stdlib/vendor das versões reais em uso.
- Property/edge cases: Rotate quatro vezes recupera UV, Flip 2 vezes, preview cancelado imutável, JSON estável.
- Stress: PNG malformado, atlas muito grande, dimensões negativas e arquivos JSON/OBJ hostis.
- Perf: medição release/debug separada, profiling real antes de introduzir BVH/cache complexo.
- Usability: primeira casa concluída com teclado e sem editor UV.

## Thresholds a medir, não prometer

Meta exploratória de 60 FPS em cena com 10 mil faces e custo de input baixo. Publicar máquina/GPU, SO, resolução, modo de build, tamanho do atlas e scripts de fixture. FPS isolado não mede usabilidade, mesh edit nem trabalho de CPU.

## Reportar honestamente

Usar PASS, FAIL, NOT RUN, BLOCKED, NOT APPLICABLE; incluir SHA e comandos. Não declarar a11y ou integridade do export com base apenas em screenshots ou docs.

## Renderer SDL3/OpenGL3: compatibilidade obrigatória

Executar a [matriz de compatibilidade gráfica](./graphics-compatibility-matrix.md) com logs reais Windows/Linux/Wayland/X11, drivers, FBO resize, ImTextureRef 1.92+, DPI, ImGuizmo/Undo, memória, frame time e evidências. Todos os testes GPU permanecem **NOT RUN** até execução local comprovada.
