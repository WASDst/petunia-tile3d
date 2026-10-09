# Ferramentas pós-MVP — backlog condicionado

> **Não aprovadas como implementação imediata.** Propostas para V1/V2, alinhadas com pesquisas em [Comparativo](../14-research/competitive-landscape.md). Cada feature precisa validar demanda, adequação ao foco do produto, custo e compatibilidade.

## V1 candidatas

**Tile Palette avançada:** variações por peso, favoritos, grupos, etiquetas, seletor de região irregular opcional. Complexidade pequena/média, mas requer modelo de seleção claro.

**Surface Stamp:** selecione faces de mesh já existente e aplique TileRegion sem editar UV manualmente. Uso forte para pipeline vindo de Blockbench/Blender; detectar orientation, fit/preserve scale e seams. Evitar auto-reunwrap destrutivo.

**Tile Density Doctor:** analisa diferenças de pixels/mundo, stretched UV, overlap inesperado, bleeding risco, atlas out-of-bounds. Reparar apenas com preview e comando explícito.

**Macro Brush:** repetir tiles por linha/retângulo/padrão, com previsualização e edição numérica. Reuse GeometryPatch/batch transaction, sem scripting engine.

**Autotile simples:** mapa de regras declarativas de vizinhança para tile top/side/corner em faces coplanares de grade. Evitar solver universal e dependência de ML.

**Tile Variation Paint:** substituir aleatoriamente tiles por grupo com seed persistente/preview; seed determinística para exports reproduzíveis.

**Asset Shelf:** pequeno catálogo reutilizável de grupos transformáveis e referências de atlas; pode começar como snapshots imutáveis.

## Estudos separados

- Animated UV/sprites: Crocotile3D já suporta; exige serialização de frames/tempo, renderer e export target semantics.
- Sprite renderer: export planar isométrico ou spritesheet 4/8 direções; pipeline GPU/headless pode ser demorado.
- Procedural roofs/stairs/fences: templates limitados, preview reversível, não um graph universal.
- Semantic brushes (e.g., wall/window/door): só se a validação com usuários mostrar benefício ao invés de menus novos.

## Critérios de promoção

Só passa de proposta a backlog aprovado quando registrar: cenário real, número de passos poupados, custos de manutenção, avaliação com pessoa iniciante, suporte keyboard-only, riscos em geometry/UV e test plan, interação com existing tools e non-goals. Refusar recurso que exige produto inteiro adicional para ganho marginal.
