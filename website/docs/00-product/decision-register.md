# Registro de decisões, propostas e restrições

> Atualizado 2026-10-09. **Produto em descoberta**: decisão arquitetural de fundação não equivale a código pronto, nem preferência de produto é um gate verde.

| Código | Tema | Estado | Resumo |
|---|---|---|---|
| T3D-001 | Produto independente | baseline | repo `WASDst/petunia-tile3d`; separado do Petunia3D |
| T3D-002 | Reuso | baseline | contratos/algoritmos seletivos, sem dependência do frontend/app inteiro |
| T3D-003 | Rust lean | baseline | preferir Rust/std; justificar, auditar e versionar cada crate |
| T3D-004 | Desktop UI | protótipo requerido | Slint com feedback acessível; Go/No-Go antes de fixar |
| T3D-005 | Graphics | protótipo requerido | OpenGL 3.3 via glow/FBO; verificar composição real com Slint |
| T3D-006 | Tile-first model | baseline | cena contém geometria real + UV per face-corner e metadados de origem |
| T3D-007 | Document ownership | baseline | single writer via Commands/transactions/Undo |
| T3D-008 | Tilesets | baseline | TextureAsset e TileRegion em pixels, sem alterar mesh ao editar atlas por padrão |
| T3D-009 | Compatibilidade | baseline | projeto nativo versionado; GLB/OBJ via export, sem round-trip total presumido |
| T3D-010 | Acessibilidade | requisito | teclado completo, foco, UI scale, contraste, cognitive clarity |
| T3D-011 | Escopo MVP | proposta detalhada | importar PNG, palette, quad/sticky/block, seleção/transform, save/export |
| T3D-012 | Features diferenciadoras | em discussão | priorizadas por pesquisa; nenhuma automaticamente aprovada |
| T3D-013 | Licença do aplicativo | PENDENTE | crates Rust futuras sem licença definida; **website GPL-3.0-or-later** com proveniência documentada |
| T3D-014 | Code ownership/release | baseline | repositório e versionamento próprios; CI independente |

## Como alterar decisão

Cada proposta informa: problema, duas opções realistas, tradeoffs, evidência de usuário, custo de manutenção, risco para UX/acessibilidade e compatibilidade, decisão humana, data, aceite e plano de migração. Sem editar silenciosamente a baseline histórica.

## Ordem de autoridade

1. Instrução autorizada do usuário atual.
2. Este registro + [princípios](./principles.md) + contrato do domínio.
3. [AGENTS.md](https://github.com/WASDst/petunia-tile3d/blob/main/AGENTS.md) para execução.
4. Código real para status da implementação.
5. Referências do Petunia3D, Prumo e concorrentes como consulta, **nunca autoridade automática**.

## Status da implementação

Atualmente: website + documentação. **Nenhuma feature de desktop implementada, validada, lançada ou distribuída** neste repositório.
