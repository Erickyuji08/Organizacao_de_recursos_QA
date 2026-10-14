# ADR-012 — Migrações de banco de dados

- **Status:** Aceito
- **Data:** 2026-10-14

## Contexto

PostgreSQL foi adotado em ADR-002. Ainda não existe esquema, porque as entidades e regras de negócio não foram implementadas. A estratégia de migração pode ser decidida sem criar tabelas nesta etapa.

## Problema

Como versionar mudanças SQL reproduzíveis e integrá-las ao ciclo de inicialização/teste Spring Boot?

## Requisitos relacionados

RF-03..RF-09; RF-14; RNF-01/RNF-03/RNF-05; Q-01/Q-02/Q-04/Q-08/Q-16.

## Alternativas

1. Flyway com migrações SQL versionadas.
2. Liquibase com changelog.
3. Alterações manuais no ambiente.

## Decisão

Adotar Flyway e arquivos SQL versionados no controle de versão. Migrações aplicam-se em ordem e não devem ser editadas depois de aplicadas em ambiente compartilhado; corrigir com nova migração. Não usar `hibernate.ddl-auto=update/create/create-drop`; Hibernate valida o esquema já migrado. Nenhuma migração de domínio é criada antes de o modelo ser aprovado.

## Justificativa

Flyway é uma estratégia direta e compatível com Spring Boot/Maven para executar SQL revisável, sem introduzir DSL/changelog adicional. Migrações podem ser verificadas em PostgreSQL real com Testcontainers.

## Consequências positivas

- Histórico de esquema revisável e repetível.
- Teste de bootstrap/migração integrado à pipeline.

## Consequências negativas

- Migrações aplicadas são imutáveis e correções exigem uma nova versão.
- Mudanças compatíveis e reversão de produção exigem planejamento explícito; não executar rollback automático destrutivo.

## Riscos

- Uma migração pode codificar regra/modelo ainda não aprovado se criada prematuramente.
- Extensão opcional pode não estar disponível em todos os ambientes.
- Execução manual ou ordem divergente causa drift.

## Estratégia de validação

Quando o modelo estiver aprovado, executar Flyway em banco PostgreSQL vazio e em banco com versão anterior usando Testcontainers; validar ordem, checksum e execução repetida. Revisar migrações no CI. Estratégia de recuperação é nova migração corretiva/restore operacional aprovado, nunca desativar validação ou editar migração aplicada.
