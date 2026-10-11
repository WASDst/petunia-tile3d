# Code Agents — práticas de implementação Odin

> Normativo para executar tarefas deste repositório **após conferir o estado real**. PoC greenfield Odin. Nenhum código do editor foi construído até a última revisão documental.

## Leitura de entrada

1. [AGENTS.md](https://github.com/WASDst/petunia-tile3d/blob/main/AGENTS.md).
2. [Decisões](../00-product/decision-register.md).
3. [Arquitetura Odin](../01-architecture/module-boundaries.md).
4. [Como navegar](./reading-navigation.md).
5. [Protocolo](./implementation-protocol.md), [prompts](./prompt-library.md), [evidências](./evidence-handoffs.md).
6. [Catálogo Prumo](./workforce-index.md) e recursos específicos quando relevantes.

## Comportamento obrigatório

- Confirmar branch e SHA; ler fonte canônica **deste projeto**, não assumir que Petunia3D tenha código igual.
- Cada tarefa entrega um vertical slice pequeno e observável, não engine ou refatoração geral.
- Preferir Odin core/vendor existentes após auditoria de API/licença; não inventar biblioteca ou recurso de toolkit.
- GUI final é **decisão humana pendente**. Raylib é somente um candidato à PoC.
- Todos os movimentos podem ser reproduzidos/aceitos sem mouse; não chamar visualmente acessível de semanticamente acessível sem teste.
- Registrar testes executados e não executados; não inferir commit/push de instruções ou patches locais.

## Especialistas recomendados

Engine/geometry: implementer, engine-engineer, reviewer, tester. GUI: editor-engineer, ui-component-engineer, ux-architect, accessibility-reviewer. Render: renderer-engineer, performance-agent. IO: security-reviewer. Docs: documentation-maintainer. Catálogos originais Prumo são **referência de workflow**, e não uma imposição de stack Rust.

## Limites

Sem autoexecutar skills externas, sem cópia de código sob licença incerta e sem modificar Petunia3D. Uma mudança só recebe status DONE quando o fluxo completo funciona e há evidência.
