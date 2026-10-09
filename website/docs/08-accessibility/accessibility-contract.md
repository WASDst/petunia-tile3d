# Acessibilidade e facilidade — critérios de conformidade

> **Obrigatório**, não checklist opcional. Princípios inspirados em WCAG 2.2 AA quando aplicável; GUI desktop Slint não é HTML e exige validação de APIs de acessibilidade de SO e leitores de tela reais.

## Teclado end-to-end

Todos os fluxos essenciais realizáveis sem pointer: importar imagem, escolher tile na palette, navegar viewport via virtual cursor/numeric coordinates, colocar, Sticky edge selection, flip/rotate, Undo, Save e Export. `Tab/Shift+Tab` navega controles, `F6` transita regiões, `Esc` cancela ferramenta primeiro, `Enter/Space` ativa. Keymap remapeável, no obrigar chord com modifier mantido.

**Click-move-click** é alternativa explícita a drag-and-drop; não iniciar ações em `mouse-down` irreversíveis. Strokes e box brush têm entrada de dimensões e posicionamento numérico.

## Visual

- UI Scale de 100–200% sem cortar ações primárias em viewport comum; zoom do canvas independente da escala da UI.
- Focus ring persistente, 3:1 para contornos não-textuais significativos e 4.5:1 texto comum quando WCAG se aplicar; uso de forma/ícone/texto além de cor.
- High Contrast, dark/light bem definidos. Texturas pixel art podem usar original color, mas overlays acessíveis usam tokens escolhidos.
- Reduced Motion remove transição decorativa, não informações.
- Hover/pressed/active/disabled/selected visíveis e rotulados; tooltips seguem keyboard-focus também.
- Linha da seleção e tamanho de handles adaptáveis. Não informar error somente via toast efêmero.

## Cognição/neurodivergência

Ações têm nomes literais e consequências previsíveis. Paleta sempre apresenta tile selecionado + orientação. Interface progressiva: 5 ferramentas primárias e Advanced recolhido. Não forçar usuário a memorizar modificadores, mudanças de workplane ou conversão de UV; indicar na própria Work Surface o plano alvo. Dialog modal apenas para operações destrutivas, não micro decisões.

Linguagem inclusiva, explicações "o que aconteceu / por que / como corrigir", Command Search para localizar ações, histórico Undo legível e tutorial guiado opcional por tarefa.

## Screen reader / A11y tree

Slint `accessible-role`, `accessible-label`, `accessible-description` e `accessible-value` ou API efetivamente compatível com versão escolhida; não assumir suporte sem testar NVDA (Windows) e Orca (Linux) nos controles. Viewport 3D requer camada acessível de listagem/seleção de faces e anúncio de posição/orientação em texto.

## Cenários de teste

- Somente teclado: do tileset PNG até colocar dez tiles e salvar.
- Teclado com Sticky Edge: selecionar borda, confirmar ângulo 90°, Undo.
- 200% scale e 1366×768: funcionalidades primárias sem clipping.
- High Contrast/Reduced Motion: foco e preview ainda claros.
- NVDA/Orca: nome/role/state de tool, tile grid, numeric fields, overlays, errors.
- Touchpad/mouse: não causar jumps quando scroll na palette, wheel em numeric fields só com foco.
- Cognição: 5 usuários-alvo completam fluxo iniciante sem conhecimento de UV (meta de pesquisa, não resultado obtido).

## Gate

Um recurso não está `verified` se o percurso essencial depende de drag, icon sem accessible name, keymap não remapeável ou erro bloqueante inacessível. Documentar indisponibilidade real de AT/OS como `not tested`, nunca `passed`.

## Gates dos cinco diferenciais aprovados

| Feature | Percurso **sem mouse/drag** | Feedback não exclusivamente visual |
|---|---|---|
| [Pixel Density Doctor](../05-tools/pixel-density-doctor.md) | Abrir via Command Search → escopo/target numérico → percorrer diagnósticos por teclado → preview/cancel/fix | `Face X: 8 pixels/unidade, alvo 16, alongamento`, causa e ação sugerida |
| [Surface Tile Stamp](../05-tools/surface-tile-stamp.md) | Escolher tile na Palette → selecionar face por lista/teclado → escolher mapping → Enter/Undo | Nome do tile, alvo e resultado, orientação e distorção anunciados |
| [Replace Similar](../05-tools/replace-similar-tiles.md) | Escolher tile → `Selecionar semelhantes` → filtrar escopo → preview → substituir → Undo | `Encontradas N, ignoradas M`; motivos de exclusão acessíveis |
| [Tile Variations](../05-tools/tile-variations.md) | Criar grupo pela Palette → ajustar pesos por campos → alternar brush variation → confirmar placement/reroll | Variante selecionada e quantidades enunciadas, sem flicker |
| [Smart Wall/Roof](../05-tools/smart-wall-roof-brush.md) | Selecionar tool → preencher posição/dimensões/ângulo → preview → Confirmar/Cancelar | Estimativa de faces e tiles, pitch, invalid targets e cause |

Esses percursos precisam ser testados em Linux/Windows reais com foco, leitores de tela e UI Scale 200%. `Not tested` não é `pass`; alternativas por teclado existem mesmo para posicionamento no mundo via valores X/Y/Z e seleção de faces. Nunca promover uma feature como concluída somente porque o button Slint está desenhado.
