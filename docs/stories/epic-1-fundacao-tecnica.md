# EPIC-1: Fundação Técnica e Qualidade Inicial

**Status:** 📋 Planning
**Owner:** @pm
**Stakeholders:** Solicitante do projeto, @sm, @po, @dev, @data-engineer, @devops, @architect, @qa
**Created:** 2026-10-14

---

## Objective

Estabelecer uma base de desenvolvimento reproduzível, compilável e verificável para o sistema Organização de Recursos, compatível com a arquitetura ratificada e pronta para receber futuras stories de produto. Este Epic entrega somente infraestrutura técnica; não implementa funcionalidades ou regras de negócio.

## Business Value

Reduzir divergência entre ambientes de desenvolvimento e CI e tornar verificáveis, desde o início, o build, a persistência PostgreSQL, as migrações e os testes. O Epic não entrega capacidade de produto ao usuário final.

---

## Scope

### In Scope

- Java 21 e uma versão estável, fixada e compatível da linha Spring Boot 3.x.
- Maven e Maven Wrapper; estrutura de monólito modular em camadas conforme ADR-001.
- PostgreSQL, Spring Data JPA/Hibernate e configuração externa conforme ADR-002.
- Flyway com migrações SQL versionadas; nenhuma migração ou tabela de domínio antes de aprovação do modelo.
- Testes mínimos com JUnit 5 e Testcontainers usando PostgreSQL real; H2 não substitui a validação PostgreSQL.
- Dev Container/Codespaces com suporte a Java 21, Maven Wrapper e Docker/Testcontainers, sem credenciais versionadas.
- GitHub Actions para executar `./mvnw -B verify` com Java 21.
- JaCoCo com gates mínimos de 80% de cobertura de linhas e 70% de branches, sem exclusões ad hoc.
- README com configuração, execução, testes e limites do ambiente local.
- Endpoint técnico de health check, sem endpoints de negócio.

### Out of Scope

- Autenticação funcional, emissão/validação funcional de JWT ou autorização de fluxos de negócio.
- Cadastro ou gerenciamento de usuários, recursos, salas, docentes ou materiais.
- Reservas, aprovações, alocação, regras de agenda ou proteção concorrente de reservas.
- Bloqueios de manutenção, estados de negócio ou auditoria de negócio.
- Relatórios, notificações, integrações externas ou interface de usuário.
- Entidades, repositórios, tabelas ou migrações de domínio presumidos.
- Implementação de locks sem operação de reserva real.
- Execução de deploy ou configuração de produção.

---

## Stories

As linhas abaixo são uma decomposição proposta para aprovação. Nenhum arquivo de story foi criado e os links indicam os caminhos canônicos esperados pelo workflow (`docs/stories/{epic}.{story}.story.md`).

| ID | Título | Pontos | Prioridade | Situação |
|----|--------|--------|------------|----------|
| [1.1](./1.1.story.md) | Projeto Java/Spring Boot e Maven Wrapper | A estimar por @sm | A definir | Proposta — não criada |
| [1.2](./1.2.story.md) | PostgreSQL, Flyway e Testcontainers | A estimar por @sm | A definir | Proposta — não criada |
| [1.3](./1.3.story.md) | Ambiente reproduzível, CI e qualidade inicial | A estimar por @sm | A definir | Proposta — não criada |

### Story 1.1 — Projeto Java/Spring Boot e Maven Wrapper

- **Objetivo:** iniciar a aplicação compilável em Java 21/Spring Boot 3.x, estabelecer a estrutura técnica em camadas e disponibilizar um endpoint de health check.
- **Dependência:** nenhuma; é a fundação para as demais stories.
- **Escopo:** Maven Wrapper, versão fixada do Spring Boot 3.x, inicialização da aplicação, organização de pacotes conforme ADR-001, endpoint técnico e teste mínimo de contexto/endpoint.
- **Critérios de aceite propostos:**
  1. O Maven Wrapper executa o build com Java 21 e a versão Spring Boot 3.x é fixada e compatível.
  2. A aplicação inicializa e mantém as fronteiras de API, aplicação, domínio e infraestrutura sem lógica de negócio.
  3. O health check técnico responde conforme contrato do Actuator, sem expor informação sensível.
  4. Testes automatizados verificam o contexto e a resposta técnica; não há entidades ou rotas de negócio.
- **Executor previsto:** @dev.
- **Quality gate especializado previsto:** @architect, com revisão arquitetural, de código e de padrões. O gate SDC posterior continua sendo responsabilidade de @qa.
- **Requisitos/decisões relacionados:** CT-01, CT-02, CT-03, RNF-01, RNF-04, RNF-05; ADR-001 e ADR-010.
- **Evidências esperadas:** versão Java/build efetivos, saída de teste do contexto e health check, e revisão da estrutura de dependências.
- **Definition of Done proposta:** critérios demonstrados por testes; `./mvnw -B verify` localmente aprovado; revisão especializada e CodeRabbit pré-commit registrados; File List da story completa; pronta para o gate QA do SDC.

