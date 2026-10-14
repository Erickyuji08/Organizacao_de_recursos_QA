# Registros de decisões arquiteturais

Os ADRs abaixo registram decisões técnicas ratificadas para orientar o bootstrap posterior. Eles não aprovam regras de negócio que permanecem abertas no PRD e não afirmam que a implementação já exista.

## Status

- **Aceito:** decisão arquitetural aprovada para implementação futura; validações operacionais continuam obrigatórias antes de declarar a solução comprovada.
- **Aceito com ressalva:** decisão principal adotada, com dependência identificada que não autoriza extrapolar o escopo.

## Índice

| ADR | Título | Status |
|---|---|---|
| [ADR-001](ADR-001-monolito-modular-em-camadas.md) | Monólito modular em camadas, Java 21, Spring Boot 3.x, Maven Wrapper e REST | Aceito |
| [ADR-002](ADR-002-postgresql-como-persistencia.md) | PostgreSQL, Spring Data JPA/Hibernate, configuração externa | Aceito |
| [ADR-003](ADR-003-protecao-contra-dupla-reserva.md) | Locks pessimistas ordenados e revalidação transacional | Aceito; prova PostgreSQL obrigatória |
| [ADR-004](ADR-004-fronteiras-transacionais.md) | Fronteiras de transação na camada de aplicação | Aceito |
| [ADR-005](ADR-005-autenticacao-e-autorizacao.md) | Spring Security, JWT de acesso, papéis e menor privilégio | Aceito; sem matriz CRUD inventada |
| [ADR-006](ADR-006-ciclo-de-vida-e-estados.md) | Estados mínimos e ocupação da agenda | Aceito para proteção técnica |
| [ADR-007](ADR-007-auditoria-operacional.md) | Auditoria mínima e atômica de mutações/decisões | Aceito; retenção depende de política institucional |
| [ADR-008](ADR-008-notificacoes-e-integracoes.md) | Notificações e integrações externas fora do baseline | Aceito |
| [ADR-009](ADR-009-erros-http.md) | API REST e erros RFC 9457 | Aceito |
| [ADR-010](ADR-010-estrategia-de-testes.md) | JUnit 5, MockMvc, Testcontainers, JaCoCo e gates | Aceito |
| [ADR-011](ADR-011-codespaces-devcontainer.md) | Dev Container/Codespaces e dados locais efêmeros | Aceito; implementação futura |
| [ADR-012](ADR-012-migracoes-de-banco.md) | Flyway e migrações SQL versionadas | Aceito |

## Governança

Decisões que alterem escopo, regra de negócio, prioridade ou comportamento além das decisões técnicas ratificadas exigem aprovação de produto e atualização dos artefatos de requisitos por seus responsáveis. As decisões não substituem a implementação, a validação por testes nem as políticas institucionais pendentes de retenção e criticidade.
