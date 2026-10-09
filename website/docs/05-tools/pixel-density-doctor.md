# Pixel Density Doctor — densidade consistente em arte pixelada

> **Decisão: incluir na V1 (T3D-015).** Especificação autoral aprovada como funcionalidade; **não implementada**. Detalhes de UI/algoritmo são baseline inicial, ajustável após protótipo. Depende de FaceCorner UV, atlas válido, faces avaliadas, bounds e Undo/Redo.

## Problema e resultado

Props feitos com tiles de 8, 16 e 32 px frequentemente exibem pixels de tamanhos diferentes no espaço 3D. O usuário não deveria abrir o UV Editor ou estimar escala olhando uma parede. O Doctor mede pixels por unidade de mundo em superfícies selecionadas ou na cena, destaca discrepâncias e sugere correções **que sejam geometricamente realizáveis**.

**Fluxo feliz:** selecionar objeto/face → `Verificar escala dos pixels` → overlay por severidade + tabela ordenável → escolher target em pixels/unidade e ação → preview visual/diff → confirmar uma transação → Undo disponível. Nunca corrigir ao abrir um projeto.

## Terminologia simples

- **Escala dos pixels** é o rótulo principal na UI; `texel density` aparece na ajuda avançada.
- `Pixels por unidade`: quantidade de pixels do atlas projetados em uma unidade do mundo, medida por direção tangente na superfície.
- `Alongamento`: diferenças entre direções (pixels retangulares/UV skew).
- `Pixels borrados` pode vir do sampler e não é necessariamente erro de UV; diagnosticar separadamente.

## Arquitetura sem engine de materiais extra

```text
DocumentSnapshot + TextureSize + evaluated world transforms
    ↓ tile-domain/density_query (Rust puro)
FaceDensityReport{face_id, pixels_per_unit_min/max, anisotropy, warnings}
    ↓ tile-app Query (read-only, revision keyed)
AccessibilityReport + ViewportOverlay
    ↓ explicit FixIntent (scope + mode + target)
Command: ReplaceUV / ResizeGeometry / RetileIfPossible
```

Para triângulos, derivar duas tangentes independentes da superfície em coordenadas mundo e comparar com o deslocamento UV convertido em dimensões de atlas, usando a transformação jacobiana local (SVD ou solução equivalente para 2×2 se realmente necessária). Para quads, analisar triangulação **derivada e não destrutiva**, agregando erro/anisotropia; aplicar o transform de mundo, inclusive escala não uniforme. Reject degenerate faces, nonfinite e UV sem triângulo válido com diagnóstico `not-measurable`, não divisão por zero. Cores do overlay não substituem texto/ícones.

### Invariante crítica: não falsificar correção

Uma UV não pode manter uma **TileRegion limitada** e, simultaneamente, garantir qualquer densidade arbitrária em uma face de dimensão livre. Portanto oferecer ações com pré-condições:
1. **Analisar / selecionar afetados:** sempre possível, sem escrita.
2. **Reaplicar mapeamento dentro do tile atual:** somente se target for atingível; provar por preview e bounds.
3. **Substituir tile pela variante de resolução apropriada:** somente se recurso compatível existir.
4. **Redimensionar a geometria (opt-in explícito):** altera proporções mundo e exige preview separado; nunca automático.
5. **Repetir/subdividir superfícies:** fase posterior e só para faces compatíveis; não extrapolar UV para outro tile do atlas e fingir que é repetição segura.

Se a correção não for possível, explicar: "Este tile tem 16 px e a superfície exigiria 32 px para a escala escolhida. Escolha outra região, redimensione a peça ou mantenha o aviso." Não recomendar re-unwrap genérico.

## Controles e acessibilidade

- Abrir por menu `Ferramentas > Escala dos pixels` e busca de comandos, sem shortcut exclusivo.
- Escopo `Seleção / Objeto / Cena`, target numérico com unidade, tolerância configurável, overlay on/off.
- Lista de problemas com colunas nomeadas `Parte / Face / Atual / Alvo / Causa / Correção possível`; `Enter` seleciona Face sem mouse.
- Link `Por que isso acontece?` expande explicação curta + exemplo visual, sem linguagem punitiva.
- `Esc` fecha preview/cancela; tecla de confirmação somente com foco explícito; High Contrast distingue direção de alongamento por símbolos/tramas.

## Prioridade e gates

**V1, após Surface Tile Stamp e modelo UV confiável** (implementação read-only pode começar antes). Testar: atlas não quadrado, tiles de tamanhos diversos, UV rotacionada/mirror, transform de objeto incluindo escala não uniforme, n-gon triangulado apenas para leitura, diferentes níveis de zoom, face degenerada, UV customizada, missing atlas, atualizações após Undo/Redo. Verificar que nenhuma opção produz coordenadas fora da região sem indicação e que export conserva aparência. CPU query cache por revision; não recomputar por frame ou bloquear a viewport.

## Fora de escopo

Auto-densidade invisível, "corrigir tudo" destrutivo, recriação arbitrária de texels, pack de atlas, conversão para PBR/normal maps. Não adicionar `xatlas` nem geometry solver pesado para esta ferramenta.
