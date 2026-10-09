# Website — Petunia Tile3D

Caderno de **criação de produto novo**, não documento de refatoração. Baseado na experiência e layout do [website do Petunia3D](https://github.com/WASDst/petunia3d/tree/refactor/architecture-foundation/website), mas conteúdo e manifesto independentes.

## Executar
```bash
python3 -m http.server 8080 -d website
```
Abra `http://localhost:8080`. Não abrir diretamente via `file://` porque `fetch()` usa arquivos Markdown.

## Stack do SITE (não do aplicativo Rust)
HTML, CSS e JavaScript. Sem bundler/npm/build obrigatório. A interface herdada usa Web Awesome, Phosphor Icons, Alpine.js, Marked e Fuse.js via CDN pinado; portanto **o website requer internet para a experiência completa**. O aplicativo desktop deverá ser offline-first e não herda essas dependências. Avaliar no futuro reduzir CDNs e colocar assets locais com integridade.

Novas páginas: adicionar `website/docs/<area>/<page>.md` e atualizar `website/docs/manifest.json`. `website/llms.txt` é o índice de contexto para assistentes. Conteúdo de pesquisa e propostas não recebe status de decisão aprovada.

Publicação: servir `website/` como diretório estático. Nenhuma implantação foi configurada somente por criar os arquivos.

## Fonte e direitos
Estrutura visual/roteamento adaptados do website Petunia3D; não copiar docs de refatoração como se fossem requisitos deste produto. Consulte `docs/02-reuse/license-provenance.md`.
