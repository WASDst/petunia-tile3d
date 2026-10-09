# Licenças, proveniência e distribuição

> **Pendência para aprovação humana antes de importar código.** Esta página é política de engenharia, não parecer jurídico.

## Verificado

O `Cargo.toml` da [branch Petunia3D analisada](https://github.com/WASDst/petunia3d/blob/refactor/architecture-foundation/Cargo.toml) especifica `license = "GPL-3.0-or-later"`. O repositório tem [LICENSE GPLv3](https://github.com/WASDst/petunia3d/blob/refactor/architecture-foundation/LICENSE). Portanto copiar parte relevante de código não é "copiar livremente sem obrigações".

O Petunia Tile3D é repositório separado e **não recebeu licença própria nesta entrega documental**; não declarar open-source aprovado ou redistribuir sem decisão do detentor de direitos.

## Caminhos viáveis (decidir)

1. **GPL-3.0-or-later também para Tile3D**: caminho conservador para integrar código Petunia3D GPL sob seus termos (mantendo notices e atribuições). Ainda checar terceiros.
2. **Código totalmente original que reproduz somente ideias/algoritmos gerais**: escolher licença independente, sem copiar expressão protegida nem assets de terceiros.
3. **Re-licenciamento explícito**: possível apenas com direitos e consentimentos de titulares apropriados, incluindo contribuidores; não assumir que estar na mesma org resolve.

Não transferir imagens do Crocotile3D, tilesets de terceiros, dados de usuário ou assets com licença incompatível. Créditos por inspiração não são licença de código.

## Controle de provenance

Cada cópia/adaptação deve registrar:
- repository URL e commit SHA; caminho de origem;
- direitos/licença declarados na origem;
- autorias/notices preservados;
- testes portados/modificados;
- divergências e razões técnicas;
- auditoria `cargo deny` ou ferramenta equivalente, se usada, sem adicionar ferramenta só por estética.

`LICENSE` e `NOTICE` só serão adicionados com licença aprovada. Até lá o website pode ser visto, mas isso **não concede licença para reutilizar seu conteúdo** automaticamente.
