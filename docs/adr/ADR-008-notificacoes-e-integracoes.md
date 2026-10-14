# ADR-008 — Notificações e integrações externas

- **Status:** Aceito: fora do baseline
- **Data:** 2026-10-14

## Contexto

Não há integração externa ou notificação exigida no PRD. Q-13 registra notificações como possibilidade não decidida, sem evento, destinatário, canal ou nível de garantia.

## Problema

Como evitar acoplamento externo e falhas desnecessárias no fluxo transacional do baseline?

## Requisitos relacionados

Q-13; RF-02/RF-10/RF-12/RF-13 são fluxos potencialmente afetados, não requisitos de notificação.

## Alternativas

1. Não incluir notificações nem integrações externas no baseline.
2. Integrar apenas após requisito de produto e contrato aprovados.

## Decisão

Não incluir provedor, fila, outbox ou worker no baseline. Reabrir esta decisão somente após aprovação de evento, destinatário, canal, dados, garantia de entrega e comportamento em falha. Se aprovada, a integração não manterá transação de reserva aberta durante chamada remota. Relatórios permanecem sob Q-12 e não são tratados como integração neste ADR.

## Justificativa

Nenhum sistema externo é declarado e o requisito de notificações não está aprovado. A omissão é a opção mais simples e reversível; síncrono amplia latência/indisponibilidade e assíncrono exige idempotência e operação adicional.

## Consequências positivas

- Evita acoplamento e vazamento de dados externos sem justificativa.
- Mantém escrita transacional independente de indisponibilidade externa.

## Consequências negativas

- Nenhuma notificação externa será enviada no baseline.
- Eventual integração futura exigirá decisão específica de entrega e tratamento de falhas.

## Riscos

- Duplicação ou perda de notificações sem contrato de idempotência/retentativa.
- Integração externa síncrona pode impedir ação de negócio durante falha do provedor.
- Conteúdo/destinatário incorretos podem expor dados.

## Estratégia de validação

Confirmar que o baseline não contém dependências nem fluxos de notificação; se uma integração for aprovada no futuro, testar timeout, 5xx, duplicação e recuperação com WireMock, além da preservação da invariante transacional.