### Story 1.2 — PostgreSQL, Flyway e Testcontainers

- **Objetivo:** configurar a persistência técnica aprovada e verificar a inicialização/migração com PostgreSQL real, sem presumir modelo de negócio.
- **Dependência:** Story 1.1, para estender o projeto Spring Boot e o build existentes.
- **Escopo:** PostgreSQL, Spring Data JPA/Hibernate, propriedades externas, Flyway, validação do esquema migrado e teste de integração com Testcontainers. Não criar migração de domínio.
- **Critérios de aceite propostos:**
  1. Host, porta, banco, usuário e senha da conexão são fornecidos por configuração externa; nenhum segredo real é versionado ou registrado.
  2. Flyway está habilitado para migrações SQL versionadas e Hibernate não cria/atualiza esquema automaticamente.
  3. Um teste de integração inicia PostgreSQL real via Testcontainers e verifica a inicialização da infraestrutura/migração sem H2 como substituto.
  4. Não são criadas entidades, tabelas ou migrações de domínio especulativas.
  5. Falta ou incompatibilidade de configuração não é mascarada por fallback silencioso para outro banco.
- **Executor previsto:** @data-engineer.
- **Quality gate especializado previsto:** @dev, com validação de esquema e revisão de migrações. O helper oficial também retorna `rls_test`; isso não autoriza criar políticas ou tabelas: tal teste é inaplicável enquanto o escopo não contiver RLS. O gate SDC posterior continua sendo responsabilidade de @qa.
- **Requisitos/decisões relacionados:** CT-03, RNF-01, RNF-04, RNF-05; ADR-002, ADR-003, ADR-004, ADR-010 e ADR-012.
- **Evidências esperadas:** teste executado contra PostgreSQL Testcontainers, resultado Flyway/Hibernate e verificação de ausência de credenciais versionadas. Não declarar locks ou concorrência de reservas validados.
- **Definition of Done proposta:** critérios demonstrados com PostgreSQL real; `./mvnw -B verify` aprovado; revisão de migrações e CodeRabbit pré-commit registrados; File List da story completa; pronta para o gate QA do SDC.

### Story 1.3 — Ambiente reproduzível, CI e qualidade inicial

- **Objetivo:** tornar o setup e os gates de qualidade reproduzíveis em Codespaces e GitHub Actions e documentar o procedimento.
- **Dependências:** Stories 1.1 e 1.2, para que o container e a CI validem o build e os testes PostgreSQL existentes.
- **Escopo:** Dev Container/Codespaces, suporte a Docker/Testcontainers, workflow GitHub Actions, JaCoCo, gates de cobertura e instruções no README. Não configurar deploy de produção.
- **Critérios de aceite propostos:**
  1. O Dev Container disponibiliza Java 21, Maven Wrapper e suporte a Docker/Testcontainers sem credenciais pessoais versionadas.
  2. O workflow usa Java 21 e executa `./mvnw -B verify`; testes PostgreSQL não são substituídos silenciosamente por H2.
  3. JaCoCo mede o código de produção incluído no build e falha abaixo de 80% de linhas ou 70% de branches, sem exclusões ad hoc.
  4. README documenta pré-requisitos, configuração externa, execução da aplicação, testes e limitações do ambiente local/Codespace.
  5. O workflow é revisado, mas o Epic não registra CI remoto como aprovado sem execução real no GitHub.
- **Executor previsto:** @devops para CI e ambiente; os limites de propriedade de CI/CD permanecem com @devops.
- **Quality gate especializado previsto:** @architect, com revisão de infraestrutura, varredura de segurança e validação de configuração. O gate SDC posterior continua sendo responsabilidade de @qa.
- **Requisitos/decisões relacionados:** RNF-01, RNF-03, RNF-05, CT-01, CT-02, CT-03 e Q-16; ADR-001, ADR-010 e ADR-011.
- **Evidências esperadas:** validação local de `./mvnw -B verify`, relatório JaCoCo, configuração revisada do Dev Container/workflow e instruções conferidas. Resultado remoto do GitHub somente após execução.
- **Definition of Done proposta:** critérios demonstrados; workflow e README descrevem o mesmo procedimento; nenhum segredo foi adicionado; CodeRabbit pré-commit e revisão especializada registrados; File List da story completa; pronta para o gate QA do SDC.

**Quality gates comuns planejados:** CodeRabbit pré-commit para cada story conforme integração configurada no AIOX; depois da implementação, @qa executa o QA Gate do SDC. Nenhum desses gates foi executado nesta etapa de planejamento.

