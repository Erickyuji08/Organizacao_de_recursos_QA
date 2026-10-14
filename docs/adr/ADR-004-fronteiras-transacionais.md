# ADR-004 — Fronteiras transacionais

- **Status:** Proposto (não aprovado)
- **Data:** 2026-10-14

## Contexto

Uma operação pode envolver a gravação da reserva e alocações correlatas; manutenção também interage com a agenda. Auditoria de produto é condicional a Q-11. A aplicação é proposta como monólito modular, com PostgreSQL candidato.

## Problema

Como evitar commits parciais entre gravações de uma mesma operação sem manter transações abertas durante integrações externas?

## Requisitos relacionados

REQ-01/REQ-03/REQ-04; RF-09/RF-10/RF-11/RF-14; Q-04/Q-05/Q-08/Q-11.

## Alternativas

1. Transação local por caso de uso de escrita.
2. Transações independentes por entidade/componente.
3. Transação distribuída entre serviços/datastores.
4. Persistência eventual com outbox, mesmo sem integração confirmada.

## Decisão

Propor fronteira transacional no caso de uso que altera uma invariante de negócio. Reserva e ocupações/alocações que forem parte da mesma decisão devem gravar/confirmar atomicamente. Manutenção deve coordenar a mesma proteção de agenda conforme Q-08. Se Q-11 tornar auditoria obrigatória, o evento e a alteração auditada participam da mesma transação local. Não incluir serviço remoto na transação.

## Justificativa

Transação local corresponde ao monólito e evita estado parcialmente persistido. Commit distribuído e outbox seriam complexidade sem integração aprovada. A transação não substitui uma constraint ou estratégia correta contra corridas.

## Consequências positivas

- Falha de gravação produz rollback de alterações do mesmo caso de uso.
- Torna explícita a unidade atômica e testável.
- Evita chamadas externas prolongadas dentro da transação.

## Consequências negativas

- A unidade de trabalho pode aumentar contenção se incluir operações demais.
- Auditoria obrigatória e atômica pode abortar negócio quando a auditoria falha.
- Mudanças de estado e locks requerem tratamento de concorrência.

## Riscos

- Transação ampla ou ordenação de locks inadequada causa contenção/deadlock.
- Falha de conexão após commit pode deixar resultado ao cliente ambíguo.
- Não há garantia de entrega de notificação externa por esta decisão.

## Estratégia de validação

Testar rollback/commit usando PostgreSQL Testcontainers e injeção de falha em cada gravação; provar atomicidade de reserva/alocações, manutenção e, se Q-11 exigir, evento de auditoria. Revisar limites transacionais por caso de uso.
