# Formato de projeto da PoC

> Novo documento Odin; formato **planejado** e ainda não estabilizado.

## Primeira persistência: JSON versionado

O editor deve salvar informações autorais e não o estado de GUI, GPU ou caches. Campos essenciais: schema_version, id, nome, tilesets (paths relativos, dimensões, hashes opcionais), objetos, faces, positions, corner UVs, tiles bindings e metadados mínimos. Preferir JSON para que o experimento seja debuggável.

~~~json
{
  "schema_version": 1,
  "project_id": "example-id",
  "tilesets": [
    { "id": "atlas-1", "path": "assets/tiles.png", "width": 256, "height": 256 }
  ],
  "objects": [
    {
      "id": "object-1",
      "name": "House",
      "faces": [
        { "id": "face-1", "atlas_id": "atlas-1", "vertices": [], "uv": [] }
      ]
    }
  ]
}
~~~

**Esquema ilustrativo incompleto**. Os campos vertices/uv serão arrays válidos de quatro posições/cantos no contrato final; não tratar este exemplo como fixture.

## Regras

- ID persistente não depende do índice em array ou handle GPU.
- Parse com limits: bytes máximos, número de faces, recursion, sizes e path traversal.
- Validar referência do atlas e ausência de textura; não apagar faces por missing file.
- Save atômico com arquivo temporário + replace e instruções de recuperação.
- Paths normalizados e relativos; nenhum recurso externo executável.
- Schema migrations explícitas; desconhecido não causa delete silencioso.
- Save/Load com snapshot consistente, sem ToolSession incompleta.

## Compatibilidade

Não prometer abrir formatos do Petunia3D sem converter. OBJ/MTL é export. Futuro GLB segue especificação glTF; os dados nativos poderão evoluir para contêiner após evidências.
