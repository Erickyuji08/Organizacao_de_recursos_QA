# ADR-007 — Auditoria operacional

- **Status:** Pendente (Q-11)
- **Data:** 2026-10-14

## Contexto

Q-11 pergunta se deve existir trilha operacional, quais eventos/conteúdos devem ser registrados e por quanto tempo. RTM e ADRs rastreiam documentação; isso não implica auditoria de eventos de produto. Logs técnicos também não são automaticamente trilha auditável.

## Problema

É necessário decidir se o sistema precisa registrar eventos de negócio e quais garantias de completude, acesso e retenção se aplicam.

## Requisitos relacionados

RNF-03 (rastreabilidade documental, distinta); RF-10/RF-12/RF-13; Q-07/Q-11.

## Alternativas

1. Sem trilha operacional de produto.
2. Auditoria para eventos selecionados.
3. Auditoria ampliada para mutações/decisões, com política de acesso e retenção.
4. Auditoria assíncrona best-effort ou atômica com a operação.

## Decisão

Pendente de Q-11. Não criar tabela, componente ou integração de auditoria de produto antes da decisão. Se a auditoria for obrigatória e parte da operação, recomenda-se persistir evento e fato de negócio na mesma transação PostgreSQL. Retenção e conteúdo permanecem não definidos.

## Justificativa

Não há requisito aprovado para trilha operacional. A atomicidade evita fato sem evento, mas aumenta acoplamento e pode falhar a operação se o evento falhar. A escolha depende do propósito e da garantia aprovados.

## Consequências positivas

- Se adotada atomicamente, mantém correspondência transacional entre mudança e trilha.
- Se não adotada, evita custo e coleta de dados sem finalidade declarada.

## Consequências negativas

- A alternativa obrigatória pode reduzir disponibilidade da escrita e aumentar armazenamento.
- A alternativa sem auditoria não fornece histórico operacional de negócio.

## Riscos

- Registrar dados excessivos/sensíveis ou reter indefinidamente.
- Confundir log mutável com trilha imutável ou controle de não repúdio.
- Confundir auditoria com a RTM e rastreabilidade dos requisitos.

## Estratégia de validação

Decidir eventos/campos, ator, acesso, retenção e falha aceitável em Q-11; revisar privacidade/segurança. Se adotada, injetar falha antes do commit e provar a garantia escolhida, além de testes de acesso e retenção aprovados.
