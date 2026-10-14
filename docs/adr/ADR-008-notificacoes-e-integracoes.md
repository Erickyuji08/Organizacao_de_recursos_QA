# ADR-008 — Notificações e integrações externas

- **Status:** Pendente (Q-13)
- **Data:** 2026-10-14

## Contexto

Não há integração externa aprovada no PRD ou nos diagramas. Notificações são uma pergunta aberta. Canal, eventos, destinatários, criticidade, privacidade e necessidade de entrega não foram definidos.

## Problema

Como evitar que uma integração não requerida comprometa consistência ou disponibilidade do fluxo de reservas?

## Requisitos relacionados

Q-13; RF-02/RF-10/RF-12/RF-13 são fluxos potencialmente afetados, não requisitos de notificação.

## Alternativas

1. Não implementar notificações/integrações externas.
2. Chamada síncrona para um provedor, se uma dependência imediata for aprovada.
3. Entrega assíncrona por outbox/worker com política de retries, se atraso for aceitável.

## Decisão

Pendente de Q-13. Não criar provedor, fila, outbox ou worker no baseline. Se integração for aprovada, escolher o padrão após definir evento, garantias, destinatário e falha aceitável; não manter transação de reserva aberta durante chamada remota. Relatórios dependem de Q-12 e não são tratados como integração neste ADR.

## Justificativa

Nenhum sistema externo é declarado. Síncrono reduz componentes mas pode ampliar latência/indisponibilidade; assíncrono exige consistência eventual, idempotência e operação adicional.

## Consequências positivas

- Evita acoplamento e vazamento de dados externos sem justificativa.
- Mantém escrita transacional independente de indisponibilidade externa.

## Consequências negativas

- Se notificações forem necessárias, usuário não recebe informação fora do sistema até decisão/implementação.
- Padrão assíncrono requer persistência de entrega e operação de fila/worker.

## Riscos

- Duplicação ou perda de notificações sem contrato de idempotência/retentativa.
- Integração externa síncrona pode impedir ação de negócio durante falha do provedor.
- Conteúdo/destinatário incorretos podem expor dados.

## Estratégia de validação

Decidir Q-13; elaborar contrato de evento/canal e análise de dados. Se aprovada integração HTTP, simular timeout, 5xx, duplicação e recuperação com WireMock; testar que uma falha não viola a invariante transacional. Usar outbox apenas se o requisito de entrega justificar.
