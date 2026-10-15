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

## Executar localmente

Requisitos: JDK 21 e Docker em execução para os testes de integração. Não é necessário instalar Maven globalmente; o Maven Wrapper obtém a versão configurada no projeto.

Confirme que o Java ativo é 21 e execute os testes:

```sh
java -version
./mvnw -B verify
```

`verify` inicia um PostgreSQL efêmero via Testcontainers. Não há fallback para H2 ou outro banco.

### PostgreSQL local e aplicação

Em um terminal, inicie PostgreSQL para desenvolvimento. A senha abaixo é somente um placeholder fictício: substitua-a localmente e não a versione.

```sh
docker run --rm --name organizacao-postgres \
  -e POSTGRES_DB=organizacao_recursos \
  -e POSTGRES_USER=organizacao_local \
  -e POSTGRES_PASSWORD=replace-with-local-only \
  -p 5432:5432 postgres:16-alpine
```

Em outro terminal, configure a conexão usando variáveis de ambiente:

```sh
export SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/organizacao_recursos
export SPRING_DATASOURCE_USERNAME=organizacao_local
export SPRING_DATASOURCE_PASSWORD=replace-with-local-only
./mvnw spring-boot:run
```

O Flyway mantém seu histórico; esta etapa não cria tabelas de domínio. Hibernate valida o schema e não gera nem atualiza DDL.

O health check técnico está em `http://localhost:8080/actuator/health`. Para verificá-lo:

```sh
curl --fail http://localhost:8080/actuator/health
```

O endpoint deve retornar estado `UP` sem expor detalhes dos componentes. Esta base não implementa autenticação, endpoints de domínio ou regras de negócio.

## Configuração e ambiente futuro

Não adicione `.env`, senhas ou chaves ao Git. Em ambientes implantados, credenciais devem vir do mecanismo de secrets/configuração do ambiente. O comando `docker run` acima não usa volume: seus dados são efêmeros e desaparecem quando o container é removido. Uma configuração futura com volume no Codespace não será backup permanente.

Excluir o Codespace pode eliminar volumes, dados e configuração local; dados importantes exigem backup externo.

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
- PostgreSQL/Testcontainers foram implementados e validados localmente na Story 1.2; Dev Container e GitHub Actions continuam pendentes da Story 1.3.
- Não há interface visual definida no escopo atual.
