# Tile Variations — diversidade determinística sem perder controle

> **Decisão: incluir na V1 (T3D-019).** **Não implementada.** Variação leve, previsível, opcional e acessível; sem runtime procedural, scripts ou gerador de mundos.

## Problema

Repetir o mesmo tile em parede/chão chama atenção e força escolher variações manualmente. A feature usa uma **Palette de variações**: um conjunto de TileRegions equivalentes com pesos (ou uniformes) aplicados durante o Brush ou sob comando explícito em faces já criadas.

## UX mais simples possível

1. Selecionar alguns tiles na Palette → `Criar grupo de variações`.
2. Aceitar pesos iguais (default) ou editar sliders/número numa lista nomeada.
3. Ativar `Variar ao pintar` no Tile Brush (toggle persistido em preferências/session, **desligado por padrão** no projeto já existente).
4. Preview/ghost mostra tile efetivamente sorteado para o alvo, estável enquanto o cursor permanece no mesmo ponto.
5. Paint/Place grava o **TileId específico** escolhido + source collection/seed metadata; export mostra sempre o mesmo visual.
6. Com faces existentes: `Revariar seleção` abre preview de antes/depois e confirmação, uma transação Undo.

## Algoritmo, seed e identidade

```text
VariantGroup {
    id: VariantGroupId,
    choices: [TileId + validated positive weight],
    algorithm_version: u16,
    seed: u64,
}
VariantChoiceKey = (GroupId, PlacementId or stable cell coords, user seed, algorithm version)
    → stable PRNG / hash → weighted choice → resolved TileId
```

Preferir PRNG/hash inteiro simples implementado em Rust puro com especificação versionada e fixtures. Não usar `HashMap` iteration order nem `std::hash::RandomState` para escolha estável. Usar **placement stable ID** após confirmar (não index de Vec), ou coordenadas inteiras canonizadas + object ID no preview antes do commit; garantir que preview não muda aleatoriamente a cada redraw. Se IDs gerados ao confirmar, reservar o ID no ToolSession para que preview/commit coincidam. `Reroll` incrementa salt/seed explicitamente e é um Command.

`seed` não é uma autoridade de UV alternativa: cada face persiste o TileId escolhido + FaceCorner UV como resultado visual final. Não recalcular textura ao carregar projeto com versão nova do algoritmo, exceto `Revariar` explícito; variante futura ausente é diagnóstico com `Relink` e preservação da UV já gravada.

## Pesos e validação

- Pesos `u16` positivos, limite de total para não overflow, prefix sum com inteiro largo; 0 é `disabled` explícito ou rejeitado conforme UX congelada.
- Grupo com uma única opção atua como Brush normal.
- Não permitir TileRegions invalid/out-of-bounds ou atlas não carregado no grupo ativo sem indicar `skip`/erro e fallback de preview.
- Variações com tamanhos de tile diferentes: `Fit` pode alongar escala; mostrar warning; preferir seleção de mesmo tamanho, não corrigir UV fora do atlas.
- Seed ao criar um grupo é explícita/visível e pode ser `Gerar nova` via ação; não exigir compreensão de números aleatórios para usuário comum.

## Limites e defaults

Começar em **faces selecionadas/células de brush**, sem regras contextuais automáticas de esquina/telhado (isso pertence a Smart Edge/Corner futuro). Sem shader random por face: CPU escolhe TileId e guarda UV uma vez, export fiel. Sem dependency `rand` se PRNG simples versionado satisfizer testes; não reinventar criptografia (não é security RNG).

## Interface e acessibilidade

Group list com thumbnails, nome de tile, peso (%) calculado e campo numérico; reordenação acessível por Up/Down, não só drag; preview `variação 2 de 4` além da cor. `Reroll` action com contagem, antes/depois e Undo. Usuários com epilepsia/Reduced Motion não recebem preview flicker; sessão congela escolha por cursor cell; wheel/palette não muda seed sem comando.

## Testes e gates

Mesma seed + mesmo placement/group → resultado idêntico em Windows/Linux, depois de save/reopen, após Undo/Redo e diferentes versões de Rust; distribuição de 1000 amostras para pesos (estatístico com tolerância), zero/overflow, rotated/mirrored, atlas missing, invalid region, locked faces, changes of group choices, no `reroll on redraw`, preview=commit, import/export preserving visual tile. Em grandes strokes, O(N) ou melhor e um Undo record.

## Dependências

Rust/std + structs + pequeno PRNG integer. Nenhuma crate procedural, physics ou AI.
