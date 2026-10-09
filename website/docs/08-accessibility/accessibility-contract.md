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
