# Organização de Recursos

Sistema acadêmico planejado para organizar a solicitação e gestão de salas, professores e materiais, preservando reservas sem conflitos de horário. O PRD ainda contém decisões de negócio em aberto; nenhuma funcionalidade da aplicação foi implementada.

## Estado do projeto

As decisões arquiteturais estão documentadas em [ADRs](docs/adr/README.md) e em [arquitetura](docs/arquitetura.md). A fundação técnica está sendo entregue sequencialmente pelo EPIC-1; a aplicação da Story 1.1 não implementa funcionalidades de negócio. Consulte o [PRD](docs/prd.md), a [RTM](docs/rtm.md) e o [checklist arquitetural](docs/arquitetura-checklist.md) para escopo e pendências antes de implementar funcionalidades.

## Stack definida

- Java 21 LTS.
- Spring Boot 3.5.16.
- Maven Wrapper.
- API REST; autenticação com Spring Security/JWT fica pendente das decisões de identidade e não está implementada nesta etapa.
- PostgreSQL com Spring Data JPA/Hibernate e Flyway.
- JUnit 5, Spring Boot Test/MockMvc, Testcontainers PostgreSQL e JaCoCo.
- GitHub Actions com `./mvnw -B verify`; gates JaCoCo de 80% linhas e 70% branches.
- GitHub Codespaces com Dev Container, a configurar na Story 1.3.

## Executar localmente — Story 1.1

Requisitos atuais: JDK 21. Não é necessário instalar Maven globalmente; o Maven Wrapper obtém a versão configurada no projeto. Docker e PostgreSQL serão necessários para a Story 1.2, ainda não fazem parte desta story.

Confirme que o Java ativo é 21 e execute os testes:

```sh
java -version
./mvnw -B verify
```

Para iniciar a aplicação:

```sh
./mvnw spring-boot:run
```

O health check técnico está em `http://localhost:8080/actuator/health`. Para verificá-lo:

```sh
curl --fail http://localhost:8080/actuator/health
```

O endpoint deve retornar estado `UP` sem expor detalhes dos componentes. Esta base não implementa autenticação, endpoints de domínio ou regras de negócio.

## Configuração e ambiente futuro

As configurações do Spring serão externalizadas por variáveis de ambiente quando a persistência for adicionada na Story 1.2. Credenciais e chaves devem vir de secrets do ambiente; nunca adicione `.env`, senhas ou chaves ao Git.

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
- PostgreSQL/Testcontainers, Dev Container e GitHub Actions são entregas das Stories 1.2 e 1.3 e ainda não foram validados.
- Não há interface visual definida no escopo atual.
