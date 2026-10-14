# C4 — Contêineres

**Status:** arquitetura lógica ratificada, não inventário de software implantado. Monólito modular e PostgreSQL foram escolhidos em [ADR-001](../adr/ADR-001-monolito-modular-em-camadas.md), [ADR-002](../adr/ADR-002-postgresql-como-persistencia.md) e [ADR-004](../adr/ADR-004-fronteiras-transacionais.md); nenhum está configurado ainda.

```mermaid
C4Container
    title Organização de Recursos — Contêineres planejados
    Person(solicitante, "Solicitante", "Professor ou coordenador")
    Person(responsavel, "Responsável", "Aprova, valida e acompanha")
    Person(administrador, "Administrador", "Gerencia recursos e períodos")
    System_Boundary(org, "Organização de Recursos") {
        Container(api, "API / aplicação", "Java 21, Spring Boot 3.x, Maven Wrapper", "API REST, casos de uso, domínio, segurança e adaptadores; monólito modular")
        ContainerDb(db, "Banco relacional", "PostgreSQL", "Persistência transacional via Spring Data JPA/Hibernate; esquema evoluído por Flyway SQL")
    }
    Rel(solicitante, api, "Consulta e solicita operações REST")
    Rel(responsavel, api, "Registra aprovações, validações e acompanhamento")
    Rel(administrador, api, "Gerencia cadastros, bloqueios e manutenção")
    Rel(api, db, "Transações locais via portas/adaptadores")
```

## Notas de fronteira

- O contêiner de aplicação único mantém as gravações de reserva e alocações na mesma transação. Não se propõe um serviço separado para reservas, auditoria ou notificações.
- A API REST foi decidida para o backend, mas não há endpoints implementados nem requisito de interface visual.
- PostgreSQL é o banco adotado. Locks ordenados e revalidação são a proteção principal contra concorrência; constraint de exclusão é apenas defesa adicional se o esquema permitir.
- Spring Security/JWT stateless é a decisão de segurança; chaves são externas e não há provedor externo de identidade no baseline.
- A auditoria mínima está definida em ADR-007 e participa da transação; a política de retenção/leitura aguarda decisão institucional. Relatórios/notificações permanecem fora do baseline.
- Ver [componentes](componentes.md) para os limites internos e [arquitetura](../arquitetura.md) para trade-offs.