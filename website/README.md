# Website — Petunia Tile3D / Odin

Site documental para uma PoC independente de modelagem tile-first em **Odin**. A arquitetura anterior Rust + Slint está substituída na **baseline prospectiva**; documentos históricos permanecem recuperáveis via Git.

## Rodar

~~~bash
python3 -m http.server 8080 -d website
~~~

Abra http://localhost:8080. O arquivo index.html não funciona integralmente em file:// porque as páginas são carregadas por fetch().

## Stack do site vs stack do editor

- **Site:** HTML + CSS + JavaScript, sem bundler. Retém shell documental responsivo e ferramentas de navegação/search do site do Petunia3D.
- **Editor (a implementar):** Odin, domínio independente, renderer inicial candidato raylib, GUI definitiva em aberto.
- O site depende de bibliotecas front-end em CDN (Web Awesome, Phosphor, Marked, Alpine e busca opcional com Fuse). Não confundir com a meta de editor desktop offline-first.
- Edite website/docs/manifest.json para adicionar/remover páginas da navegação. Manter links locais e status rastreáveis.

## Deploy

Pode ser servido como conteúdo estático. Na Vercel, utilizar este repositório com outputDirectory website (ver vercel.json da raiz). **Adicionar configuração não equivale a deployment publicado**.

## Proveniência

Frontend derivado do website Petunia3D sob GPL-3.0-or-later, com histórico e deveres indicados em LICENSE e NOTICE.md. Licença do executável Odin segue indefinida.

## Stack de interface de longo prazo

A seleção do shell é **Odin + Dear ImGui com docking completo**. A [stack completa](docs/07-ui/imgui-stack-complete.md) contém módulos futuros, contratos e critérios de adoção; a [próxima decisão](docs/13-engineering/graphics-backend-decision.md) compara SDL3+OpenGL3, SDL_GPU3 e WGPU. Bibliotecas não são consideradas integradas só por constarem no roadmap.

## Renderer escolhido (11/10/2026)

O baseline aprovado é **Odin + SDL3 + OpenGL 3.3 Core + Dear ImGui docking completo**, com viewport offscreen em FBO e desenho por `ImTextureRef` na API ImGui 1.92+. Não existe editor executável neste repositório. [ADR](docs/13-engineering/graphics-backend-decision.md) · [Integração](docs/13-engineering/sdl3-opengl3-integration.md) · [Gizmo](docs/07-ui/imguizmo-integration.md).
