# Dependências leves, Rust cru e decisões de bibliotecas

> Filosofia: **menor grafo de dependências que forneça software correto e acessível**, não "0 dependencies" dogmático. Cada crate nova explica porque std Rust não basta.

## Classifique por área

| Área | Implementação própria preferida | Dependência possivelmente justificada |
|---|---|---|
| IDs, revision, commands, Undo, workplane state | `std` e structs/enums locais | nenhuma inicialmente |
| TileRegion/UV/quad/mesh topology | Rust puro; math pequenos explicitos | `glam` **se benchmark e clareza justificarem** |
| Project manifest e migrations | formatos limitados e versionados | `serde` e `serde_json`, em vez de parser próprio vulnerável |
| PNG read/write | não reinventar codec complexo | `png` ou `image` com features mínimas; validar crates |
| GUI acessível desktop | não implementar toolkit completo | `slint` com features restritas e a11y real testada |
| GL API | encapsular buffers/shaders | `glow` + menor conjunto de window/context dependências compatível |
| GLB/OBJ | serializador limitado testado | `gltf-json`/pequena crate se reduzir erros de spec; validar |
| Random tile variants | algoritmo seed determinístico std possível | `rand` só se necessário |
| Logging/diagnóstico | `eprintln!` no POC, errors claros | `tracing` quando depuração real precisar |
| Persistence atomic | std::fs, tmp path, rename com cuidado | `tempfile` se riscos de arquivos temporários justificarem |
| Geometry complexa, boolean/auto unwrap | fora do MVP | `geo`, `manifold-rust`, `xatlas` **não por padrão** |
| ECS/engine/physics | fora da baseline | evitar `bevy`, `wgpu`, `rapier`, etc. sem ADR |

**Cuidado:** não confundir o site HTML, que tem CDNs, com a binary Rust. Documentação pode usar stack do website original; desktop deve funcionar offline.

## Custo medido

Adicionar dependency exige:
- problema concreto e por que std/local não resolve;
- compatibilidade de licenças (GPL Petunia3D quando copiar);
- cargo tree / features audit, binary size diff, compile time diff quando viável;
- security/Rust unsafe risk e maintenance;
- fallback/exit cost e API boundary.

Transitives importam: o arquivo `petunia_mesh/Cargo.toml` usa geo/manifold/xatlas; render-gl traz egui_glow + glutin; ui-slint usa WGPU em 2026-10-09. Não copiar esses crates em massa.

## Rust code quality

Módulos curtos, erros tipados na boundary, evitar estado global, `unsafe` isolado e documentado, ownership/borrowing claros. Allocations rastreáveis em stroke hot path; `Vec::reserve` e dados contíguos quando útil, sem overengineering. Evitar `Arc<Mutex<_>>` por padrão para editor single-writer; workers recebem snapshots imutáveis.

## Stack inicial candidata

Rust 2024 + std; Slint com acessibilidade; OpenGL 3.3 via glow/context provider, podendo glutin quando indispensável; serde/json e PNG decoder mínimo. **Não fixar versões ou lockfiles antes de PoC**, além de registrar target MSRV explicitamente quando decidir. O Petunia3D usa Rust 1.98.1, mas isso não obriga Tile3D a compartilhar o toolchain sem verificar.

## Segurança prática

Nunca fazer parser de PNG/GLB manual "porque Rust cru" sem validar spec e adversarial inputs. Menos dependências pode reduzir superfície, mas código próprio inseguro pode piorar o produto.
