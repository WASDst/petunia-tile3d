# Ferramentas pós-MVP — funcionalidades aprovadas e demais hipóteses

> **Situação:** cinco ferramentas aprovadas para **inclusão no roadmap**, mas nenhuma implementada. Esta decisão não adiciona complexidade ao MVP nem substitui os [gates de qualidade](../11-quality/testing-performance.md). Subfuncionalidades genéricas além das cinco continuam em análise.

## Funcionalidades aprovadas

| Funcionalidade | Fase proposta | Especificação | Dependency gate |
|---|---|---|---|
| **Surface Tile Stamp** | V1 | [Desenhar tiles diretamente em faces](./surface-tile-stamp.md) | FaceCorner UV, seleção de faces e undo |
| **Select/Replace Similar Tile** | V1 | [Substituir tiles semelhantes](./replace-similar-tiles.md) | TileBinding/TileId e comandos atômicos |
| **Tile Variations** | V1 | [Variações com seed determinística](./tile-variations.md) | Palette, TileRegion e placement IDs estáveis |
| **Pixel Density Doctor** | V1 | [Diagnóstico de escala de pixels](./pixel-density-doctor.md) | geometria avaliada e UV + atlas size |
| **Smart Wall/Roof Brush** | V1+ | [Wall Strip e telhado básico](./smart-wall-roof-brush.md) | Workplane, snapping e preview de geometry patch |

**Ordem não fixa:** ganho quick-win de Similar/Variants, compatibilidade de Surface Stamp, análise read-only de densidade e por último geração Wall/Roof. Antes de editar código, especificar vertical slice, testes, memória, licenças e acessibilidade. `Aprovado` não significa build pronto.

## Fluxos complementares que continuam como propostas

- Tile Palette avançada: favoritos, tags, quick presets e grupos mais elaborados.
- Macro Brush genérico, shape presets, material kits, custom TileRegion polygons.
- Autotile por regras de vizinhança e cantos automáticos (não confundir com Wall/Roof básicos).
- Asset Shelf e Prefab Quick Stamp de grupos.
- Animated UV/tiles, sprite exporter, Atlas packing/repair, complex roofs/curved surfaces.
- Editor de textura embutido (MVP: abrir externamente + reload).
- Universal geometry generators, skinning e rigging: fora do foco inicial.

## Critérios de inclusão de novos recursos

Cenário real, valor demonstrável, número de passos, interação por teclado, preview/Undo, respeito ao pixel scale, conservação dos dados, cenário de erro, testes, impacto do grafo de dependências e custo de manutenção. Ferramenta que exige outra engine ou muitos painéis para um caso simples deve ser redesenhada.
