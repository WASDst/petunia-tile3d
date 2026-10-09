# UX — layout, workspaces e interação Slint

> Design alvo, sujeito a protótipo Slint/FemtoVG/OpenGL validado. Não copiar toda a GUI complexa do Petunia3D.

## IA da interface: TILES / EDIT / PAINT?

**MVP deve abrir no modo BUILD**, sem forçar usuário a aprender um seletor de workspace. O projeto pode possuir três **contextos**, não três motores:

1. **BUILD** — primário, Tile Brush/Sticky/Block, viewport 3D + tileset sempre à vista.
2. **EDIT** — manipular faces/UV/objetos, com ferramenta mais técnica em disclosure.
3. **TEXTURE (V1)** — edição simples do atlas dentro do app somente após necessidade comprovada; MVP suporta editor externo + Reload.

Modos são *presets de painel e ferramenta*, não tipos distintos de geometria/Document. Alternar não destrói o estado nem força export/import intermediário.

## Estrutura proposta

```text
┌─────────────────────────────────────────────────────────────┐
│ Project   Undo/Redo      BUILD / EDIT     Help    Settings  │
├──────┬──────────────┬───────────────────────┬──────────────┤
│ Tool │ Tileset      │                       │  Parts       │
│ Rail │ Palette      │   3D WORK SURFACE     │  Properties  │
│      │ select/grid  │   Preview + snapping  │  (context)   │
│      │ tiles        │                       │              │
│      │              │   Context Bar         │              │
├──────┴──────────────┴───────────────────────┴──────────────┤
│ Status, Commands, Export, Accessible Feedback              │
└─────────────────────────────────────────────────────────────┘
```

Para janelas estreitas o Palette vira painel alternável sem cobrir permanentemente a viewport. `Viewport` tem área mínima utilizável; medidas finais dependem de testes reais em 1366×768 e com UI Scale 200%.

## Tool Rail

BUILD: Tile Brush, Sticky, Block, Select, Erase. EDIT: Select, Move, Rotate, Scale, Vertex/Edge/Face e Tile Stamp. Ferramentas infrequentes em flyout contextual, com labels padrão; icon-only apenas quando sem ambiguidades e sempre tooltip/accessible name.

## Tileset Palette

Zoom nearest (não borrar pixel art), grid on/off, tamanho do tile, recorte retangular livre, nomes e ID, atalhos de seleção por teclado, highlight forte, mini-preview no cursor, ações Rotate/Mirror. Indicar origem de arquivo, missing state, atualização externa. Não sacrificar painel do atlas a uma Asset Library genérica.

## Properties

Categorias curtas; controles recomendados somente quando uma propriedade aplica-se à seleção. Valores editáveis via digitação, drag sensitivity com limites e wheel quando foco explícito. Não mudar o valor ao rolar página quando campo não está focado.

## Feedback

Estados hover, active, focus, selected, disabled, loading e error verificáveis; toast para ação reversível leve, banner persistente/dialog para erro bloqueante; mensagens unem **causa + impacto + ação**, nunca "Failed" genérico. Save status e dirty indicator explícitos.

## Workspace transitions

Troca preserva seleção e documento; rejeitar requests durante transação aberta só com cancel/commit explícitos. `F6`: Header → Tools → Palette → 3D Work Surface → Structure/Properties → Status, com feedback de foco. Loop de Tab local previsível, Shift+F6 reverso. A sequência proposta será validada com usuários.

## Onboarding

Iniciar com "Importar tileset" ou "Experimente exemplo" e um cursor ghost sempre legível. Uma dica por tarefa, dispensável e recuperável, nunca tours modais longos; respectivo tutorial sempre disponível no Help.

## Não fazer

Não duplicar DRAW/POLY/UV/PAINT gerais; não criar quatro painéis fechados obrigatórios para fazer um quad; não esconder Undo, seleção ou alvos de foco em gestos exclusivamente com mouse.

## Entradas de UI das funcionalidades aprovadas — sem multiplicar workspaces

As cinco novas funcionalidades não criam modo global extra, sidebars permanentes nem segundo editor UV:

| Ação | Onde aparece primeiro | Onde aparecem ajustes avançados |
|---|---|---|
| [Surface Tile Stamp](../05-tools/surface-tile-stamp.md) | BUILD/EDIT: Palette → `Aplicar na superfície` ou face context menu | Context Bar: Fit, Orientation, Keep Pixel Scale |
| [Select/Replace Similar](../05-tools/replace-similar-tiles.md) | Context menu do tile/face e Command Search | Dialog curto: escopo, contagem, ignoradas, preview |
| [Tile Variations](../05-tools/tile-variations.md) | Palette: `Criar variações`; Brush: `Variar ao pintar` | Lista de pesos, seed, reroll |
| [Pixel Density Doctor](../05-tools/pixel-density-doctor.md) | `Ferramentas > Escala dos pixels` e Command Search | Painel diagnóstico não modal, com `Corrigir...` opt-in |
| [Wall/Roof Brush](../05-tools/smart-wall-roof-brush.md) | BUILD: `Construir parede` flyout; `Telhado` quando houver suporte | Context Bar: altura, comprimento, pitch, tile top/bottom |

Não colocar todas as cinco no Tool Rail principal. Na first-run experience preservar **Tile Brush, Sticky, Select** como call to action mais imediata; secundárias em menu, Palette e Inspector. Tooltip, nomes, feedback de preview e Undo comuns; teclado/F6 e leitor de tela alcançam cada ação sem hover ou drag.

No `Pixel Density Doctor`, legenda não depende só de vermelho/verde. No `Replace Similar`, anunciar quantas faces serão alteradas/ignoradas e por quê. No `Surface Tile Stamp`, mostrar mapeamento e distorção antes do click. No `Wall/Roof`, contagem de quads e custo antes de aplicar. No `Variations`, não sortear novos resultados a cada render.
