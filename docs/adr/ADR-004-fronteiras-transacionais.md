# ADR-004 — Fronteiras transacionais

- **Status:** Aceito
- **Data:** 2026-10-14

## Contexto

Uma operação pode envolver a gravação da reserva e de seus vínculos com recursos; manutenção e aprovação também interagem com a agenda. A aplicação é um monólito modular e PostgreSQL é o banco adotado. Auditoria de negócio participa da mesma transação quando exigida pelo evento registrado, conforme ADR-007.

## Problema

Como garantir atomicidade de uma operação de aplicação sem manter transações abertas durante trabalho externo?

## Requisitos relacionados

REQ-01/REQ-03/REQ-04; RF-09/RF-10/RF-11/RF-14; Q-04/Q-05/Q-08/Q-11.

## Alternativas

1. Transação local por caso de uso de escrita.
2. Transações independentes por entidade/componente.
3. Transação distribuída entre serviços/datastores.
4. Persistência eventual com outbox, mesmo sem integração confirmada.

## Decisão

Delimitar transações na camada de aplicação/serviço por operação de escrita. A criação ou aprovação de reserva, os vínculos aos recursos e os eventos de auditoria correspondentes são confirmados ou revertidos em conjunto. Alterações de estado que ativem/desativem ocupação usam a mesma proteção e revalidação da agenda definida em ADR-003. Não executar chamadas HTTP, filas ou outro trabalho remoto dentro da transação.

## Justificativa

Transação local corresponde ao monólito e evita estado parcialmente persistido. A transação, isoladamente, não evita corridas; deve combinar-se com locks de recurso e revalidação em ADR-003.

## Consequências positivas

- Falha de gravação produz rollback de alterações do mesmo caso de uso.
- Torna explícita a unidade atômica e testável.
- Evita chamadas externas prolongadas dentro da transação.

## Consequências negativas

- A unidade de trabalho pode aumentar contenção se incluir operações demais.
- Falha ao gravar auditoria exigida aborta a operação de negócio para não perder rastreabilidade.
- Mudanças de estado e locks requerem tratamento de concorrência.

## Riscos

- Transação ampla ou ordenação de locks inadequada causa contenção/deadlock.
- Falha de conexão após commit pode deixar resultado ao cliente ambíguo.
- Não há garantia de entrega de notificação externa por esta decisão.

## Estratégia de validação

Testar commit e rollback com PostgreSQL Testcontainers, injetando falha entre gravações correlatas; verificar que estado de reserva, vínculos e auditoria persistem atomicamente. Testes concorrentes usam conexões independentes e validam os locks definidos em ADR-003.