---

## Success Criteria

- [ ] O build e os testes do projeto são reproduzíveis com Java 21 e Maven Wrapper.
- [ ] A aplicação Spring Boot 3.x inicializa e expõe somente o health check técnico deste Epic.
- [ ] A configuração de persistência usa PostgreSQL e variáveis externas; um teste utiliza PostgreSQL via Testcontainers.
- [ ] Flyway está configurado sem introduzir esquema de negócio não aprovado.
- [ ] Dev Container, README e CI descrevem procedimentos compatíveis entre si.
- [ ] JaCoCo aplica os limites ratificados de 80% de linhas e 70% de branches, sem exclusões ad hoc.
- [ ] A CI configurada executa `./mvnw -B verify` com Java 21; evidência de execução remota fica pendente até o workflow ser executado no GitHub.
- [ ] Nenhuma funcionalidade de negócio, credencial real ou segredo foi incluído.

---

## Technical Requirements

- Seguir ADR-001: Java 21, Maven Wrapper, Spring Boot 3.x e camadas de API, aplicação, domínio e infraestrutura, sem abstrações ou módulos sem consumidor justificado.
- Seguir ADR-002: PostgreSQL, Spring Data JPA/Hibernate, configuração externa e validação do esquema; nenhum banco em memória substitui PostgreSQL.
- Seguir ADR-012: Flyway com SQL versionado; nenhuma migração de domínio antes do modelo aprovado.
- Seguir ADR-010: JUnit 5, Testcontainers/PostgreSQL real, JaCoCo 80%/70% e CI `./mvnw -B verify` com Java 21.
- Seguir ADR-011: Dev Container mínimo e suporte a Docker/Testcontainers; segredos somente por configuração externa/secret store.
- Preservar o escopo de ADR-003/004/005 sem antecipar operações de reserva, implementação de concorrência, transações de negócio ou autenticação funcional.
- Não modificar requisitos, ADRs ou decisões de arquitetura aprovados como parte das stories deste Epic.

---

## Risks

| Risk | Impact | Mitigation |
|------|--------|------------|
| PRD mantém questões funcionais em aberto; o bootstrap pode avançar indevidamente sobre elas. | High | Manter as exclusões explícitas; exigir nova story validada para qualquer funcionalidade e decisão necessária. |
| Cobertura de 80%/70% pode incentivar testes artificiais ou exclusões em um scaffold pequeno. | Medium | Testar comportamento técnico real, manter o gate íntegro e reportar bloqueio de cobertura em vez de mascará-lo. |
| Docker/Testcontainers ou imagens podem não estar disponíveis em algum ambiente. | Medium | Validar o ambiente configurado; falhar explicitamente, sem trocar por H2 ou fallback silencioso. |
| Segredos podem ser adicionados a configurações, exemplos ou logs. | High | Usar placeholders identificados e configuração externa; revisar arquivos e logs sem registrar valores secretos. |
| A task disponível `brownfield-create-epic` pressupõe enhancement e padrões de código existentes. | Medium | Tratar a iniciativa como bootstrap greenfield; usar o template/schema genérico do Epic e somente os itens aplicáveis dos checklists, sem afirmar compatibilidade com código inexistente. |

---

## Dependencies

**Depends on:**

- Arquitetura ratificada em [docs/arquitetura.md](../arquitetura.md) e decisões aceitas em [docs/adr/](../adr/).
- Aprovação humana deste Epic antes de @sm criar as stories.

**Story sequence (sem ciclos):**

- Story 1.1 não depende de outra story.
- Story 1.2 depende de 1.1.
- Story 1.3 depende de 1.1 e 1.2.

---

## Documentation

| Type | Location | Status |
|------|----------|--------|
| PRD e rastreabilidade | [docs/prd.md](../prd.md), [docs/rtm.md](../rtm.md) | Existente; criticidade e evidência operacional ainda pendentes conforme RTM |
| Arquitetura e checklist ratificados | [docs/arquitetura.md](../arquitetura.md), [docs/arquitetura-checklist.md](../arquitetura-checklist.md) | Existente |
| ADRs | [docs/adr/](../adr/) | Existentes e aceitos; sem alteração proposta |
| Instruções do bootstrap | README a atualizar pela Story 1.3 | Pendente |
| Evidências de execução | Saídas locais, relatório JaCoCo e execução remota do workflow | Pendente; não produzidas no planejamento |

---

## Rastreabilidade

### Requisitos do PRD/RTM

