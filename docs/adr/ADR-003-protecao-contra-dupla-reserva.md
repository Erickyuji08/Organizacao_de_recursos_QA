# ADR-003 — Proteção contra dupla reserva

- **Status:** Aceito (prova operacional obrigatória antes de liberar reservas)
- **Data:** 2026-10-14

## Contexto

RF-09/RN-01 exigem ausência de conflitos e a estratégia concorrente foi determinada para a implementação. O intervalo é semiaberto `[início, fim)`: início inclusivo, fim exclusivo; intervalos adjacentes não se sobrepõem. A identidade do recurso e a forma de contabilizar demanda por quantidade dependem do modelo de domínio, mas nenhuma verificação apenas na aplicação pode garantir exclusão concorrente.

## Problema

Como garantir que operações simultâneas de reserva, aprovação e manutenção não confirmem ocupações incompatíveis, inclusive quando envolvem vários recursos?

## Requisitos relacionados

REQ-01; RF-09/RF-14; RN-01; Q-01/Q-02/Q-04/Q-08/Q-09.

## Alternativas

1. Apenas verificar disponibilidade na aplicação antes de gravar.
2. Locks pessimistas dos registros de recurso e revalidação dentro da transação.
3. Constraint PostgreSQL de exclusão como defesa adicional quando o modelo a suportar.
4. Isolamento serializável ou controle otimista como mecanismo principal.

## Decisão

Para cada operação que crie, aprove, altere ou retire uma ocupação, abrir uma transação PostgreSQL e bloquear pessimisticamente os registros dos recursos envolvidos (`SELECT ... FOR UPDATE`). Adquirir todos os locks em ordem determinística, primeiro pelo tipo canônico do recurso e depois por identificador estável; não bloquear recursos um a um na ordem recebida da API. Depois de adquirir os locks, reconsultar as ocupações conflitantes na mesma transação e só então gravar a reserva e seus vínculos. Qualquer conflito ou falha reverte a transação.

Para reservas com vários recursos, adquirir o conjunto completo de locks ordenado antes da validação e da escrita. Toda operação que possa alterar disponibilidade, inclusive aprovação e manutenção, segue a mesma disciplina. Uma mudança de estado para `PENDENTE` ou `APROVADA` é bloqueante e exige revalidação; `REJEITADA` e `CANCELADA` deixam de bloquear. Uma transição que libera ocupação também mantém locks até o commit, para serializar concorrentes. A aprovação nunca ignora a verificação: em disputas entre solicitações incompatíveis, apenas a transação que ainda encontra disponibilidade pode confirmar, e as demais recebem conflito.

Usar restrição PostgreSQL `EXCLUDE USING gist` apenas se o esquema aprovado representar cada ocupação por recurso/intervalo e a restrição puder cobrir exatamente os estados bloqueantes. Até essa prova, ela é defesa adicional candidata, não garantia assumida. Para materiais por quantidade ou casos não representáveis por exclusão, os locks no registro pai do recurso e a revalidação agregada são a proteção principal. Conflitos detectados pela validação ou constraint são erro de domínio/API HTTP 409; erros técnicos não são convertidos em conflito. Ver ADR-006 para estados, ADR-004 para atomicidade e ADR-009 para contrato de erro.

## Justificativa

A consulta de disponibilidade tem uma janela de corrida e não pode ser autoridade para confirmar gravação. Locks sobre registros estáveis de recurso serializam também a ausência de reservas; a revalidação dentro do lock fecha a janela de corrida. O ordenamento global evita ciclos de espera entre operações multi-recurso.

## Consequências positivas

- Explicita a necessidade de prova contra concorrência real antes do aceite.
- Evita falsa garantia por pré-consulta ou sincronização apenas na JVM.
- Mantém mecanismos diferentes abertos para sala/docente/material.

## Consequências negativas

- Locks reduzem concorrência para operações sobre o mesmo recurso e podem aumentar latência.
- Toda escrita que altera ocupação deve seguir o protocolo; acesso direto que o ignore pode reintroduzir corridas.

## Riscos

- Timeout/deadlock ainda pode ocorrer por código externo ao protocolo; falhas abortam integralmente, e deadlocks identificados devem ser observáveis, não mascarados.
- Constraints por intervalos e material quantitativo requerem modelos diferentes.
- Os critérios detalhados por tipo de recurso e manutenção continuam sujeitos às regras de produto ainda abertas.

## Estratégia de validação

Criar testes de integração Testcontainers com PostgreSQL e conexões independentes sincronizadas para reserva-reserva, aprovação-aprovação, aprovação-reserva e reserva-manutenção. Cobrir recursos múltiplos enviados em ordens diferentes e estados rejeitado/cancelado. Verificar resultado 409 para o perdedor, ausência de gravação parcial, rollback e invariantes no estado final. Repetir cenários para encontrar deadlocks; não declarar a proteção operacionalmente validada até esses testes passarem no ambiente de CI.
