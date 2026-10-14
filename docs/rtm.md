# Matriz de rastreabilidade (RTM)

**Status:** rascunho de rastreabilidade de produto. Decisões técnicas estão ratificadas nos ADRs; campos de teste, commit e evidência CI permanecem vazios porque não há implementação, testes ou CI executados.

## Legenda

- **Origem:** requisito de produto REQ, contexto CTX ou princípio POL com rótulo documental atribuído nesta documentação; os IDs não eram identificadores do enunciado original.
- **Criticidade e risco:** como as fontes não fornecem classificação, ambos são `não definido/pendente`; nenhuma pontuação foi arbitrada.
- **Critério de aceite:** ver o RF/RNF referenciado. Critérios de produto condicionais permanecem não prontos para teste até a decisão Q indicada; ADRs não são evidência de implementação.
- **Teste**, **Commit** e **Evidência de CI:** campos reservados, intencionalmente sem preenchimento.
- CT-01..03 são restrições/contexto tecnológico ratificadas nos ADRs, não evidência de ferramentas já configuradas.

## Requisitos funcionais

| Requisito | Origem | Criticidade | Risco | Critério de aceite | Teste | Commit | Evidência de CI |
|---|---|---|---|---|---|---|---|
| [RF-01](prd.md#rf-01--consultar-disponibilidade) | REQ-02 | Não definido/pendente | Não definido/pendente | Consulta representa disponibilidade do escopo definido; condicionado a Q-01/Q-02, não pronto para teste. |  |  |  |
| [RF-02](prd.md#rf-02--gerenciar-proprias-reservas) | REQ-02 | Não definido/pendente | Não definido/pendente | Operações aprovadas para reservas próprias são suportadas; condicionado a Q-03/Q-10, não pronto para teste. |  |  |  |
| [RF-03](prd.md#rf-03--gerenciar-usuarios) | REQ-04 | Não definido/pendente | Não definido/pendente | Operações de usuário acordadas são suportadas; condicionado a Q-03, não pronto para teste. |  |  |  |
| [RF-04](prd.md#rf-04--gerenciar-salas) | REQ-04 | Não definido/pendente | Não definido/pendente | Operações de sala acordadas são suportadas; condicionado a Q-03, não pronto para teste. |  |  |  |
| [RF-05](prd.md#rf-05--gerenciar-professores) | REQ-04 | Não definido/pendente | Não definido/pendente | Operações de professor acordadas são suportadas; condicionado a Q-03, não pronto para teste. |  |  |  |
| [RF-06](prd.md#rf-06--gerenciar-materiais) | REQ-04 | Não definido/pendente | Não definido/pendente | Operações de material acordadas são suportadas; condicionado a Q-03, não pronto para teste. |  |  |  |
| [RF-07](prd.md#rf-07--gerenciar-bloqueios) | REQ-04 | Não definido/pendente | Não definido/pendente | Operações e efeitos acordados para bloqueios são suportados; condicionado a Q-03/Q-08, não pronto para teste. |  |  |  |
| [RF-08](prd.md#rf-08--gerenciar-periodos-de-manutencao) | REQ-04 | Não definido/pendente | Não definido/pendente | Operações, intervalos e efeitos acordados para manutenção são suportados; condicionado a Q-03/Q-08, não pronto para teste. |  |  |  |
| [RF-09](prd.md#rf-09--preservar-reservas-sem-conflitos-de-horarios) | REQ-01 | Não definido/pendente | Não definido/pendente | Intervalo `[início, fim)` e estados técnicos bloqueantes/não bloqueantes seguem ADR-003/006; predicados de conflito por tipo de recurso continuam condicionados a Q-02/Q-04. |  |  |  |
| [RF-10](prd.md#rf-10--aprovar-solicitacoes-especiais) | REQ-03 | Não definido/pendente | Não definido/pendente | A ação de aprovação é representada conforme definição, critérios e resultado aprovados; Q-05. |  |  |  |
| [RF-11](prd.md#rf-11--validar-alocacao-de-docentes) | REQ-03 | Não definido/pendente | Não definido/pendente | Resultado da validação corresponde aos critérios acordados; Q-06. |  |  |  |
| [RF-12](prd.md#rf-12--acompanhar-retirada-de-materiais) | REQ-03 | Não definido/pendente | Não definido/pendente | Sistema apresenta/registra somente o acompanhamento definido para retirada; Q-07. |  |  |  |
| [RF-13](prd.md#rf-13--acompanhar-devolucao-de-materiais) | REQ-03 | Não definido/pendente | Não definido/pendente | Sistema apresenta/registra somente o acompanhamento definido para devolução; Q-07. |  |  |  |
| [RF-14](prd.md#rf-14--preservar-ausencia-de-conflitos-em-requisicoes-concorrentes) | Derivação solicitada de REQ-01; não é texto independente da fonte | Não definido/pendente | Não definido/pendente | Locks ordenados e revalidação serializam gravações; conflito na revalidação resulta em HTTP 409 (ADR-003/009). Invariante por tipo depende de Q-02/Q-04; prioridade/fairness e estado de negócio detalhado continuam abertos em Q-09. |  |  |  |

## Regras confirmadas

| Requisito | Origem | Criticidade | Risco | Critério de aceite | Teste | Commit | Evidência de CI |
|---|---|---|---|---|---|---|---|
| [RN-01](requisitos/regras-negocio.md#rn-01--invariante-geral-de-reservas-sem-conflitos) | REQ-01 | Não definido/pendente | Não definido/pendente | Intervalo e estados técnicos de ocupação definidos em ADR-003/006; predicado de conflito por tipo de recurso continua pendente em Q-02, portanto não pronto para teste de aceite. |  |  |  |
| [RN-02](requisitos/regras-negocio.md#rn-02--atribuicoes-declaradas-do-solicitante) | REQ-02 | Não definido/pendente | Não definido/pendente | Atribuição documentada de consultar disponibilidade e gerenciar próprias reservas; Spring Security/JWT e verificação de propriedade foram decididos em ADR-005, mas operações e provisionamento de identidade permanecem pendentes. |  |  |  |
| [RN-03](requisitos/regras-negocio.md#rn-03--atribuicoes-declaradas-do-responsavel) | REQ-03 | Não definido/pendente | Não definido/pendente | Atribuições documentadas de aprovar, validar e acompanhar; critérios/efeitos pendentes. |  |  |  |
| [RN-04](requisitos/regras-negocio.md#rn-04--atribuicoes-declaradas-do-administrador) | REQ-04 | Não definido/pendente | Não definido/pendente | Atribuição documentada de gerenciar itens declarados; operações pendentes. |  |  |  |

## Requisitos de qualidade/processo com fonte

| Requisito | Origem | Criticidade | Risco | Critério de aceite | Teste | Commit | Evidência de CI |
|---|---|---|---|---|---|---|---|
| [RNF-01](requisitos/requisitos-nao-funcionais.md#rnf-01--testes-automatizados-e-praticas-tddbdd) | CTX-02 | Não definido/pendente | Não definido/pendente | JUnit 5, Spring Boot Test/MockMvc e Testcontainers/PostgreSQL real; H2 não substitui integração PostgreSQL. |  |  |  |
| [RNF-02](requisitos/requisitos-nao-funcionais.md#rnf-02--consideracao-de-atam) | CTX-02 | Não definido/pendente | Não definido/pendente | Avaliação ATAM documentada; execução/evidências operacionais ainda pendentes. |  |  |  |
| [RNF-03](requisitos/requisitos-nao-funcionais.md#rnf-03--rastreabilidade-por-identificadores) | CTX-02, POL-02 | Não definido/pendente | Não definido/pendente | Rastrear 100% dos requisitos classificados como críticos a testes/evidências; classificação e execução pendentes. |  |  |  |
| [RNF-04](requisitos/requisitos-nao-funcionais.md#rnf-04--preservar-responsabilidades-seguranca-testabilidade-e-clareza) | POL-01 | Não definido/pendente | Não definido/pendente | Camadas, Spring Security/JWT e autorização por papel/propriedade definidos; gate sem defeito/vulnerabilidade crítica. |  |  |  |
| [RNF-05](requisitos/requisitos-nao-funcionais.md#rnf-05--integracao-a-ci) | CTX-02 | Não definido/pendente | Não definido/pendente | GitHub Actions executa `./mvnw -B verify` com Java 21; JaCoCo mínimo 80% linhas e 70% branches. Workflow ainda não existe. |  |  |  |

## Restrições/contexto tecnológico documentados

| Requisito | Origem | Criticidade | Risco | Critério de aceite | Teste | Commit | Evidência de CI |
|---|---|---|---|---|---|---|---|
| CT-01: Java 21 | CTX-01 | Não definido/pendente | Não definido/pendente | Java 21 LTS adotado por ADR-001/010/011; toolchain ainda não configurada no repositório. |  |  |  |
| CT-02: Spring Boot 3.x | CTX-01 | Não definido/pendente | Não definido/pendente | Última versão estável compatível da linha 3.x será fixada no bootstrap e validada com plugins/dependências. |  |  |  |
| CT-03: Maven | CTX-01 | Não definido/pendente | Não definido/pendente | Maven Wrapper versionado; PostgreSQL/JPA/Hibernate e Flyway conforme ADR-002/012. |  |  |  |

## Rastreabilidade complementar

- O detalhamento dos critérios condicionais está nos [14 RF](prd.md#requisitos-funcionais), com origem, ator e prioridade.
- Decisões técnicas e perguntas parcialmente respondidas: [ADRs](adr/README.md) e [perguntas abertas](prd.md#perguntas-abertas).
- Perguntas de domínio, inclusive intervalos, sobreposição, concorrência, aprovação, estados, manutenção, permissões, auditoria, relatórios e notificações: [Q-01 a Q-16](prd.md#perguntas-abertas).
- Regras e limites: [regras de negócio](requisitos/regras-negocio.md).
- Casos de uso descrevem somente os fluxos literalmente conhecidos: [casos de uso](requisitos/casos-de-uso.md).
- Nenhum campo de teste, commit ou CI é preenchido por inferência; atualizar após decisões aprovadas e evidência real.
- A cobertura de 100% se aplica aos requisitos classificados como críticos; não há classificação nem evidência de cobertura registrada nesta etapa.
