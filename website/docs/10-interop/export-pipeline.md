# Import/Export, pixel pipeline e integração com games

## Entrada

MVP importa tilesets PNG (mínimo); opcional JPEG apenas como referência ou atlas opaco. Decode em worker limitado, validar dimensões/pixel count/memory; exibir erro claro e thumbnail placeholder. Arquivo externo nunca executa scripts. PNG read/write depende de biblioteca auditada de codec; não implementar compressor PNG "Rust cru" improvisado.

## Saída

Priorizar export de `GLB` para engine e `OBJ+MTL+PNG` como via secundária. Se escrever GLB, usar spec oficial com arrays/accessors/buffers e validação externa; adotar crate pequena de JSON/GLTF se reduzir erros. O objetivo não é zero deps a custo de formatos incorretos.

Export recebe Document snapshot avaliado. Tratar: quad triangulation winding, uv flip, texture paths embed/link, alpha mode, material unlit, coordinate axes, normals flat, scale e origin, nomes de objetos. Preview de perdas: tile metadata, animations, variant rules e links live não são representáveis em OBJ/glTF puro.

## Game-ready

Painel de verificação mostra tris, faces degeneradas, UV out-of-bounds, missing textures, materiais, texture filter recommendation, atlas overdraw e escala. Fixes sempre opt-in e undoable em documentos; export read-only.

## Integração Petunia3D

Troca básica por GLB/OBJ com aparência aproximada. Promessa de `Open in Petunia3D` somente quando existir protocolo/CLI público, negociações de versão e transfer fixture test. Não adicionar dependência do executável Petunia3D, nem modificar Petunia3D apenas para fazer marketing do Tile3D.

## Sprite export — proposta complementar

Export de imagens 2D renderizadas (PNG, múltiplos ângulos) é funcionalidade de outro nicho e será debatida depois: pode ser acessível, mas requer render target e transparência verificável; não entra no MVP por padrão.
