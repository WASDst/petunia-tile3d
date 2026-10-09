# Hipóteses de diferencial e "software killer" — PARA DISCUSSÃO

> **Não aprovadas.** Comparações baseadas em [pesquisa documentada](../14-research/competitive-landscape.md). P0/P1 aqui é ordem de conversa, não compromisso de implementar. "killer" = valor percebido desproporcional ao custo, uma hipótese a testar.

## Matriz de oportunidade (estimativas relativas)

| Ideia | Dor resolvida | Custo | Valor potencial | Risco |
|---|---|---|---|---|
| **Pixel Density Doctor** | pixels variam de tamanho entre props | baixo-médio | alto | preservar UX em UV edit manual |
| **Brush by Surface** | pintar tile em mesh existente exige UV manual | médio | muito alto | mapping em faces deformadas |
| **Select/Replace Similar Tile** | mudar dezenas de ocorrências manualmente | baixo | alto | metadata provenance vs hand UV |
| **Tile Variants seeded** | repetição visual evidente | baixo | alto | determinismo e UX de seeds |
| **Prefab Quick Stamp** | reconstruir casas/árvores similares | baixo-médio | alto | refs/prefab overrides |
| **Rect-to-Room 3D** | construir 4 paredes e chão de maneira repetitiva | médio | muito alto | tool scope ou modelo 2º |
| **Auto Corners (rule graph pequeno)** | parede/telhado fica quebrado em cantos | médio | alto | regras espaciais e "magic" |
| **Visual UV Health** | bleed/stretch/error invisível até engine | baixo-médio | alto | falso positivo e custo |
| **Texture Editor Live Reload** | ferramentas de pixel art externas atualizam arquivos | baixo | médio-alto | file access/watchers e missing state |
| **Export presets (Godot/Unity/PS1)** | escala, UV e alpha quebram no game engine | baixo-médio | alto | formatos mudam, validar fixtures |
| **Sprite sheet from 3D angles** | criar assets 2.5D é laborioso | médio-alto | alto | renderer/export pipeline |
| **Tile-aware Roof/Fence templates** | criar padrões repetitivos não é produtivo | médio | alto | bloat de presets |

## Prioridades para primeira conversa

### 1. Pixel Density Doctor + Keep Pixel Scale

**Why:** pessoas relatam descompasso de escala/texel density ao mesclar Blockbench e Crocotile. O algoritmo base é proporcionalidade pixel/world por face; implementar relatório e overlay simples primeiro, sem auto-rewrite. **Demo:** selecione paredes de tamanhos distintos e veja inconsistências antes do export.

### 2. Select/Replace Similar Tile

**Why:** comparável a "encontrar e substituir" num editor 2D, mas aplicada a scene geometry. Temos TileBinding ID e UV baked; podem ser trocadas ocorrências com preserve rotation/flip. **Demo:** substitua todas as janelas de uma casa com uma escolha, um Undo.

### 3. Surface Tile Stamp

**Why:** compatibiliza objetos importados e fluxo Tile-first. Clique uma face selecionada, escolha tile, aplique UV fit/keep-scale. Mapeamento simples em quads regulares; faces irregulares com preview de distorção. **Demo:** aplicar telhas pixel art numa mesh importada sem abrir UV panel.

### 4. Roof / Wall Quick Pattern

**Why:** casa de pixel art envolve repetir padrões. Começar com "Wall Strip" configurado com 2 TileRegions e parâmetros, não com graph node infinito. **Demo:** 20 quads coerentes em dois cliques.

## Como testar sem construir editor inteiro

Implementar mock interativo das ações e CPU patch no Rust; cinco usuários alvo executam cenário antes/depois. Avaliar:
- passos/tempo sem memorizar comandos;
- precisão UV/Pixel size;
- confiança em cancel/Undo;
- capacidade keyboard only;
- custo por feature em source/tests e potencial de maintenance.

## Ideias deliberadamente não recomendadas agora

Procedural world generator completo, live collaboration, game logic, physics collision runtime, full animation rigs, cloud sync, AI text-to-3D, shader nodes, online asset marketplace e import de tudo. Elas competem por interface, binary e testes com o propósito tile-first.

## Referências

[Manual Crocotile](https://www.crocotile3d.com/howto.html) · [Blockbench](https://www.blockbench.net/) · [Sprytile](https://github.com/Sprytile/Sprytile) · [TrenchBroom](https://trenchbroom.github.io/manual/latest/) · [Asset Forge](https://kenney.nl/knowledge-base/asset-forge/getting-started-with-asset-forge) · [discussão sobre texel density](https://www.reddit.com/r/godot/comments/1exl8od)
