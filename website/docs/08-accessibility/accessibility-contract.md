# Acessibilidade e experiência neuroinclusiva

> **Contrato obrigatório** para a PoC Odin. Dear ImGui foi selecionado, mas **não oferece suporte geral garantido a leitores de tela**. A integração com UIA/AT-SPI e a estratégia de acessibilidade semântica exigem estudo específico e validação.

## Princípios

A ferramenta deve preservar autonomia sem gestos de precisão obrigatórios, poucos conceitos de cada vez, interface com nomes claros, estado sempre visível e fluxo previsível para pessoas com distintas habilidades motoras, visuais e cognitivas.

## Keyboard-first

- F6 / Shift+F6 propõe transitar entre Header, Tools, Palette, Viewport, Inspector, Status.
- Tab navega dentro de zona; Esc cancela ToolSession antes de alterar seleção ou fechar app.
- Ações de placement possuem click-to-click, teclado com cursor virtual ou campos de posição/medida.
- Sticky Edge permite escolher aresta por teclado; Undo/Redo, Save e Export são acessíveis sem mouse.
- Comandos nomeados e atalhos remapeáveis; evitar obrigar usuário a manter modificador pressionado.

## Visão, densidade e escala

- Interface em 100–200% sem clipping e com área mínima útil para viewport.
- Tema alto contraste, foco explícito diferente de hover/selected, status textual, não somente vermelho/verde.
- Paleta de tiles nearest, grid legível, aumento de células/targets e opção para ocultar efeitos.
- Reduced Motion e animação evitável; tooltips não são a única fonte de ajuda.
- Feedback de orientação/snap também disponível como coordenada e descrição.

## Fluxo cognitivo

Começar por Importar/Exemplo; uma dica contextual por vez. Permitir nomes significativos, presets, recuperação de trabalho, confirmations proporcionais à perda de dados, Undo transacional. Erros descrevem causa + impacto + correção.

## Critério para GUI

Um widget renderizado visualmente não garante semântica nativa. Comparar a capacidade real das bibliotecas Odin de expor accessibility tree e leitores de tela Windows/Linux, incluindo Wayland. Se inexistente, registrar BLOQUEADOR ou planejar uma estratégia de adaptação verificável.

## Critérios de aceite

- Criar, orientar, apagar, desfazer e exportar tiles inteiramente por teclado.
- Inspecionar modo, tile ativo e foco sem depender da cor.
- Não perder a operação com zoom 200%, janela reduzida ou troca de painel.
- Erros não destroem documento nem forçam reinício da aplicação.
- Teste manual assistivo identificado por tecnologia e plataforma; não declarar conformidade WCAG/AT-SPI/IAccessible2/Windows UIA sem evidência.
