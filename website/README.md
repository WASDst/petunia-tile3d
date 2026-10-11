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
