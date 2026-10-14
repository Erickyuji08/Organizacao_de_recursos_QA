# ADR-010 — Estratégia de testes automatizados

- **Status:** Proposto (não aprovado)
- **Data:** 2026-10-14

## Contexto

O contexto declara testes automatizados, TDD/BDD, ATAM, rastreabilidade e CI; não define escopo, ferramentas, cobertura, gates ou limiares. O pedido atual especifica testes de integração com Testcontainers. Não existe código nem pipeline no repositório.

## Problema

Como verificar regras de domínio, API, transações e concorrência sem confundir testes em memória com prova de integridade do banco?

## Requisitos relacionados

RNF-01/RNF-02/RNF-03/RNF-04/RNF-05; CTX-02; POL-02; Q-16.

## Alternativas

1. Apenas testes unitários.
2. Testes unitários + integração PostgreSQL em Testcontainers + testes API/aceite para fluxos aprovados.
3. Somente testes end-to-end em ambiente compartilhado.

## Decisão

Propor a alternativa 2: testes de domínio/casos de uso isolados; integração com PostgreSQL/Testcontainers para migrations, constraints, rollback e concorrência; testes de API para contratos e autorização; BDD/Cucumber apenas para cenários aprovados. CI Maven candidata a executar build, unitários e integração. Nenhum limiar de cobertura ou gate está escolhido até Q-16.

## Justificativa

Testes unitários oferecem feedback rápido, mas não provam comportamento de constraints/isolation. Testcontainers aproxima a prova do banco candidato; testes API validam a fronteira HTTP. Não se devem automatizar lacunas como expectativas.

## Consequências positivas

- Evidência por camada e ligação a IDs da RTM.
- Reproduz concorrência no banco em vez de simular exclusão na JVM.
- Pipeline inicial pode estabelecer baseline sem afirmar conformidade de desempenho.

## Consequências negativas

- Testes PostgreSQL aumentam tempo e dependência de runtime/container.
- Critérios de gate, cobertura, TDD/BDD e ATAM ainda precisam de acordo.

## Riscos

- Testes concorrentes flakey por sincronização ou ambiente não controlado.
- H2/mocks serem tratados indevidamente como validação do mecanismo de banco.
- RTM indicar evidência sem execução real.

## Estratégia de validação

Executar suites unitária, integração/Testcontainers e API em CI; confirmar logs/resultados por execução. Testes de carga/JMeter ficam fora do gate rápido até workload e métricas Q-16. Auditar amostra requisito → caso/teste → evidência sem preencher antecipadamente a RTM.
