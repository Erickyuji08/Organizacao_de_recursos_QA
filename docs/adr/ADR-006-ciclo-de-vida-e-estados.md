# ADR-006 — Ciclo de vida e estados

- **Status:** Pendente (Q-04/Q-05/Q-08/Q-14/Q-15)
- **Data:** 2026-10-14

## Contexto

As fontes declaram gerenciamento de reservas, aprovação especial, validação docente e manutenção, sem definir estados, transições, cancelamento, quais registros ocupam agenda ou efeitos de aprovação/manutenção.

## Problema

Como representar ciclo de vida sem introduzir estados ou efeitos que não foram aprovados?

## Requisitos relacionados

REQ-01/REQ-02/REQ-03/REQ-04; RF-02/RF-07/RF-08/RF-09/RF-10/RF-11; Q-04/Q-05/Q-08/Q-14/Q-15.

## Alternativas

1. Estado único de reserva com decisão de aprovação registrada separadamente.
2. Ciclo explícito com estados de solicitação, decisão e encerramento.
3. Outra máquina de estados aprovada pelo negócio.

## Decisão

Pendente de definição e aprovação do produto. Nenhum rótulo, transição, condição de ocupação ou regra de cancelamento é selecionado. Quando aprovado, modelar transições explicitamente no domínio; rejeitar transições inválidas e persistir o resultado atomicamente.

## Justificativa

O estado define disponibilidade e, portanto, a invariante central. Aprovação não pode ser presumida pré-condição de reserva; manutenção não pode cancelar ou alterar reservas existentes por inferência.

## Consequências positivas

- Preserva neutralidade enquanto os conceitos de negócio estão abertos.
- A abordagem proposta dá base a testes de transição depois de aprovada a tabela de estados.

## Consequências negativas

- RF-02/RF-07/RF-08/RF-10 não ficam prontos para critérios finais ou implementação.
- Modelo de banco e constraint de agenda não podem ser fechados.

## Riscos

- Estado implícito em código/flags torna impossível auditar ou raciocinar sobre ocupação.
- Aprovação ou manutenção pode causar mudança indevida da agenda se ligada a uma transição não ratificada.

## Estratégia de validação

Obter tabela aprovada de estados, ator permitido, pré-condição, efeito na ocupação, terminalidade e concorrência. Gerar testes parametrizados para todas as transições permitidas/proibidas e testar falha/concorrência no banco.