- **CTX-01 / CT-01, CT-02, CT-03:** Java 21, Spring Boot 3.x e Maven/Maven Wrapper.
- **CTX-02 / RNF-01:** testes automatizados com JUnit 5 e Testcontainers/PostgreSQL real.
- **RNF-03:** registrar evidências por identificadores; a classificação de criticidade permanece pendente e não será inventada neste Epic.
- **RNF-04 / POL-01:** preservar separação de responsabilidades e segurança; autenticação funcional permanece fora do escopo.
- **RNF-05 / Q-16:** GitHub Actions, `./mvnw -B verify`, Java 21 e cobertura JaCoCo de 80% linhas/70% branches.

Requisitos REQ/RF de produto não são critérios de implementação deste Epic; suas regras e questões abertas continuam pendentes para stories de produto aprovadas.

### ADRs relacionados

- [ADR-001](../adr/ADR-001-monolito-modular-em-camadas.md): plataforma e camadas.
- [ADR-002](../adr/ADR-002-postgresql-como-persistencia.md): PostgreSQL, JPA/Hibernate e configuração externa.
- [ADR-003](../adr/ADR-003-protecao-contra-dupla-reserva.md): estratégia futura; este Epic não implementa nem declara validada a proteção contra dupla reserva.
- [ADR-004](../adr/ADR-004-fronteiras-transacionais.md): fronteiras transacionais de operações futuras; sem operações de negócio neste Epic.
- [ADR-005](../adr/ADR-005-autenticacao-e-autorizacao.md): autenticação funcional depende de decisões pendentes e está excluída.
- [ADR-010](../adr/ADR-010-estrategia-de-testes.md): testes, Testcontainers, cobertura e CI.
- [ADR-011](../adr/ADR-011-codespaces-devcontainer.md): ambiente Dev Container/Codespaces.
- [ADR-012](../adr/ADR-012-migracoes-de-banco.md): Flyway e proibição de migração de domínio antes do modelo aprovado.

---

## Epic Review and Validation

**PM review outcome:** Escopo aprovado pelo stakeholder nesta conversa para encaminhamento a @sm; stories ainda não criadas.
**Epic lifecycle status:** Planning.
**Story lifecycle status:** nenhuma story criada ou validada.

| Check | Result | Notes |
|-------|--------|-------|
| PM checklist 6.1 — objetivo, coesão, tamanho e dependências | Pass | Um Epic técnico, três stories propostas e sequência explícita. |
| PM checklist 6.2 — decomposição e critérios verificáveis | Pass | Cada proposta inclui objetivo, escopo, dependência, critérios, executor, revisão, evidências e DoD; @sm ainda deve produzir as stories canônicas. |
| PM checklist 6.3 — completude do primeiro Epic | Pass | Inicialização, persistência, ambiente, CI, cobertura e documentação estão cobertos sem escopo de produto. |
| PO checklist 6.1 — scaffolding greenfield | Pass | Inicialização do projeto e build foram incluídos em 1.1. |
| Dependências e ciclos | Pass | Grafo acíclico: 1.1 → 1.2 → 1.3. Stories posteriores são verificáveis após suas dependências; não são independentes em ordem de implementação. |
| Rastreabilidade PRD/RTM/ADRs | Pass | CT/RNF/Q-16 e ADRs pertinentes estão ligados; criticidade e evidências de execução continuam explicitamente pendentes. |
| Atribuições AIOX | Pass | `assignExecutorFromContent`/`validateExecutorAssignment` retornaram atribuições válidas para as três propostas; executor e gate especializado são distintos. @qa permanece responsável pelo QA Gate SDC posterior. |
| Task `brownfield-create-epic` | Not applicable in full | A task é para pequenos enhancements brownfield; este repositório não tem código de produção e a arquitetura já foi ratificada. O template/schema oficial de Epic e itens aplicáveis dos checklists foram usados. O processo completo de PRD/Arquitetura não foi refeito. |
| Aprovação | Approved by stakeholder | Aprovação concedida nesta conversa para encaminhar o Epic a @sm. O lifecycle status permanece `Planning`; nenhuma story foi criada. |

**Quality gate note:** atribuições são previsões de planejamento; nenhum gate, CodeRabbit, teste, CI ou validação PostgreSQL foi executado nesta revisão documental.

**Next workflow action:** encaminhar a @sm para `*draft` das três stories. Cada story deverá passar por validação @po antes de desenvolvimento por seus executores e pelo QA Gate @qa do SDC.

---

## Progress

- **Total Points:** A estimar após criação e refinamento das stories.
- **Completed Points:** 0
- **Progress:** 0%

---

## Change Log

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2026-10-14 | 0.1 | Epic técnico proposto para revisão e aprovação humana | @pm |

---

**Template:** `.aiox-core/product/templates/epic.hbs` — v1.0
**Template schema:** `.aiox-core/product/templates/engine/schemas/epic.schema.json`
