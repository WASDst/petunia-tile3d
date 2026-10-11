# Filosofia do Petunia Tile3D

> Baseline de produto para um **novo editor Odin**. Não é port, fork nem versão reduzida do executável Petunia3D.

## Propósito

Transformar regiões de imagens 2D em superfícies 3D editáveis, texturizadas e exportáveis **sem exigir um curso prévio de modelagem ou UV**. A ação primária é selecionar um tile e colocá-lo no espaço, com prévia previsível.

## Cinco princípios

1. **Tile-first:** escolhe-se o material no atlas antes de construir a geometria; uma face possui geometria real e UV explícita.
2. **Menor número de gestos:** Tile Brush, Sticky e Wall cobrem grande parte dos trabalhos de ambiente; detalhes avançados ficam contextuais.
3. **Acessível e gentil:** nomes visíveis, foco claro, teclado completo, gestos alternativos, contraste, zoom de UI e feedback que explica causa + ação.
4. **Odin simples e modular:** dados explícitos, pacotes pequenos, sem engine geral, ECS ou abstrações criadas sem consumidor.
5. **Interoperabilidade honesta:** salvar em formato versionado; exportar malhas e texturas sem prometer round-trip perfeito.

## Não objetivos da PoC

- Modelador universal com B-Rep, sculpting, rigging, physics ou UV unwrap automático.
- Voxel engine ou um cubo por tile.
- Cloud obrigatório, store de plugins, marketplace ou contas.
- GUI profissional completa e definitiva antes de medir os obstáculos reais.
- Reaproveitamento por import automático de crates Rust.

## Experimentos mensuráveis

Fluxo PNG → tile → quad → edge extension → parede → Undo → JSON → OBJ/MTL. Verificar UV em atlas com nearest-neighbor, consistência de escala, input alternativo, consumo de memória, FPS e depuração. 60 FPS com 10 mil faces é **alvo exploratório**, nunca resultado aferido. Registrar hardware.

## Origem das decisões

O roadmap preserva cinco diferenciais aprovados: Pixel Density Doctor, Surface Tile Stamp, Similar Tile, Tile Variations e Smart Wall/Roof. Nenhum está implementado. A seleção de GUI continuará em discussão; ver [decisões](./decision-register.md).
