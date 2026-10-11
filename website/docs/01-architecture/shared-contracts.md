# Contratos de composição e fronteiras

Não existe obrigação de criar biblioteca compartilhada com Petunia3D durante a PoC Odin. O que compartilhamos agora são **conceitos testados**: Commands, documentos versionados, UV por canto, snaps, export e acessibilidade.

## Contratos mínimos

| Contrato | Responsabilidade | Invariante |
|---|---|---|
| TileRegion | Recorte do atlas em pixels | Bounds validados, coordenadas inteiras |
| TileBinding | Proveniência da textura | Não substitui UV persistida |
| TileFace | Geometria e 4 UVs | Winding e normal verificáveis |
| Document | Assets e entidades versionados | Nunca possui handles GPU |
| ToolIntent | Pedido de ação | Não muta estado persistido |
| CommandResult | Resultado de transação | Uma revisão + um Undo por gesto |
| RenderSnapshot | Estado compilado de visualização | Não pode modificar autoral |
| ExportSnapshot | Geometria/UV/texture para arquivo | Estável durante serialização |

## Futuras extensões

- Face quad não significa voxel; split/triangulação pode ser derivado.
- Onde houver índice, usar handle estável com geração/revisão para impedir referências obsoletas.
- Regras de snapping e pixel density são do domínio, não do backend de UI.
- GUI, Lua, MCP, plugins e ABI não entram no protótipo sem necessidade demonstrada.

## Interoperabilidade com Petunia3D

OBJ/GLB como formatos de intercâmbio futuros; não dependências binárias internas. Nenhuma extração compartilhada até existir prova de pelo menos dois consumidores reais.
