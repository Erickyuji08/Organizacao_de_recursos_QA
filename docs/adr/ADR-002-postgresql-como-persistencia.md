# ADR-002 — PostgreSQL como persistência relacional

- **Status:** Proposto (não aprovado)
- **Data:** 2026-10-14

## Contexto

A integridade de reservas concorrentes é o driver central. O projeto não especifica banco de dados. O desenho ATAM considera PostgreSQL candidato pela disponibilidade de transações relacionais, constraints e suporte a intervalos, sem prova de que uma constraint isolada represente todas as regras.

## Problema

Qual persistência permite proteger dados relacionais e concorrência de reservas sem presumir que materiais, docentes, salas e manutenção tenham a mesma semântica?

## Requisitos relacionados

REQ-01; RF-04/RF-06/RF-07/RF-08/RF-09/RF-14; RN-01; Q-01/Q-02/Q-04/Q-08/Q-16.

## Alternativas

1. PostgreSQL relacional.
2. Outro RDBMS, com estratégia de integridade equivalente a validar.
3. Banco não relacional.
4. Banco em memória como persistência da aplicação.

## Decisão

Recomendar PostgreSQL para persistência transacional do sistema. Não se seleciona versão, extensão, esquema ou constraint específica neste ADR. Banco em memória é aceitável apenas como apoio unitário, nunca como evidência de integridade concorrente.

## Justificativa

O modelo provável contém entidades e relações e exige consistência entre reserva e recursos. PostgreSQL oferece constraints e transações que podem atuar como defesa final, sujeitas à semântica aprovada e ao experimento. Nenhum requisito prescreve fornecedor; a escolha permanece proposta técnica.

## Consequências positivas

- Transações relacionais locais e integridade referencial.
- Possibilidade de provar constraints e isolamento com banco real em Testcontainers.
- Suporte potencial a restrições de intervalo para recursos de capacidade unitária.

## Consequências negativas

- Acoplamento técnico a PostgreSQL e sua operação.
- Extensões/constraints podem exigir permissões e configuração.
- Material por quantidade não é protegido automaticamente por uma constraint simples de exclusão.

## Riscos

- Um modelo de ocupação incompleto pode proteger sala, mas não docente/material/manutenção.
- Escolher banco sem testar versões, migrações e recursos suportados pode bloquear implantação.
- Falha do banco é ponto de indisponibilidade sem RNF operacional definido.

## Estratégia de validação

Após Q-01/Q-02/Q-04/Q-08, prototipar constraints/locks relevantes na versão PostgreSQL escolhida; testar migrations, rollback, duas conexões concorrentes e reserva-versus-manutenção com Testcontainers. Ratificar somente após o resultado e confirmação de operação.
