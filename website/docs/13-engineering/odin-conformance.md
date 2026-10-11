# Conformance da implementação Odin

> Guia de engenharia antes do primeiro commit com código. Não há builds registrados.

## Vertical slice CPU-first

1. Criar TileRegion com bounds de atlas explícitos (inclusive erro de limites).
2. Construir quad com winding e UV nos quatro cantos.
3. Aplicar Rotate/Flip e verificar orientação em atlas assimétrico.
4. Testar placement no workplane e no edge snap em diferentes normais.
5. Executar Place/Cancel/Undo sem alterar revisão inesperadamente.
6. Serializar JSON versionado; round-trip exato para ids e UV.
7. Exportar OBJ/MTL e comparar posições, indices, UV e material em ferramenta externa.

## Testes a desenvolver

- Unit: atlas bounds, UV corner permutation, snapping tolerances e degenerate faces.
- Property: invertibilidade de rotação por 4 quartos, serialização determinística e revision.
- Integration: gesto inteiro, Undo, save/load, export.
- Visual: nearest pixel, snapping overlay, câmera, input focus e GPU cache.
- Manual: Windows/Linux, Wayland/X11, keyboard-only, 100–200% scale, low-end GPU.

## Relato de gates

Estado de cada gate: PASS / FAIL / NOT RUN / BLOCKED / NOT APPLICABLE; inclui comando, plataforma, resultado, evidência e SHA. Benchmarks relatam hardware e tamanho da cena, nunca só "rápido".

## Análise de risco

Odin fornece mecanismos explícitos de gerenciamento, porém não elimina bugs de lifetime, uso de ponteiros, cleanup ou FFI. Revisar arrays/slices, allocators e handles; preparar sanitização e testes de fuzz para arquivo externo antes da V1.
