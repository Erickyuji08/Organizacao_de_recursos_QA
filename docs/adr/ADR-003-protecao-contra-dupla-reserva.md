# ADR-003 — Proteção contra dupla reserva

- **Status:** Pendente (Q-01/Q-02/Q-04/Q-08/Q-09 e prova técnica)
- **Data:** 2026-10-14

## Contexto

RF-09/RN-01 exigem ausência de conflitos em termos gerais. RF-14 explicita concorrência como derivação contextual, ainda a ratificar. A definição temporal, os recursos que conflitam, os estados ocupantes e o resultado de cada requisição permanecem abertos.

## Problema

Como garantir que escritores simultâneos não persistam estado que viole a definição de conflito aprovada?

## Requisitos relacionados

REQ-01; RF-09/RF-14; RN-01; Q-01/Q-02/Q-04/Q-08/Q-09.

## Alternativas

1. Apenas verificar disponibilidade na aplicação antes de gravar.
2. Constraint PostgreSQL de exclusão para recursos discretos, com validação de domínio.
3. Locks pessimistas por recurso/entidade-pai, incluindo locking ordenado.
4. Isolamento serializável, com tratamento de abortos/retries.
5. Controle otimista por versão.

## Decisão

Não selecionar ainda mecanismo único nem regra vencedora. Direção para prova técnica: validação de domínio mais defesa atômica no banco; considerar constraint de exclusão para ocupação exclusiva discreta e locks/serializável para capacidade quantitativa ou políticas mais complexas. Controle otimista sozinho não protege novas linhas conflitantes. O resultado de produto em Q-09 permanece fora da decisão técnica.

## Justificativa

A consulta de disponibilidade tem uma janela de corrida e não pode ser autoridade para confirmar gravação. O mecanismo deve proteger todo caminho de escrita, inclusive manutenção. Sem predicado, modelo de recurso e estados, selecionar uma constraint seria prematuro.

## Consequências positivas

- Explicita a necessidade de prova contra concorrência real antes do aceite.
- Evita falsa garantia por pré-consulta ou sincronização apenas na JVM.
- Mantém mecanismos diferentes abertos para sala/docente/material.

## Consequências negativas

- Bloqueia definição final de esquema e implementação de reserva.
- O tratamento de colisões, abortos, deadlocks e retry exige trabalho específico por mecanismo.

## Riscos

- A constraint cobre apenas subconjunto dos conflitos aprovados.
- Escritores futuros podem contornar a mesma proteção.
- Sem Q-09, resposta HTTP não equivale a política de negócio.

## Estratégia de validação

Criar teste de integração Testcontainers com conexões independentes sincronizadas para cada dimensão de conflito aprovada e para reserva-versus-manutenção. Verificar estado final, resultado de ambas as chamadas, rollback e repetição de corrida; medir contenção sem gate de desempenho até Q-16. Fechar ADR depois da aprovação das Qs e da prova.
