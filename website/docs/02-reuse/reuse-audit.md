# Inspiração Petunia3D — sem migração

A PoC Odin **não portará** o Petunia3D nem adicionará crates Rust ao build. Essa mudança substitui o antigo plano de reuso direto. O projeto independente preserva sua própria experiência, releases e documentação.

## Reuso conceitual

| Fonte | Aproveitamento | Política |
|---|---|---|
| Shape-first e workplane | UX e invariantes para posicionar tile | Reinterpretar para tile-first |
| Commands/Undo | Transação atômica por gesto | Implementação própria Odin |
| Câmera e picking | Cálculo matemático/contratos | Reimplementar e testar |
| FaceCorner UV | Mesmo princípio de dados | Schema Tile3D próprio |
| Export e validação | Fixtures de comportamento | Reproduzir testes sem copiar código |
| GUI Slint, crates egui e wgpu | **Não importar** | Estudo independente em Odin |
| Manifold, xatlas, boolean complexo | **Fora da PoC** | Apenas se surgirem necessidades reais |

## Regras

- Repositórios separados; jamais declarar feature implementada porque existe em Petunia3D.
- Código GPL-3.0-or-later pode impor obrigações a trabalhos derivados. Documentação e referência técnica não constituem permissão irrestrita para copiar.
- Registrar licença, URL, commit, trecho/algoritmo e alternativa independente antes de um port.
- Não confundir o objetivo de uma PoC simples com a arquitetura de um modelador de propósito geral.

[Proveniência](./license-provenance.md) · [Arquitetura Odin](../01-architecture/module-boundaries.md).
