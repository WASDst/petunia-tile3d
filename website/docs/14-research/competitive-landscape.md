# Pesquisa: Crocotile3D e outras ferramentas especializadas

> **Snapshot 2026-10-09.** Dados observados nas fontes oficiais e relatos comunitários. A ausência de documentação sobre uma feature **não prova ausência no software**. Não converter hipóteses de diferenciação em alegações de "ninguém tem".

## Crocotile3D

Fonte: [site oficial](https://www.crocotile3d.com/) e [manual de uso](https://www.crocotile3d.com/howto.html). Em 2026-10-09 o site lista versão 2.7.4, de 04/10/2026.

**Comprovado pelo site/manual:** importar múltiplos tilesets e selecionar região, Tile/Sticky/Block/Primitive tools, UV transforms, Paint, vertex colors, splitting/merging, smoothing, meshes imported, lighting/shadow, animated UV/tiles, animation/timeline, rigging/skinning, prefabs, nested instances, import FBX/glTF/etc e export OBJ/glTF/GLB/DAE.

**Implicação:** não chamar Animated Tiles, Block Brush, Prefab, UV or painting de "ideias inéditas", pois já são parte do produto de referência. Nosso diferencial deve ser **simplicidade de fluxo, conserto de problemas concretos e alternativa acessível**, não volume de ferramentas.

## Blockbench

Fonte: [Blockbench](https://www.blockbench.net/) e [Wiki](https://www.blockbench.net/wiki/guides/blockbench-overview-tips/).

**Comprovado:** low-poly/cuboids + mesh edit, 2D/3D texture painting, UV automático e manual, image editor externo live, animation e plugin store. Não assumir equivalência com construção de quads por region de atlas. Conversas [Reddit](https://www.reddit.com/r/Blockbench/comments/1tcsz1i/can_you_build_3d_models_using_tilesets_in/) descrevem essa diferença do ponto de vista de um usuário.

## Sprytile / ReSprytile

[Sprytile repo](https://github.com/Sprytile/Sprytile), [ReSprytile](https://github.com/HexMissCode/ReSprytile). Features documentadas: construir mesh com tiles, UV painting sobre mesh preexistente, pixel-grid para alinhamento. Relatos apontam problemas de compatibilidade com versões do Blender, mas **variam por fork e data**; não alegar que "Sprytile não funciona" genericamente.

## TrenchBroom

[Manual](https://trenchbroom.github.io/manual/latest/), [repo](https://github.com/TrenchBroom/TrenchBroom): edição direta em 2D/3D, brushes, grid snapping, UV lock, texture fit/justify, perf de mapas grandes, export, entity browser e automatic backups. Foco em level editor, portanto utilidade como referência para *workflow* de paredes, brushes e atlas alignment — não como dependência.

## Asset Forge

[Guia](https://kenney.nl/knowledge-base/asset-forge/getting-started-with-asset-forge) e [produto](https://kenney.itch.io/assetforge): kitbash com blocos, pintura por material, custom collections e export mesh ou sprite. Inspira um shelf de peças reutilizáveis e export rápido; foco não é a mesma semântica de TileRegion em quad.

## Evidências comunitárias — alcance limitado

- [Comparação Crocotile vs Blockbench (Reddit, jul/2026)](https://www.reddit.com/r/IndieGaming/comments/1uqe5ms/crocotile_or_blockbench/): comentários favorecem Crocotile para environment e Blockbench para props; um usuário nota **dificuldade de alinhar escala e pixel density** entre ferramentas. É experiência pontual, não pesquisa estatística.
- [Tile building in Blockbench? (Reddit, mai/2026)](https://www.reddit.com/r/Blockbench/comments/1tcsz1i/can_you_build_3d_models_using_tilesets_in/): iniciante procurando alternativa gratuita ao Crocotile e confronto com processo de construir mesh/cube antes de aplicar texture.
- [Pixel density em pixel-art 3D (Reddit, ago/2024)](https://www.reddit.com/r/godot/comments/1exl8od): usuário relata dificuldade de consistência de pixel size em workflows Blockbench/Crocotile.
- [UV/grid fricção ao sair do Blender (Reddit, nov/2023)](https://www.reddit.com/r/low_poly/comments/1846qpz/blockbench_or_crocotile3d/): dificuldade em manter grid e UV consistente, evidência anedótica útil para desenho de tarefa.
- [ReSprytile Blender 4](https://github.com/HexMissCode/ReSprytile): forks surgem para recuperar compatibilidade, reforçando valor de aplicativo dedicado com pipeline autônomo.

## Oportunidades inferidas — para validação

| Sinal | Hypothesis | Como testar |
|---|---|---|
| Pixel density desuniforme | guia "Keep Pixel Scale" e diagnóstico pixel/world | antes/depois com artista exportando props de escalas diferentes |
| Tool hopping entre editores | one-click Tile Region Stamp sobre mesh importada | contar operações de UV em tarefas simples |
| Carga cognitiva de softwares gerais | cinco ferramentas primárias e comandos contextuais | teste com iniciantes sem tutorial externo |
| Manutenção de múltiplos assets | Smart Replace/Select Similar e Tile Collections | prova de edição de 20 paredes sem refazer atlas |
| Grid/UV snapping difícil | Sticky workplane + planar/surface snap previsível | teste em cantos, diagonais e câmera ortho |

**Não concluímos** que as features acima "ainda não existem" em Crocotile/Blockbench; são propostas de diferenciação por implementação simples e foco. Veja [Backlog de ideias](../15-ideas/opportunity-backlog.md).
