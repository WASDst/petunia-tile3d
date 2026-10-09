# Auditoria: o que aproveitar do Petunia3D

> **Fonte examinada:** [Petunia3D, branch refactor/architecture-foundation](https://github.com/WASDst/petunia3d/tree/refactor/architecture-foundation), 2026-10-09. Diagnóstico por leitura de arquivos específicos; não equivale à execução de testes no código fonte nem garantia de estabilidade das crates.

## Inventário de oportunidades

| Origem | Realidade observada | Recomendação Tile3D | Por quê |
|---|---|---|---|
| [mesh](https://github.com/WASDst/petunia3d/tree/refactor/architecture-foundation/crates/mesh) | Mesh/Face com `verts: Vec<u32>` e `uv: Vec<[f32;2]>` paralelos; dep. `geo`, `manifold-rust`, `xatlas-rs-v2` | **EXTRAIR algoritmos leves / ADAPTAR tipos; não importar crate toda** | invariantes de UV importantes, mas deps e formatos legados caros |
| [mesh/uv.rs](https://github.com/WASDst/petunia3d/blob/refactor/architecture-foundation/crates/mesh/src/uv.rs) | project planar/cube e unwrap externo | **REUTILIZAR lógica elementar**; adaptar para `FaceCorner` | Tile Brush nasce com UV explícito e não precisa auto-unwrap |
| [mesh/poly_pen.rs](https://github.com/WASDst/petunia3d/blob/refactor/architecture-foundation/crates/mesh/src/poly_pen.rs) | padrões de face creation | **ESTUDAR / ADAPTAR** | tile placement não deve herdar UI/tool lifecycle |
| [core/inference](https://github.com/WASDst/petunia3d/tree/refactor/architecture-foundation/crates/core/src) | snapping pixel-based e multi-object previstos em docs, implementação relatada | **REUSAR matemática depois de auditar** | evitar reescrever prioridades e tolerância |
| [project/material.rs](https://github.com/WASDst/petunia3d/blob/refactor/architecture-foundation/crates/project/src/material.rs) | Material inclui PBR/Toon/Glass e `Canvas` multimodal | **NÃO DEPENDER** na baseline | Tile3D precisa texture atlas + Unlit e alpha, não PBR completo |
| [project/prefab.rs](https://github.com/WASDst/petunia3d/blob/refactor/architecture-foundation/crates/project/src/prefab.rs) | Prefab snapshots e revision links | **ADAPTAR conceitos** | mini-asset reutilizável, mas não herdar Project legado inteiro |
| [ui-slint](https://github.com/WASDst/petunia3d/tree/refactor/architecture-foundation/crates/ui-slint) | Shell Slint modularização em progresso; ainda depende de WGPU | **REUSAR padrões, NÃO importar crate** | Tile3D precisa GUI específica e backend mais enxuto |
| [render-gl](https://github.com/WASDst/petunia3d/tree/refactor/architecture-foundation/crates/render-gl) | depende de `petunia_core`, `project`, `mesh`, `egui_glow`, glutin/winit | **REUSAR técnicas; nova boundary GL pequena** | dependência grande e acoplada |
| [module-uv](https://github.com/WASDst/petunia3d/tree/refactor/architecture-foundation/crates/module-uv) | chamadas mutáveis a `AppState` | **NÃO IMPORTAR** | manter Application e UV puros |
| [module-paint](https://github.com/WASDst/petunia3d/tree/refactor/architecture-foundation/crates/module-paint) | Paint de superfície com APIs Petunia | **FUTURO** | MVP pode editar PNG externamente, reload não destrutivo |
| [docs 03-geometry](https://github.com/WASDst/petunia3d/tree/refactor/architecture-foundation/website/docs/03-geometry) | contratos aprovados para Geometry Source/FaceCorner/Snap | **REUTILIZAR PRINCÍPIOS** | documentação de Petunia3D não é automaticamente requisito de Tile3D |

## Caminho seguro de reuso (recomendação)

1. Rodar e medir `cargo tree -p petunia_mesh`, perfis de binary, e testes reais em HEAD do Petunia3D (ainda não feitos aqui).
2. Criar aqui um `tile-domain` muito pequeno com Rust/std, sem depender de Petunia3D. Comprovar TileRegion/UV e GeometryPatch.
3. Extrair apenas código comprovado e licenciado, com attribution nos commits, mantendo testes e fixtures originais. Opcional: biblioteca compartilhada com semver separada **somente quando dois produtos consumirem de verdade**.
4. Evitar dependência Git flutuante em branch `refactor/architecture-foundation`. Pin commit SHA ou release tags; não importear o workspace inteiro.
5. Validar independência: `petunia-tile3d` compila/roda sem clonar ou instalar o aplicativo Petunia3D como um todo. Atalho `git clone petunia3d` não é arquitetura.
6. Quando interfaces divergirem, usar adaptador de dados de fronteira, não `cfg(feature)` cruzado em cada módulo.

## Riscos atuais que não vamos esconder

- Finalização de FaceCorner UV no Petunia3D ainda é direção arquitetural, não comprovadamente refatorada no código.
- O Slint oficial pode depender de backends internos que exigem validação da solução GL. Nada de prometer renderer totalmente sem WGPU sem PoC real.
- Copiar primitivas + Project + Render pode importar PBR, zip, raytracing/physics, egui, wgpu e outros transitivos desnecessários.
- Copiar file format Petunia3D antigo sem semântica de atlas/tile não traz interoperabilidade verdadeira.

## Critérios de aceite para cada extração

Interface pública pequena, testes determinísticos de propriedade/regressão, compatibilidade de licença, `cargo tree` documentado, sem dependência do frontend Petunia3D, sem regredir perfis low-end, provenance e changelog.
