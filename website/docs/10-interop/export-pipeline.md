# Export — do Tile3D para jogos e editores

> Pipeline ainda não implementado. A PoC Odin mira OBJ + MTL primeiro; GLB depois.

## OBJ/MTL

ExportSnapshot compila objetos, faces, materiais por atlas e UVs em valores normalizados. O writer produz OBJ e MTL legíveis por Blender e ferramentas comuns. Arquivo PNG acompanha o material; paths devem ser relativos, sanitizados e sem referenciar diretórios arbitrários.

## Conversões explícitas

1. Aplicar transformações e definir unidade/orientação de eixos por opção.
2. Validar winding, normals, faces degeneradas, UV e textura ausente.
3. Deduplicar vertices de export apenas se preservar seams UV (posição igual pode exigir UV diferente).
4. Gerar índice e names determinísticos; aplicar bounding box e avisos.
5. Verificar em editor externo: dimensões, faces, UV, materiais, screenshot / script de comparação.
6. Nunca modificar a mesh autoral para otimizar export.

## Posterior

GLB/glTF, interop Godot/Unity, atlas packing, texture baking, simplificação de faces e export de cenas mais amplas. Não afirmar suporte até testes de leitura externa.

## Regras de qualidade

Se um formato não puder preservar convenção ou recurso, avisar por capability report, não ocultar dado ou exportar arquivo incorreto. Export é leitura de snapshot, nunca altera documento.
