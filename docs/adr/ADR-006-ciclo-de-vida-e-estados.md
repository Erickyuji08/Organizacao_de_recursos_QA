# ADR-006 — Ciclo de vida e estados

- **Status:** Aceito para proteção técnica da agenda; semântica de produto complementar permanece condicionada
- **Data:** 2026-10-14

## Contexto

As fontes declaram gerenciamento de reservas e aprovação especial sem um catálogo de estados. Para que a proteção concorrente seja executável, é necessário definir estados técnicos bloqueantes, sem transformar aprovação especial em pré-condição global de reserva.

## Problema

Como representar estados mínimos para proteger a agenda e definir quais transições precisam revalidar ocupação?

## Requisitos relacionados

REQ-01/REQ-02/REQ-03/REQ-04; RF-02/RF-07/RF-08/RF-09/RF-10/RF-11; Q-04/Q-05/Q-08/Q-14/Q-15.

## Alternativas

1. Estado mínimo `PENDENTE`, `APROVADA`, `REJEITADA` e `CANCELADA`, com ocupação explícita.
2. Estado único e decisão de aprovação não relacionada à agenda.
3. Não registrar estado persistido.

## Decisão

Adotar os estados mínimos `PENDENTE`, `APROVADA`, `REJEITADA` e `CANCELADA`. `PENDENTE` e `APROVADA` ocupam agenda e bloqueiam novos conflitos; `REJEITADA` e `CANCELADA` não ocupam. Uma transição de estado para ocupante deve bloquear os recursos em ordem determinística e revalidar conflitos na mesma transação antes de gravar. Uma transição para não ocupante libera a ocupação no commit, mantendo os locks até então. Aprovação de solicitação especial é uma decisão distinta e não é pré-condição para toda reserva; seu vínculo a estados ou efeitos de negócio não foi inferido. Transições não listadas são negadas até decisão de produto.

Manutenção e bloqueio também adquirem locks dos recursos e usam a política transacional de ADR-003; impacto sobre reservas já existentes continua conforme Q-08.

## Justificativa

O estado define disponibilidade e, portanto, a invariante central. A aprovação especial não é presumida como pré-condição geral. Não se cancela nem altera uma reserva existente automaticamente em razão de manutenção. O intervalo semiaberto `[início, fim)` segue ADR-003.

## Consequências positivas

- Preserva neutralidade enquanto os conceitos de negócio estão abertos.
- A abordagem proposta dá base a testes de transição depois de aprovada a tabela de estados.

## Consequências negativas

- Regras de negócio de aprovação especial, cancelamento por ator/prazo e impacto de manutenção permanecem incompletas.
- Não se presume que este catálogo resolva toda a semântica de Q-04/Q-05/Q-08/Q-14/Q-15.

## Riscos

- Estado implícito em código/flags torna impossível auditar ou raciocinar sobre ocupação.
- Aprovação ou manutenção pode causar mudança indevida da agenda se ligada a uma transição não ratificada.

## Estratégia de validação

Gerar testes parametrizados para cada transição implementada e não permitida; testar com PostgreSQL/Testcontainers que estados bloqueantes/não bloqueantes preservam a agenda, inclusive em transições concorrentes e após rollback. Confirmar que aprovação especial não é interpretada como condição global de reserva.
