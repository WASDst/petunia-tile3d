# Interface proposta — foco no tile

> Modelo de experiência **toolkit-agnostic**. O protótipo visual em raylib é provisório, enquanto a GUI definitiva ainda será analisada.

## Primeiro contato

Abre com escolha **Importar PNG** ou **Usar exemplo**. A tela principal possui:

~~~text
┌─────────────────────────────────────────────────────────────┐
│ Projeto • Salvar      Undo/Redo     BUILD / EDIT      Ajuda │
├──────┬───────────────┬──────────────────────────┬───────────┤
│ Tool │ Tileset       │                          │  Parts    │
│ Rail │ Palette       │       VIEWPORT 3D        │  Details  │
│      │               │  cursor / preview / grid │  (context)│
│      │ grid / region │                          │           │
├──────┴───────────────┴──────────────────────────┴───────────┤
│ Status + coordenadas + preview + atalhos + erros acionáveis│
└─────────────────────────────────────────────────────────────┘
~~~

**BUILD** é o contexto padrão; **EDIT** para mudanças de faces/objetos. Futuro TEXTURE somente se validado. Não replicar todos os workspaces do Petunia3D.

## Tileset Palette

- Regiões por célula 8/16/32/custom ou seleção livre com limites inteiros.
- Zoom nearest-neighbor, grid legível e cursor/seleção de alto contraste.
- Tile selecionado cria preview 3D imediato; Rotate/Flip acessível por botão/atalho.
- Referência de origem e missing state claros; edição externa + Reload inicialmente.

## Interação

- Ferramenta persistente com cancelamento Esc; cada arrasto produz somente um Undo.
- Movimento de câmera não faz commit acidental.
- Sticky Edge mostra borda ativa, orientação e face futura antes do clique.
- Parâmetros têm campos numéricos que aceitam teclado sem movimentos obrigatórios de mouse.
- Seleção e contexto sobrevivem à troca de modo, salvo cancelamento explícito.

## Navegação acessível

Proposta: F6 percorre Header → Tools → Palette → Viewport → Inspector → Status, Shift+F6 volta. Estados nomeados (selected, hover, focus, disabled), tooltips e atalhos remapeáveis. Controles icon-only só quando comprovadamente compreensíveis.

## Evolução V1

As cinco ferramentas aprovadas entram como ações contextuais: Surface Tile Stamp na Palette/face, Similar no menu, Variations na Palette, Pixel Density Doctor no menu Tools, Smart Wall/Roof como derivação do Wall Brush. **Não abrir cinco sidebars permanentes.**

[Contrato de acessibilidade](../08-accessibility/accessibility-contract.md) · [GUI em avaliação](./gui-options.md).
