# Protocolo para agentes — Odin Tile3D

## Solicitação de tarefa

~~~yaml
task:
  id: "<issue>"
  goal: "<behavior observável>"
  repo: "WASDst/petunia-tile3d"
  ref: "<branch e SHA confirmados>"
  docs: ["website/docs/..."]
  scope: ["menor pacote Odin afetado"]
  non_goals: ["features fora do vertical slice"]
  gates: ["odin test, build, screenshots e a11y conforme domínio"]
~~~

## 1 — Verificar

Leia decisões e specs do domínio. Inspecione código e testes reais; se só houver documentos, classifique **MISSING**. Não crie relatório afirmando implementação a partir de um protótipo de texto.

## 2 — Projetar

Testar primeiro TileRegion → CornerUV → Quad CPU. Depois preview/placement → Command/Undo; raylib viewport; JSON e OBJ. UI profissional e opções de GUI permanecem em pesquisa.

## 3 — Implementar

Preferir Odin core/vendor, structs simples, função com entrada/saída explícita, ownership/allocators claros. Sem acoplar geometry a raylib, sem render no domínio, sem toolkit no Document. Não usar Rust crates, Slint, Cargo ou Go sidecar na PoC.

## 4 — Revisar

UV, winding, invalid input, stale IDs/revisions, Undo atômico, cancel sem mutação, files/path validation, screen scaling, foco e teclado, FPS/CPU no hardware medido. Não confiar só em compilação.

## 5 — Comprovar

Registrar comando Odin, versão do compilador, plataforma, saída, SHA, status PASS/FAIL/NOT RUN/BLOCKED e pendências. Capturas nativas para visual, ações por teclado para a11y e import externo para export.

## 6 — Entrega

Descrever problema, fonte, mudança, resultado verificável, riscos e próximo passo. Atualizar docs no mesmo conjunto. Nenhum push forçado ou mutação de outro projeto por implicação.
