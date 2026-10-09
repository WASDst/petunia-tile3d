# Surface Tile Stamp — texturizar diretamente faces 3D

> **Decisão: incluir na V1 (T3D-016)**, após suporte confiável a FaceCornerUV e seleção de faces. **Não implementada**. O principal diferencial é reduzir o caminho entre escolher um tile e texturizar uma superfície.

## Problema e jornada

O fluxo convencional exige criar mesh, selecionar face, abrir UV Editor, projetar, dimensionar e alinhar. Com Surface Stamp: selecionar tile → apontar para face de malha existente → visualizar encaixe → clicar/pressionar Enter → a textura está aplicada. Deve funcionar sobre a geometria criada no próprio Tile3D e, **quando importação de meshes estiver implementada**, sobre a malha importada. A inclusão desta feature não aprova/importa automaticamente FBX/GLB de terceiros.

## Modos simples

- **Encaixar na face** (default para quad retangular/plano): região escolhida cobre a face, preservando rotação e mirror especificados no preview.
- **Manter escala dos pixels** (opt-in): preservar pixels/unidade dentro das limitações de TileRegion; se exceder bounds, bloquear e sugerir outra região/tesselação futura.
- **Projetar por plano** (advanced): usa Workplane/face frame, project explicit; usar apenas quando preview de distorção aceitável.

Não tornar `Tile Stamp` uma segunda ferramenta de Paint: ela altera UV + TileBinding, **não modifica bytes da textura** e não gera nova topologia por padrão.

## Contrato de domínio

```text
TileRegion + FaceHit/ObjectId/FaceId + MappingMode + Orientation
 → pure FaceStampPlan {
      uv_before, uv_after,
      binding_before, binding_after,
      warnings, transform_summary
   }
 → transient preview
 → StampSurfaceCommand(face refs + revision preconditions)
 → FaceCornerUV / TileBinding updated atomically
```

**Uma única fonte de UV:** `FaceCorner` persiste os valores usados em render/export; `TileBinding` é provenance/query metadata. Editar UV manualmente pode marcar o binding como `custom`; não reaplicar vinculação nem converter automaticamente.

Rectangular planar quads têm mapeamento bilinear sem alteração topológica, definido por orientação estável de corners (baseada em winding/frame e ordem local, não câmera em movimento). Triângulos recebem UV afim conforme orientação normalizada. Faces concavas, não planares ou com vários vértices exigem aviso de distorção e, na V1, podem ser `unsupported`/preview bloqueado em vez de subdivisão silenciosa. Preservar material slot, alpha, seam, winding, IDs e normals.

## UI e interação

- Palette continua visível no BUILD; botão contextual **Aplicar na superfície** e ação equivalente via busca/teclado.
- Hover mostra tile já orientado na face (ghost), contorno da face e rótulo `Encaixar`/ `Escala fixa`.
- `R` (remapeável) alterna rotação de 90º quando tool está ativa; opções visíveis para Rotate/Flip (sem atalhos obrigatórios).
- `Tab` ou controles direcionais percorrem faces elegíveis pelo painel `Partes`; `Enter` faz stamp; `Esc` cancela. A11y report anuncia parte, face, tile escolhido e mapeamento.
- Multi-select segue `Aplicar a faces selecionadas` com contagem/escopo, preview de resultado e **um Undo para todo lote**.
- Overwrite em UV customizada tem confirmação contextual com `Preservar/Aplicar/Cancelar`. Ação não pode apagar UV custom silenciosamente.

## Boundary e biblioteca

Geometria/UV math em Rust puro (`tile-domain` ou `tile-geometry`); App valida e aplica Command; Slint expõe intent e preview; Renderer não re-projeta nem grava bindings. Nenhuma dependência de `module-uv` legada do Petunia3D ou unwrap automático do xatlas. Reduzir GPU upload para UV dirty region e batching por atlas.

## Testes e DoD

Face quad orientada em planos X/Y/Z, UV orientation/rotation/mirror 16 casos; seleção invertida, perspectiva vs ortográfica, atlas 16×16 e 32×64, alvo com transform em objeto, multi-face batch, missing/out-of-bounds, pinned/custom UV, locked face/part, duplicate faces, Undo/Redo e roundtrip/export. Sem importar mesh, validar V1 em malhas Tile3D criadas via Brush/Block; somente anunciar importação quando real.

## Não fazer

Não gerar mesh paralela de projeção, não copiar superfície, não redesenhar PNG, não editar outras faces sem consentimento, não importar engine PBR ou UV editor inteiro.
