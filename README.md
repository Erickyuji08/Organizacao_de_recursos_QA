# Organização de Recursos

Sistema acadêmico planejado para organizar a solicitação e gestão de salas, professores e materiais, preservando reservas sem conflitos de horário. O PRD ainda contém decisões de negócio em aberto; nenhuma funcionalidade da aplicação foi implementada.

## Estado do projeto

As decisões arquiteturais estão documentadas em [ADRs](docs/adr/README.md) e em [arquitetura](docs/arquitetura.md). Esta revisão ratificou a arquitetura, mas não criou aplicação, banco, Dev Container, pipeline ou testes. Consulte o [PRD](docs/prd.md), a [RTM](docs/rtm.md) e o [checklist arquitetural](docs/arquitetura-checklist.md) para escopo e pendências antes de implementar funcionalidades.

## Stack definida

- Java 21 LTS.
- Spring Boot 3.x (versão estável compatível a fixar no bootstrap).
- Maven Wrapper.
- API REST com Spring Security/JWT.
- PostgreSQL com Spring Data JPA/Hibernate e Flyway.
- JUnit 5, Spring Boot Test/MockMvc, Testcontainers PostgreSQL e JaCoCo.
- GitHub Actions com `./mvnw -B verify`; gates JaCoCo de 80% linhas e 70% branches.
- GitHub Codespaces com Dev Container, a configurar em etapa posterior.

## Requisitos futuros de ambiente

Na etapa de bootstrap, serão necessários JDK 21, Docker funcional para Testcontainers e acesso a PostgreSQL para desenvolvimento. Configurações Spring usarão propriedades externas e variáveis de ambiente. Credenciais e chaves JWT devem vir de secrets do ambiente; nunca adicionar `.env`, senhas ou chaves ao Git.

O Maven Wrapper, `pom.xml`, configuração `.devcontainer/`, Compose, workflow GitHub Actions e classes da aplicação ainda não existem. Portanto, não há atualmente comando de instalação/inicialização, conexão de banco ou suite de testes que possa ser executada. Quando o bootstrap for autorizado, o fluxo planejado será `./mvnw -B verify`.

Se um PostgreSQL local for provisionado por Compose futuramente, um volume poderá persistir durante reconstruções normais do Codespace, mas não é backup permanente. Excluir o Codespace pode eliminar volume, dados e configuração local; dados importantes exigem backup externo.

## Documentos

- [PRD](docs/prd.md)
- [RTM](docs/rtm.md)
- [Arquitetura](docs/arquitetura.md)
- [Checklist arquitetural](docs/arquitetura-checklist.md)
- [Registros de decisões arquiteturais](docs/adr/README.md)

## Limitações e pendências conhecidas

- Critérios de conflito por tipo de recurso, CRUD, aprovação especial, políticas de manutenção e outros fluxos ainda dependem de decisões de produto.
- Retenção e acesso da auditoria precisam de política institucional antes do uso de dados reais.
- A proteção concorrente está desenhada em ADR-003, mas precisa ser comprovada com testes PostgreSQL/Testcontainers antes de liberar reservas.
- A configuração do ambiente e da CI ainda não existe e não foi validada.
- Não há interface visual definida no escopo atual.