<div class="product-hero">
  <div class="hero-copy">
    <div class="hero-kicker"><span class="hero-kicker-dot"></span> PO<span aria-hidden="true">·</span>C / ODIN <span class="hero-kicker-meta">11 OUT 2026</span></div>
    <h1>Desenhe com tiles.<br><span>Construa em 3D.</span></h1>
    <p>Um editor nativo, acessível e direto para criar pequenos mundos, cenários e objetos low-poly a partir de tilesets 2D.</p>
    <div class="hero-actions">
      <a class="hero-primary" href="#/docs/12-roadmap/milestones.md">Explorar o roadmap <span aria-hidden="true">↗</span></a>
      <a class="hero-secondary" href="#/docs/01-architecture/module-boundaries.md">Arquitetura Odin →</a>
    </div>
    <div class="hero-meta"><span>● Em especificação</span><span>Odin + Dear ImGui</span><span>ImGui docking</span></div>
  </div>
  <div class="hero-visual" aria-label="Ilustração conceitual: tiles coloridos organizados em um pequeno objeto tridimensional">
    <div class="visual-bar"><span class="visual-lights">● ● ●</span><span>tile3d / viewport</span><span>◎</span></div>
    <div class="visual-stage">
      <div class="visual-grid"></div>
      <div class="visual-iso iso-back"></div><div class="visual-iso iso-left"></div><div class="visual-iso iso-right"></div><div class="visual-iso iso-top"></div>
      <span class="axis axis-x">X</span><span class="axis axis-y">Y</span><span class="axis axis-z">Z</span>
      <div class="visual-tooltip">Tile Brush <span>↗</span></div>
    </div>
    <div class="visual-footer"><span>tileset.png</span><span>16 × 16 px</span><span>NEAREST</span></div>
  </div>
</div>

<div class="hero-section-label">NOVA FERRAMENTA · NOVA IMPLEMENTAÇÃO</div>

## Uma ideia simples, com ferramentas de verdade

Em vez de construir uma malha complexa antes de aplicar texturas, você seleciona uma região da imagem e cria a face 3D diretamente. Faces podem ser inclinadas, movidas, prolongadas e exportadas: **tile não é voxel**.

<div class="feature-grid">
  <a class="feature-panel" href="#/docs/05-tools/mvp-tools.md"><span class="feature-symbol">▦</span><strong>Tile-first modeling</strong><span>Escolha, visualize, coloque e estenda superfícies.</span><span class="feature-arrow">→</span></a>
  <a class="feature-panel" href="#/docs/07-ui/workspaces-and-shell.md"><span class="feature-symbol">⌗</span><strong>Fluxo sem distrações</strong><span>Atlas, viewport e ferramentas exatamente onde são úteis.</span><span class="feature-arrow">→</span></a>
  <a class="feature-panel" href="#/docs/08-accessibility/accessibility-contract.md"><span class="feature-symbol">✳</span><strong>Acessibilidade primeiro</strong><span>Teclado, foco, zoom, contraste e alternativas ao arrasto.</span><span class="feature-arrow">→</span></a>
  <a class="feature-panel" href="#/docs/13-engineering/dependencies-odin.md"><span class="feature-symbol">{ }</span><strong>Odin nativo</strong><span>Domínio leve e desacoplado da interface e da GPU.</span><span class="feature-arrow">→</span></a>
</div>

## O que vamos construir primeiro?

| Marco | Entrega verificável | Situação |
|---|---|---|
| **A — Fundamentos** | PNG + TileRegion, seleção, preview, quad e câmera | Planejado |
| **B — Modelagem** | Sticky Edge, Wall/Block Brush, snapping e Undo | Planejado |
| **C — Produto mínimo** | JSON versionado, OBJ/MTL, validação externa | Planejado |
| **V1** | Pixel Density Doctor, Surface Tile Stamp, Similar Tile e Variations | Aprovado para roadmap |
| **V1+** | Smart Wall/Roof Brush e expansão gradual | Aprovado para roadmap |

> **Status responsável:** apenas este website/documentação existe neste momento. Os recursos descritos são contratos de projeto, não funcionalidades disponíveis para download.

## Três escolhas conscientes

### 1. Criar do zero em Odin

Não migrar Petunia3D; nem carregar suas crates, GUI ou arquitetura Rust. Herdamos **aprendizados e padrões de qualidade** — não a implementação. [Leia o escopo](./02-reuse/reuse-audit.md).

### 2. Dear ImGui com docking desde a primeira versão

**Dear ImGui com docking completo foi aprovado**. A PoC deve começar pela integração do shell e de um viewport dockável. Ainda precisamos validar binding Odin, escolher o backend de janela/GPU e definir a estratégia de acessibilidade. [Ver decisão](./07-ui/gui-options.md).

### 3. Fazer o primeiro objeto antes de criar um framework

No centro do projeto existe um caminho: **tileset → face → snap → parede → Undo → exportar**. Sem ECS universal, B-Rep, render PBR ou sistema de plugins neste estágio.

<div class="next-reading">
  <span>COMECE POR AQUI</span>
  <a href="#/docs/00-product/decision-register.md">Decisões e pendências ↗</a>
  <a href="#/docs/05-tools/mvp-tools.md">Ferramentas da PoC ↗</a>
  <a href="#/docs/07-ui/imgui-ecosystem.md">ImGui e extensões ↗</a>
</div>
