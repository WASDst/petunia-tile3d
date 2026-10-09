# Princípios do produto e critérios de exclusão

> Direção inicial recomendada; revisável com decisão registrada.

## Proposta de valor

A jornada principal é **tileset 2D → geometria 3D texturizada editável → cena ou asset exportável**, com mínimo de operações e compreensão de UV desnecessária. O usuário pode começar com poucos conceitos: Tile, Surface, Workplane, Part, Project.

## Desenho de experiência

- A ação primária sempre disponível é **Place Tile**.
- Selecionar tile no atlas produz imediatamente preview projetado na superfície/gride.
- Indicação explícita de onde e com que orientação será colocado antes de confirmar.
- Controles extras aparecem na Context Bar quando fazem sentido; Advanced recolhido.
- Undo/Redo de um gesto; cancel sem mutação; cada operação contextual fornece nome e próximo passo.
- Acessibilidade como contrato de sucesso (não modo à parte): F6 sequencia zonas, labels, tooltips, foco visível, sem dependência exclusiva de mouse/drag ou cor.
- Tutorial interativo e exemplos simples são parte do produto; vídeos externos não são pré-requisito.
- Interface escalável para 100–200%, reduced motion, alto contraste; gerenciar preferências no editor, não no formato do projeto.

## Não objetivos

Não oferecer viewport multiparadigma de todo Petunia3D; renderer PBR completo; bone/rig v1; edição procedural universal; ECS genérico; plugin marketplace; cloud account, telemetry, online dependency, heavy GPU required. Um recurso só entra quando seu ganho em resultado supera custo cognitivo, código e superfície testável.

## Métricas de sucesso propostas

- Primeira peça 3D em menos de cinco ações após importar imagem (meta de UX a validar, não benchmark comprovado).
- Um iniciante conclui casa estilizada pequena sem abrir UV Editor.
- Teclado permite todos os fluxos cruciais, incluindo seleção de tile, orientação, placement, Undo, export.
- Arquivo corrompido nunca elimina um projeto válido; cópia recuperável.
- Tris/quads exportados com UVs corretas, sem desalinhamento de pixel por padrão.
- Projeto médio funciona em Linux e Windows modesto com orçamento a medir.

## Decisões versus hipóteses

**Consolidado nesta proposta:** produto independente, foco tile-first, reuse seletivo, Rust com deps pequenas, UX acessível, baseline Slint/OpenGL 3.3 condicionada a protótipo técnico.

**Para debate:** autotiling, presets de telhado, smart replacement, export de sprites, layers multiusuário, AI, ligação interativa com game engines. Pesquisas não são compromissos.
