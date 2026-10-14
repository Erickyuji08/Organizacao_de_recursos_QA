# Arquitetura — Organização de Recursos

**Versão:** 1.0, 2026-10-14
**Status:** decisões técnicas ratificadas para orientar o bootstrap futuro; PRD permanece rascunho para regras de negócio.
**Escopo desta revisão:** arquitetura e decisões documentais. Nenhuma aplicação, infraestrutura, migration, teste ou CI foi criada ou executada.

## 1. Fontes e limites

O [PRD](prd.md) e a [RTM](rtm.md) preservam requisitos de produto e identificam perguntas ainda abertas. Os ADRs 001–012 ratificam decisões técnicas, sem transformar propostas de produto em requisitos. Os diagramas C4 e de sequência estão alinhados a esta arquitetura e não representam implementação existente. A lista de decisões e seus impactos está na seção [Rastreabilidade das decisões](#9-rastreabilidade-das-decisoes).

Esta documentação não declara que o repositório já contém código, banco, Dev Container, workflow, testes ou API executável. Não foram criadas classes, schema ou migrações.

## 2. Drivers e atributos arquiteturais

| Driver | Origem | Decisão |
|---|---|---|
| Reservas sem conflitos | REQ-01, RF-09/RF-14, RN-01 | Transação PostgreSQL; lock pessimista dos recursos, aquisição determinística e revalidação após lock. |
| Propriedade de reservas | REQ-02, RF-02 | JWT e verificação de propriedade no servidor; solicitante não acessa reserva alheia. |
| Aprovação, alocação e materiais | REQ-03, RF-10..RF-13 | Casos de uso separados, autorização por papel e auditoria mínima e atômica das ações implementadas. |
| Administração de recursos | REQ-04, RF-03..RF-08 | Módulos por domínio; nenhuma operação CRUD ou política não declarada é presumida. |
| Segurança, testabilidade e rastreabilidade | POL-01/POL-02, RNF-01..RNF-05 | Camadas explícitas, JWT, testes PostgreSQL, JaCoCo, RTM e CI. |
| Plataforma | CT-01..CT-03 | Java 21, Spring Boot 3.x, Maven Wrapper, PostgreSQL, Spring Data JPA/Hibernate e Flyway. |

Correção da agenda é o principal risco técnico. Testabilidade, segurança, clareza e rastreabilidade são atributos exigidos; as métricas operacionais de disponibilidade, desempenho, recuperação e escala não foram fornecidas e não são presumidas.

## 3. Estilo, módulos e dependências

Adotar monólito modular, organizado prioritariamente por domínio funcional, com camadas de apresentação/API, aplicação, domínio e infraestrutura. A direção permitida é API → aplicação → domínio/portas; infraestrutura implementa portas consumidas pela aplicação. O domínio não depende de Spring, HTTP, JPA ou serviços externos. Transações são delimitadas na camada de aplicação/serviço.

Módulos de domínio previstos: `reservas`, `recursos`, `aprovacoes`, `materiais` e `manutencao`; segurança e auditoria são preocupações transversais. Cada módulo expõe somente contratos necessários. Módulos não acessam repositórios, tabelas ou entidades JPA internas de outros módulos; dependências formam grafo acíclico. Não criar interfaces genéricas sem justificativa concreta. Não introduzir microserviços, CQRS, event sourcing, mensageria distribuída, cache ou padrões adicionais sem requisito.

Controllers adaptam HTTP e validam formato, mas não implementam regras de negócio. DTOs são usados na entrada/saída da API; entidades de persistência não são expostas. Regras ficam no domínio/casos de uso, não em controllers nem adaptadores de persistência.

## 4. Plataforma, persistência e ambientes

- **Runtime/build:** Java 21 LTS, Maven Wrapper versionado e versão estável mais recente compatível da linha Spring Boot 3.x no momento do bootstrap. Fixar versão exata e verificar compatibilidade de plugins/dependências no build; não migrar para Spring Boot 4.x contra a linha exigida.
- **API:** HTTP REST versionada em `/api/v1`.
- **Banco:** PostgreSQL como banco relacional principal, Spring Data JPA e Hibernate.
- **Schema:** Flyway com migrações SQL versionadas. Hibernate não cria/atualiza/remove esquema automaticamente; aplicar migração antes de validar o esquema.
- **Integridade:** constraints relacionais e índices são definidos com o modelo aprovado. Nenhuma tabela ou migration de negócio é criada nesta revisão.
- **Configuração:** propriedades Spring externas. Usar `SPRING_DATASOURCE_URL`, `SPRING_DATASOURCE_USERNAME` e `SPRING_DATASOURCE_PASSWORD` para conexão; credenciais de cada ambiente vêm de variáveis/secret store, nunca de arquivos versionados.
- **Desenvolvimento:** Dev Container/Codespaces e PostgreSQL local (Compose opcional) serão implementados depois. Um volume nomeado pode persistir durante reconstruções normais, mas a exclusão do Codespace pode eliminar volume e dados; não é backup permanente.
- **Testes:** Testcontainers com PostgreSQL real, sem H2 como substituto de integração ou concorrência. Dados de teste são efêmeros.
- **Execução/produção futura:** fornecer URL e credenciais via ambiente/secret store; nenhum deployment ou configuração de produção foi criado nesta tarefa.

## 5. Concorrência e ciclo da ocupação

ADR-003 e ADR-006 definem o protocolo técnico da agenda:

1. Cada criação, aprovação, alteração ou remoção de ocupação executa em transação PostgreSQL.
2. Bloquear pessimisticamente os registros estáveis de todos os recursos envolvidos (`SELECT ... FOR UPDATE`).
3. Adquirir o conjunto completo em ordem determinística: tipo canônico do recurso e, dentro do tipo, identificador estável. Nunca usar a ordem de chegada da requisição.
4. Após obter todos os locks, reconsultar conflitos na mesma transação e então gravar a reserva e seus vínculos.
5. Confirmar tudo atomicamente. Conflito ou falha reverte a reserva, os vínculos e a auditoria da operação.

O intervalo é semiaberto `[início, fim)`: início inclusivo e fim exclusivo; intervalos adjacentes não se sobrepõem. Armazenar instantes normalizados em UTC. A zona/calendário institucional de apresentação e políticas de datas continuam sujeitas a decisão de produto.

`PENDENTE` e `APROVADA` ocupam a agenda; `REJEITADA` e `CANCELADA` não ocupam. Uma transição para estado bloqueante adquire os mesmos locks e revalida. A transição para estado não bloqueante mantém os locks até o commit. Solicitações especiais não são pré-condição global de reserva. Aprovações concorrentes incompatíveis competem pelos mesmos locks: apenas a transação que ainda encontra disponibilidade pode confirmar; as demais retornam conflito. Não há garantia de prioridade/fairness entre solicitações.

Reservas que envolvam vários recursos bloqueiam todos antes da revalidação. Para materiais quantitativos, bloquear o registro estável do recurso pai e validar a demanda agregada; não presumir que uma constraint simples represente quantidades. Bloqueios e manutenção usam o mesmo protocolo, sem cancelar reservas existentes automaticamente.

`EXCLUDE USING gist` pode ser avaliada apenas como defesa adicional se o esquema representar cada ocupação por recurso/intervalo e a constraint cobrir exatamente estados bloqueantes. Até modelagem e teste no esquema real, não é considerada garantia. Testes de concorrência com conexões independentes PostgreSQL/Testcontainers são obrigatórios antes de liberar reservas: reserva-reserva, aprovação-aprovação, aprovação-reserva, reserva-manutenção, múltiplos recursos em ordens diferentes, rejeição/cancelamento, rollback e estado final sem conflito.

## 6. Segurança, auditoria e API

**Autenticação/autorização (ADR-005):** Spring Security com JWT de acesso stateless, assinado assimetricamente RS256 e validade máxima de 15 minutos. Validar assinatura, emissor, audiência e expiração. O token contém somente `sub`, papéis, `iss`, `aud`, `iat` e `exp`; sem dados pessoais ou segredos. Chaves privada/pública são carregadas por configuração externa/secret store, nunca versionadas ou registradas em logs; `kid` identifica chaves para rotação. Não criar refresh token nem provedor externo de identidade no baseline. Se credenciais locais forem escolhidas, senhas são armazenadas com BCrypt.

Papéis: Solicitante limitado às próprias reservas e à consulta declarada; Responsável às ações declaradas de aprovação, validação docente e acompanhamento; Administrador às categorias de gestão explicitamente listadas no PRD. Negar por padrão, verificar propriedade/escopo no servidor e não inferir CRUD ou permissões adicionais. Acesso não autorizado não pode mutar estado. Nunca registrar senhas, JWTs, chaves, segredos ou payloads sensíveis integrais.

**Auditoria (ADR-007):** eventos de mutações de reservas/recursos, mudanças de estado, decisões de aprovação e ações de retirada/devolução que vierem a ser implementadas registram somente ator, ação, entidade, instante UTC e resultado. Persistir evento na mesma transação da operação; falha de auditoria reverte mutação coberta. Logs técnicos estruturados, quando necessários, são distintos da trilha de negócio. Retenção, acesso e descarte dependem de política institucional e devem ser definidos antes de dados reais.

**Contrato HTTP (ADR-009):** REST sob `/api/v1`, DTOs e validação na borda. Respostas de erro centralizadas em `application/problem+json` conforme RFC 9457: `400` validação, `401` autenticação, `403` papel insuficiente, `404` inexistente/fora do escopo quando apropriado, `409` conflito de agenda/estado e `500` falha inesperada genérica. Falhas técnicas não são mascaradas como `409`. Não retornar SQL, stack trace, nomes de constraints ou segredos. Listagens que precisarem de paginação usam `page`, `size`, ordenação estável e limite máximo definido no contrato. OpenAPI/Swagger é documentação futura compatível, não afirma endpoints implementados.

Notificações e integrações externas estão fora do baseline (ADR-008). Relatórios continuam condicionados a Q-12. Não adicionar filas, outbox, workers ou serviços externos por inferência.

## 7. Testes, cobertura e CI

| Camada | Ferramentas e finalidade |
|---|---|
| Unidade | JUnit 5 para domínio/casos de uso; Mockito quando isolamento oferecer benefício real. |
| API/contexto | Spring Boot Test e MockMvc para inicialização, validação, autorização e Problem Details. |
| Integração | Testcontainers com PostgreSQL para JPA, Flyway, transações, locks, auditoria e constraints aplicáveis; sem H2 substituto. |
| Concorrência | Conexões independentes sincronizadas; validar resultado, 409, rollback e invariante final. |
| Cobertura | JaCoCo sobre classes de produção incluídas no build: mínimo 80% linhas e 70% branches. Sem exclusões ad hoc para passar o gate. |
| CI | GitHub Actions com Java 21 executando `./mvnw -B verify`; actions estáveis/compatíveis fixadas por SHA revisado; testes PostgreSQL e limites JaCoCo aplicados. |

O gate também exige 100% de rastreabilidade para requisitos classificados como críticos e nenhum defeito/vulnerabilidade crítica nos critérios de entrega. A classificação de criticidade é responsabilidade de produto e ainda não foi preenchida; não declarar o gate satisfeito enquanto faltar. SonarCloud é integração futura opcional, sem token ou ID de projeto assumidos. Não definir gate de carga sem workload/SLO aprovado.

## 8. Riscos e pendências de produto

| Pendência | Limite para implementação |
|---|---|
| Q-02: conflitos por tipo de recurso, docentes, materiais e quantidades | Refinar regra antes da disponibilidade/reserva; locks protegem os recursos identificados, sem inventar conflito entre tipos. |
| Q-03/Q-07: operações de gestão e escopo de retirada/devolução | Não presumir CRUD nem passos de negócio. |
| Q-05/Q-06/Q-08/Q-15: aprovação, alocação, manutenção e recurso restrito | Definir critérios/efeitos antes de implementar esses fluxos; manutenção não cancela reservas automaticamente. |
| Q-12/Q-13: relatórios e notificações | Fora do baseline até aprovação específica. |
| Q-11: retenção e acesso à auditoria | Definir política institucional antes de operar dados reais. |
| Q-16: criticidade e métricas não cobertas por gate | Classificar requisitos críticos; disponibilidade, performance, RTO/RPO e escala seguem sem SLO. |
| Constraint de exclusão PostgreSQL | Não contar como garantia antes de provar que o modelo e estados a suportam. |

RF-14 é derivação contextual de REQ-01 para rastrear concorrência, não nova regra de produto. A prioridade relativa de negócio dos RF permanece não definida.

## 9. Rastreabilidade das decisões

| Decisão | ADR | Requisitos/IDs | Documentos impactados |
|---|---|---|---|
| Monólito modular, camadas, dependências, DTOs, Java 21, Spring Boot 3.x e Maven Wrapper | [ADR-001](adr/ADR-001-monolito-modular-em-camadas.md) | REQ-01; RNF-04; CT-01..03 | [PRD](prd.md), [RTM](rtm.md), [README](../README.md), checklist |
| PostgreSQL, Spring Data JPA/Hibernate e configuração externa | [ADR-002](adr/ADR-002-postgresql-como-persistencia.md) | RF-04..09/RF-14; RN-01 | [RTM](rtm.md), README, checklist |
| Locks pessimistas ordenados, revalidação, intervalo e defesa concorrente | [ADR-003](adr/ADR-003-protecao-contra-dupla-reserva.md) | RF-09/RF-14; RN-01; Q-01/Q-02/Q-08/Q-09 | [PRD](prd.md), RTM, README, checklist |
| Transação na camada de aplicação e atomicidade | [ADR-004](adr/ADR-004-fronteiras-transacionais.md) | REQ-01/03/04; RF-09/RF-10/RF-14 | RTM, checklist |
| Spring Security, JWT, papéis, propriedade e segredo externo | [ADR-005](adr/ADR-005-autenticacao-e-autorizacao.md) | RF-02/RF-03/RF-10; RN-02..04; Q-10 | PRD, RTM, README, checklist |
| Estados bloqueantes/não bloqueantes da agenda | [ADR-006](adr/ADR-006-ciclo-de-vida-e-estados.md) | RF-02/RF-09/RF-14; RN-01; Q-04 | PRD, RTM, checklist |
| Auditoria mínima atômica | [ADR-007](adr/ADR-007-auditoria-operacional.md) | RF-03..RF-08/RF-10/RF-12/RF-13; Q-11 | PRD, RTM, README, checklist |
| Notificações e integrações fora do baseline | [ADR-008](adr/ADR-008-notificacoes-e-integracoes.md) | Q-13 | PRD, README, checklist |
| REST, RFC 9457, erros e paginação | [ADR-009](adr/ADR-009-erros-http.md) | RF-01/RF-02/RF-09/RF-14; Q-09/Q-10 | PRD, RTM, README, checklist |
| JUnit 5, MockMvc, Testcontainers, JaCoCo e GitHub Actions | [ADR-010](adr/ADR-010-estrategia-de-testes.md) | RNF-01..05; CT-01..03; Q-16 | PRD, RTM, README, checklist |
| Dev Container/Codespaces e dados locais não permanentes | [ADR-011](adr/ADR-011-codespaces-devcontainer.md) | CT-01..03; Q-16 | README, RTM, checklist |
| Flyway e SQL versionado | [ADR-012](adr/ADR-012-migracoes-de-banco.md) | RF-03..09/RF-14; RNF-01/03/05 | RTM, README, checklist |

## 10. Estado da implementação

Esta ratificação é documental. Permanecem para uma etapa futura: criação do projeto Maven e Wrapper, Dev Container, serviço PostgreSQL local opcional, Flyway sem tabelas de negócio prematuras, testes de contexto/configuração e integração Testcontainers e workflow GitHub Actions. Os diagramas foram alinhados aos ADRs, mas não representam implementação. Funcionalidades de negócio continuam dependendo de stories e das decisões de produto pendentes.
