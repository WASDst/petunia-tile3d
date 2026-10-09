# Regras de posicionamento, snapping e conectividade

## Workplane

Estado da ferramenta contém Workplane congelado no início do gesto (origin/basis normals), grid size em unidades do mundo, active TileRegion e target-face opcional. Modos: Ground, View, Face e Custom (MVP começa com Ground/View/Face).

## Place Tile

1. Mouse/teclado define world ray ou cursor virtual.
2. Query geométrica identifica hit face/edge ou fallback workplane.
3. Preview mostra corners, orientação, textura, validade e destino sem mutar Document.
4. Confirm faz `AddTileFaces` Command com `GeometryPatch`; cancel descarta.
5. Mapeia UV região selecionada independentemente da câmera, com rotate/flip determinísticos.
6. Seleção e status são atualizados por events.

## Sticky Edge

Detectar edge real com fonte/objeto ID, tolerância em pixels e direção orientada; prévia do novo quad compartilha vértices se pertence ao mesmo Mesh/objeto e cria faces coerentes. Se edge está bloqueada/seleção de objeto diferente, oferecer New Part ou warning — nunca weld entre objetos em silêncio.

Casos: concave angles, inverted winding, non-manifold border, UV seam, adjacent face duplicate e transformed object. A tool oferece local frame/angle numeric override e snap angular.

## Block Brush

O gesto pré-visualiza volume de caixa e faces externas texturizadas, não centenas de objetos por voxel. Ao confirmar, gere shell visível e aplique atlas mapping com orientação consistente por face. Subtrair bloco é operação distinta e só após testes de topologia; MVP pode usar Add-only e adiar subtract se invariantes ainda não estiverem garantidas.

## Brush repetition

Mouse drag é um GestureSession com cobertura visitada `HashSet<(workplane cell, orientation, object ID)>` para evitar stamps duplicados na mesma célula. Uma confirmação = um Undo. Para áreas grandes gerar patches/lotes e commit atômico; jamais checkpoint por movimento de pixel.

## Picking sem dependência de render GPU

Ray/triangle hit e snapped queries podem ser CPU com cache geométrico simples no MVP; framebuffer ID picking só entra se medição justificar. Highlight respeita depth/occlusion e se deve selecionar backface. Tooltip contextual explica alvo.

## Erros/feedback

Nada foi adicionado caso imagem ausente, UV inválida, target locked, face duplicada ou matriz singular. Exibir mensagem acessível com causa e ação; não criar "tile invisível".
