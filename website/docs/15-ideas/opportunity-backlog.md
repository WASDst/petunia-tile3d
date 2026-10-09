# Backlog de oportunidades — aprovadas vs. propostas

> Atualizado em 2026-10-09. A aprovação do usuário **incorpora cinco funcionalidades ao planejamento**; outras oportunidades continuam candidatas para discussão. `Aprovada` = incluir na visão/roadmap, **não implementada**. As dificuldades abaixo são relativas e ainda não medidas.

## Funcionalidades agora aprovadas

| Feature | Benefício | Fase | Especificação |
|---|---|---|---|
| **Pixel Density Doctor** | consistência de pixels em superfícies | V1 | [Contrato + testes](../05-tools/pixel-density-doctor.md) |
| **Surface Tile Stamp** | colar tiles em faces sem editor UV | V1 | [Contrato + testes](../05-tools/surface-tile-stamp.md) |
| **Select/Replace Similar Tile** | atualizar dezenas de tiles numa única ação | V1 | [Contrato + testes](../05-tools/replace-similar-tiles.md) |
| **Smart Wall/Roof Brush** | gerar paredes e telhados básicos com poucos gestos | V1+ | [Contrato + testes](../05-tools/smart-wall-roof-brush.md) |
| **Tile Variations** | quebrar repetição visual com pesos/seed estável | V1 | [Contrato + testes](../05-tools/tile-variations.md) |

As cinco obedecem ao mesmo modelo autoral: `TileRegion/TileBinding + Mesh/FaceCornerUV`, Commands/Undo por gesto e preview sem mutar o Document. **Wall/Roof** não aprova um solver genérico de construções; **Surface Stamp** não aprova novos formatos de importação; **Density Doctor** não autoriza modificar mesh/UV silenciosamente.

## Outras hipóteses, ainda não aprovadas

| Ideia | Dor resolvida | Esforço relativo | Risco |
|---|---|---|---|
| **Prefab Quick Stamp** | reconstruir props similares | baixo-médio | revisão de vínculos/shared edits |
| **Rect-to-Room 3D** | gerar um cômodo completo | médio | escopo largo e geometria escondida |
| **Auto Corners inteligente** | escolher canto/edge pelo contexto | médio-alto | regras espaciais e falso encaixe |
| **UV Health avançado** | detectar overlap/bleeding por regra | baixo-médio | diagnóstico excessivo; parte já coberta pelo Doctor |
| **Live Texture Reload** | ver edição do pixel-art externo na viewport | baixo-médio | permissões de filesystem e watchers |
| **Export presets (Godot/Unity/PS1)** | escala, alpha e UV interoperáveis | baixo-médio | mudanças dos formatos |
| **Sprite sheet from 3D angles** | arte 2.5D a partir de cenas | médio-alto | viewport offscreen/export |
| **Roof/Fence templates adicionais** | padrões complexos | médio-alto | interface superlotada |

## Como testar antes de codificar

- Teste com iniciantes: construir uma casa simples e repetir elementos, comparando número de ações, erros e esforço percebido.
- Casos de aceitação por teclado, sem drag/hover; `F6`, labels, orientação textual, undo e preview previsível.
- Benchmark local com cenas de centenas/milhares de faces e poucos atlases; nenhuma afirmação de FPS sem medição.
- Extrair algoritmos Rust/std somente após confrontar matemática/UV com os contratos do Petunia3D.

## Pesquisas (não prova de exclusividade)

[Estudo Crocotile3D, Blockbench, Sprytile, TrenchBroom e Asset Forge](../14-research/competitive-landscape.md). Ter valor de produto não implica que concorrentes não ofereçam função semelhante.

## Fora de escopo

Runtime de jogo, colaboração ao vivo, rigging generalista, física, nuvem, text-to-3D IA e universal graph procedural.
