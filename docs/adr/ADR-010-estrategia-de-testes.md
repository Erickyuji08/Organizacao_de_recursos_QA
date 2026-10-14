# ADR-010 — Estratégia de testes automatizados

- **Status:** Aceito
- **Data:** 2026-10-14

## Contexto

O projeto exige testes automatizados, rastreabilidade e CI. O ambiente não tem aplicação nem pipeline ainda. As metas para a implementação são 80% de cobertura de linhas e 70% de branches, sem reduzir os limites para tornar o pipeline verde.

## Problema

Como verificar camadas e requisitos com testes rápidos e reproduzíveis, incluindo comportamento real do PostgreSQL?

## Requisitos relacionados

RNF-01/RNF-02/RNF-03/RNF-04/RNF-05; CTX-02; POL-02; Q-16.

## Alternativas

1. Testes unitários isolados.
2. JUnit 5, Spring Boot Test/MockMvc e Testcontainers/PostgreSQL, com JaCoCo.
3. Testes somente end-to-end.

## Decisão

Adotar JUnit 5 para testes unitários de domínio e casos de uso; Mockito apenas quando isolar dependências produzir valor; Spring Boot Test e MockMvc para API; e Testcontainers com PostgreSQL para integração, migrações, transações, autorização e concorrência. Não usar H2 como substituto do PostgreSQL. Testes concorrentes devem usar conexões independentes e validar estado final, rollback e resultados.

JaCoCo mede classes de produção dos módulos incluídos no build; testes, código gerado e dependências externas não contam como produção. O gate exige no mínimo 80% de cobertura de linhas e 70% de branches para o conjunto de produção, sem exclusões ad hoc para contornar falhas. A CI também exige rastreabilidade de 100% dos requisitos classificados como críticos na RTM e zero defeitos/vulnerabilidades críticas nos critérios de entrega. A classificação de criticidade e automação da rastreabilidade devem ser estabelecidas sem inventar valores; até então, a evidência é revisada e registrada manualmente.

GitHub Actions executará `./mvnw -B verify` com Java 21 e Testcontainers; usar versões estáveis compatíveis de actions e fixá-las por SHA revisado. SonarCloud pode consumir relatórios JaCoCo no futuro, mas não é pré-requisito nem se presume token ou ID de projeto. API/BDD de aceitação cobre apenas critérios aprovados, sem transformar perguntas abertas em expectativas.

## Justificativa

Testes unitários oferecem feedback rápido, mas não provam comportamento de locks, transações ou constraints. Testcontainers fornece o PostgreSQL real. Gates quantitativos detectam regressão sem dispensar valor comportamental dos testes.

## Consequências positivas

- Evidência por camada e ligação a IDs da RTM.
- Reproduz concorrência no banco em vez de simular exclusão na JVM.
- Pipeline verifica os limites de cobertura e os testes PostgreSQL de forma reproduzível.

## Consequências negativas

- Testes PostgreSQL aumentam tempo e dependência de runtime/container.
- O gate de cobertura pode exigir trabalho adicional de testes úteis; não mede por si só qualidade ou correção.

## Riscos

- Testes concorrentes flakey por sincronização ou ambiente não controlado.
- H2/mocks serem tratados indevidamente como validação do mecanismo de banco.
- RTM indicar evidência sem execução real ou cobertura excluir código sem justificativa.

## Estratégia de validação

Validar o build Maven, relatórios JaCoCo e limites no pipeline GitHub Actions; verificar que `verify` falha abaixo de qualquer limite. Confirmar execução real dos testes com PostgreSQL Testcontainers em CI e revisar rastreabilidade crítica com evidências. Não há gate de carga sem workload/SLO aprovado.
