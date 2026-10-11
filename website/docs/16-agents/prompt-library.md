# Biblioteca de prompts — Odin Tile3D

> Exemplos de tarefas para agentes. Adapte paths conforme código real; nenhum trecho de editor está implementado por esta página.

## P1 — Vertical slice TileRegion

Implemente um pacote Odin puro para Atlas/TileRegion com bounds inteiros, conversão consistente de pixels para UV por canto e Rotate/Flip. Adicione odin test para retângulos fora de limite, rotação x4, flip x2, atlas assimétrico e coordenada Y. Sem raylib, UI ou arquivo.

## P2 — Preview e PlaceTile

Crie ToolSession separada de Document e Command PlaceTile. Preview não pode alterar revisão, Esc cancela e um gesto completo gera um Undo. Eventos de mouse e teclado precisam passar pela mesma operação tipada. Teste camadas CPU antes da viewport.

## P3 — Raylib viewport inicial

Monte câmera, workplane, grid, um atlas PNG nearest, um quad com UV e picking sobre plano. Não construa um toolkit GUI. Medir alinhamento de textura, coordenadas e resize; isolar adapter raylib de geometry.

## P4 — Sticky Edge

Permitir extensão de quad pela borda escolhida, com snap e indicação visual; preserva UV e não gera duplicata em face invalidada. Inputs: mouse e teclado, direção/ângulo numérico. Casos extremos: normal invertida e face degenerada.

## P5 — Wall / Block Brush

Proporção, altura, dimensões, preview e Undo único. Construir somente casca externa e agrupar faces por atlas/material. Sem voxel engine. Validar winding, seleção e cancellation.

## P6 — Persistência e export

JSON versionado, IDs estáveis, asset paths relativos, save atômico e projeto reaberto idêntico. Gerar OBJ/MTL com UV por canto e PNG associado; inspecionar o arquivo em leitor externo.

## P7 — Teste técnico de GUI (sem decisão antecipada)

Construa a mesma UI mínima com atlas, viewport, numeric field, redimensionamento, foco/Tab/F6, tooltip e status na alternativa candidata. Registre licença, binding Odin, build Windows/Linux, acessibilidade semântica real, screenshots, desempenho e integração GPU. Compare com a shortlist em GUI options.

## P8 — Pixel Density Doctor

Implemente primeiro uma função de domínio para computar escala texel/world por face, reportando discrepâncias, sem alterar UV. Preview de fix opcional com Undo e caso impossível documentado. Não prometer retopology automática.

## P9 — Surface Tile Stamp

Aplique uma TileRegion a faces já criadas, com modos fit/orientation explicitados, UV preservation quando possível, preview de distorção e Undo agrupado. Sem UI duplicada.

## P10 — Select / Replace Similar

Percorrer a cena por TileBinding/atlas escopado, mostrar contagem de afetados/ignorados, preview, parâmetros de tolerância e Undo único. Nunca substituir face sem confirmação ou resolver tiles inexistentes silenciosamente.

## P11 — Tile Variations

Múltiplos TileRegion com pesos e seed documentada. Resultado não muda a cada render; persistir seed e vínculos. Testar determinismo, zero weights e atlas indisponível.

## P12 — Smart Wall/Roof

Gerar wall strips entre pontos num plano, com preview de altura/orientação, alocação proporcional de UV e facetas minimizadas. Roof é etapa posterior, sem grafo procedural universal. Validar encerramento da transação, edição numérica e edge cases.

## Para todos

Incluir classificação Observado/Missing/Partial, versão Odin, diff mínimo, tests e resultados, passos manuais, risco a11y, docs atualizadas, commit e próximos itens. Não inferir implementação de specs.
