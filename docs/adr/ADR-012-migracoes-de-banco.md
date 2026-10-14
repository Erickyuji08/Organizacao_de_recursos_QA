# ADR-012 — Migrações de banco de dados

- **Status:** Pendente (modelo e ferramenta não escolhidos)
- **Data:** 2026-10-14

## Contexto

PostgreSQL é candidato, não aprovado; não há esquema nem migrations. Evolução controlada de um banco relacional precisa de migrações reproduzíveis, mas o modelo e as extensões dependem das decisões de ocupação.

## Problema

Como versionar e testar alterações de esquema sem escolher ferramenta ou estrutura antes de conhecer requisitos do modelo?

## Requisitos relacionados

RF-03..RF-09; RF-14; RNF-01/RNF-03/RNF-05; Q-01/Q-02/Q-04/Q-08/Q-16.

## Alternativas

1. Migrações SQL versionadas executadas por ferramenta Maven/Java.
2. Migrações declarativas por ferramenta com changelog.
3. Alterações manuais no ambiente.

## Decisão

Pendente de selecionar ferramenta. Recomenda-se usar migrações versionadas, revisadas e testadas em banco vazio e em versão anterior; não permitir alteração manual como mecanismo normal. Flyway e Liquibase são alternativas a avaliar; nenhuma está selecionada.

## Justificativa

Migrações versionadas oferecem histórico reprodutível e podem ser verificadas por Testcontainers/CI. A ferramenta deve ser compatível com Spring Boot, Maven e eventuais extensões PostgreSQL requeridas.

## Consequências positivas

- Histórico de esquema revisável e repetível.
- Teste de bootstrap/migração integrado à pipeline.

## Consequências negativas

- Mudanças retrocompatíveis e rollback podem demandar planejamento.
- Ferramenta adiciona dependência e convenções operacionais.

## Riscos

- Migração pressupõe semântica ou modelo não aprovados.
- Extensão necessária pode não estar disponível em todos os ambientes.
- Executar scripts manualmente ou em ordem divergente causa drift.

## Estratégia de validação

Após ADR-002 e Q-01/Q-02/Q-04/Q-08, comparar ferramentas e confirmar suporte a extensão necessária; executar migração em PostgreSQL Testcontainers do zero e atualização de versão anterior; validar rollback/estratégia de correção e execução repetível em CI.
