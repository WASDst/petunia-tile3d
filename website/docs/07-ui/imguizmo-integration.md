# ImGuizmo em Odin — contratos para manipulação 3D

> ImGuizmo é extensão **RECOMENDADA para o MVP**, mas **nenhum binding nem teste de integração foram implementados**. Bibliotecas: [ImGuizmo](https://github.com/CedricGuillemet/ImGuizmo) (MIT) e [cimguizmo](https://github.com/cimgui/cimguizmo) (C API). OpenGL3 e Dear ImGui com docking aprovados.

## Função e limite da extensão

ImGuizmo desenha e calcula transformações (translate, rotate, scale). Ele não implementa a geometria do Tile3D, snapping do tileset, Undo ou seleção. É apenas um **ToolUI adapter** que produz deltas/matrizes temporárias a partir de view/projection + transform original.

Contrato por entrada de viewport: view/projection (float32 4×4, layout verificado), gizmo operation (TRANSLATE/ROTATE/SCALE), mode LOCAL/WORLD, transform inicial, snap, \`ImageRect\`, seleção e estado da ToolSession.

## FFI sem múltiplas cópias de ImGui

- \`odin-imgui\` escolhido e \`cimguizmo\` devem compartilhar **a mesma versão de Dear ImGui e compatibilidade ABI C/C++**. Não vincular duas cópias com contexts separados.
- Fazer lock de commits de Odin/Capati/ImGuizmo/cimguizmo/dear_bindings; automatizar build e smoke test Windows/Linux. O shim C da extensão pode precisar regeneração manual em branch pinada.
- Wrapper Odin isolado em \`ui/gizmo\`; API exporta apenas structs/procedures do projeto, não tipos ImGui no application layer.
- Inspecionar licença MIT e reter notices na distribuição.
- Não escrever um bridge manual que suponha offsets de \`ImGuiContext\`; ponte C deve usar headers/exports verificadas.
- Habilitar uso do mesmo draw list ou \`ImGuizmo::SetDrawlist()\` sobre o ViewportPanel; clipping limitado a conteúdo viewport e cuidado com widgets de overlay.

## Sequência correta por frame (modelo conceptual)

~~~text
ImGui.new_frame()
ImGuizmo.begin_frame()    # uma vez após NewFrame, conforme API pinada
ImGui.begin(ViewportPanel)
  measure content rect and ensure offscreen GL FBO
  render scene to FBO, restore GL state
  imgui.image(viewport_color_texture)
  compute displayed ImageRect (não janela toda)
  ImGuizmo.set_drawlist(current viewport drawlist)
  ImGuizmo.set_rect(ImageRect.min_x, min_y, width, height)
  ImGuizmo.set_orthographic(camera.is_orthographic)
  current = copy(selection.preview_or_original_transform)
  ImGuizmo.manipulate(view, projection, operation, space, &current, delta, snap)
  if ImGuizmo.is_using(): send transform preview intent
  if gesture begins/ends/cancels: route ToolSession transaction
ImGui.end()
ImGui.render()
~~~

O início e fim de gesto não devem ser inferidos apenas de \`IsUsing()\` sem verificar transições/tempos de input e foco: selecionar, cancelar, perder janela, mudar ferramenta, Undo durante drag, fechar painel e trocar documento precisam de regras explícitas.

## Matemática e transformações

- **Convenção única:** escolher handedness, up axis, unidades e column-major/row-major com fixture real. ImGuizmo aceita handedness por matrizes segundo documentação atual; isso não torna automaticamente coerentes nosso raycast e projection. Testar identidade, translação, rotação 90°, nonuniform scale e matriz com negative determinant.
- \`view * model * projection\` **não pode ser confundido** com \`projection * view * model\`; validar a API por transformação conhecida e shader GLSL. Recomendação: matrices 4×4 column-major com shader \`projection * view * model * vec4(position,1)\`, sujeito ao wrapper.
- LOCAL/WORLD e pivot (selection center, active element, cursor) precisam estar explícitos no ToolSession.
- O transform de múltiplas peças pode ser aplicado como delta sobre cada transform original, mantendo IDs, pivot e constraints.
- Snap não reside só no ImGuizmo: snap grid, workplane, angle, tile edge, nearest vertex e numeric fields são regras da Application. O gizmo pode exibir snap básico; preview deve sempre usar \`ToolConstraintSolver\` autoritativo para não divergir do resultado.
- Não permitir deformação de UV inadvertida ao transformar mesh; decisões de preservação UV são ferramentas/domínio.
- Em DPI 200%, \`SetRect\` usa **coordenadas do ImageRect ImGui**, não dimensões físicas do FBO; só matrices/projection usam aspect ratio correto.

## Undo e experiência

1. Mouse down no eixo manipulado → iniciar \`ToolSession\` com snapshot de origem.
2. No drag → calcular preview copy-on-write e atualizar overlay/render snapshot de preview, **sem mutação do documento**.
3. Mouse up/Enter → validar, gerar **um** TransformSelectionCommand com os deltas, registrar um Undo e aplicar como mutador único.
4. Escape/context lost/close panel/selection removed → descartar preview, restaurar UI e foco, sem Undo.
5. Inspector numérico, teclado e acessibilidade geram **o mesmo intent/constraint solver**; não dependem de arrasto com mouse.
6. A operação atual, snap/angle/mode devem ser anunciáveis e com foco visível e tooltip textual, e aparecer no status.

## Interações não cobertas pelo ImGuizmo

- ViewCube/orientação: investigar ImViewGuizmo ou um widget próprio; escopo separado de transform gizmo.
- UV gizmo: manipulação 2D e mirror/rotate UV terão componente próprio.
- Brush painting/plane snapping: gizmo visual não é motor de placement.
- Screen reader não vem pronto do ImGuizmo nem do ImGui. Necessário registry de ações e campos acessíveis ou alternativa nativa.

## Gate de integração

1. Converter matriz conhecida Odin ↔ wrapper ↔ GLSL sem transposição inesperada.
2. Mover/rotacionar/escalar mesh em vista perspective e orthographic.
3. Select/hover/gizmo/draggable handles separados de mouse capture da câmera; não vazar clique ao chão.
4. Dock resize durante drag; troca de DPI; viewport fechado; 2 views com ID de gizmo distinto.
5. Negative/mirrored transform, escala zero e bounds inválidos → validação sem corrupção.
6. Cancel/commit/Undo/Redo idempotentes e sem mutar documento a cada frame.
7. CI de compilação de bridge C e teste visual manual/documentado em Windows/Linux.

Fontes: [ImGuizmo documentação oficial](https://github.com/CedricGuillemet/ImGuizmo/blob/master/docs/documentation.md), [ImGuizmo example](https://github.com/CedricGuillemet/ImGuizmo/blob/master/example/main.cpp), [cimguizmo](https://github.com/cimgui/cimguizmo).
