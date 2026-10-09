# Matriz de rastreabilidade (RTM)

**Status:** rascunho para validação. Campos de teste, commit e evidência CI estão reservados e vazios; não há implementação, testes ou evidências registrados nesta etapa.

## Legenda

- **Origem:** requisito de produto REQ, contexto CTX ou princípio POL com rótulo documental atribuído nesta documentação; os IDs não eram identificadores do enunciado original.
- **Criticidade e risco:** como as fontes não fornecem classificação, ambos são `não definido/pendente`; nenhuma pontuação foi arbitrada.
- **Critério de aceite:** ver o RF/RNF referenciado. Critérios condicionais permanecem não prontos para teste até a decisão Q indicada.
- **Teste**, **Commit** e **Evidência de CI:** campos reservados, intencionalmente sem preenchimento.
- CT-01..03 são restrições/contexto tecnológico, não requisitos de qualidade nem decisões arquiteturais tomadas nesta etapa.

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
| [RF-09](prd.md#rf-09--preservar-reservas-sem-conflitos-de-horarios) | REQ-01 | Não definido/pendente | Não definido/pendente | O conjunto resultante não contém par que viole a definição aprovada de conflito/ocupação; Q-01/Q-02/Q-04. |  |  |  |
| [RF-10](prd.md#rf-10--aprovar-solicitacoes-especiais) | REQ-03 | Não definido/pendente | Não definido/pendente | A ação de aprovação é representada conforme definição, critérios e resultado aprovados; Q-05. |  |  |  |
| [RF-11](prd.md#rf-11--validar-alocacao-de-docentes) | REQ-03 | Não definido/pendente | Não definido/pendente | Resultado da validação corresponde aos critérios acordados; Q-06. |  |  |  |
| [RF-12](prd.md#rf-12--acompanhar-retirada-de-materiais) | REQ-03 | Não definido/pendente | Não definido/pendente | Sistema apresenta/registra somente o acompanhamento definido para retirada; Q-07. |  |  |  |
| [RF-13](prd.md#rf-13--acompanhar-devolucao-de-materiais) | REQ-03 | Não definido/pendente | Não definido/pendente | Sistema apresenta/registra somente o acompanhamento definido para devolução; Q-07. |  |  |  |
| [RF-14](prd.md#rf-14--preservar-ausencia-de-conflitos-em-requisicoes-concorrentes) | Derivação solicitada de REQ-01; não é texto independente da fonte | Não definido/pendente | Não definido/pendente | Estado final sem duas reservas que violem a definição aprovada de conflito; pedido vencedor/resposta/transição dependem de Q-09, além de Q-01/Q-02/Q-04. |  |  |  |

## Regras confirmadas

| Requisito | Origem | Criticidade | Risco | Critério de aceite | Teste | Commit | Evidência de CI |
|---|---|---|---|---|---|---|---|
| [RN-01](requisitos/regras-negocio.md#rn-01--invariante-geral-de-reservas-sem-conflitos) | REQ-01 | Não definido/pendente | Não definido/pendente | Invariante “reservas sem conflitos de horários”; significado testável pendente de Q-01/Q-02/Q-04. |  |  |  |
| [RN-02](requisitos/regras-negocio.md#rn-02--atribuicoes-declaradas-do-solicitante) | REQ-02 | Não definido/pendente | Não definido/pendente | Atribuição documentada de consultar disponibilidade e gerenciar próprias reservas; operações/propriedade pendentes. |  |  |  |
| [RN-03](requisitos/regras-negocio.md#rn-03--atribuicoes-declaradas-do-responsavel) | REQ-03 | Não definido/pendente | Não definido/pendente | Atribuições documentadas de aprovar, validar e acompanhar; critérios/efeitos pendentes. |  |  |  |
| [RN-04](requisitos/regras-negocio.md#rn-04--atribuicoes-declaradas-do-administrador) | REQ-04 | Não definido/pendente | Não definido/pendente | Atribuição documentada de gerenciar itens declarados; operações pendentes. |  |  |  |

## Requisitos de qualidade/processo com fonte

| Requisito | Origem | Criticidade | Risco | Critério de aceite | Teste | Commit | Evidência de CI |
|---|---|---|---|---|---|---|---|
| [RNF-01](requisitos/requisitos-nao-funcionais.md#rnf-01--testes-automatizados-e-praticas-tddbdd) | CTX-02 | Não definido/pendente | Não definido/pendente | Adoção de testes automatizados e TDD/BDD citada; escopo/medida pendentes de Q-16. |  |  |  |
| [RNF-02](requisitos/requisitos-nao-funcionais.md#rnf-02--consideracao-de-atam) | CTX-02 | Não definido/pendente | Não definido/pendente | ATAM citado; escopo/evidência de conclusão pendentes de Q-16. |  |  |  |
| [RNF-03](requisitos/requisitos-nao-funcionais.md#rnf-03--rastreabilidade-por-identificadores) | CTX-02, POL-02 | Não definido/pendente | Não definido/pendente | Relação de requisitos, regras, decisões e testes por IDs; completude e verificação operacional pendentes de Q-16. |  |  |  |
| [RNF-04](requisitos/requisitos-nao-funcionais.md#rnf-04--preservar-responsabilidades-seguranca-testabilidade-e-clareza) | POL-01 | Não definido/pendente | Não definido/pendente | Preservar princípios citados; critérios objetivos pendentes de Q-16. |  |  |  |
| [RNF-05](requisitos/requisitos-nao-funcionais.md#rnf-05--integracao-a-ci) | CTX-02 | Não definido/pendente | Não definido/pendente | CI consta no contexto; pipeline, gates e evidências pendentes de Q-16. |  |  |  |

## Restrições/contexto tecnológico documentados

| Requisito | Origem | Criticidade | Risco | Critério de aceite | Teste | Commit | Evidência de CI |
|---|---|---|---|---|---|---|---|
| CT-01: Java 21 | CTX-01 | Não definido/pendente | Não definido/pendente | Tecnologia contextual registrada; critério de confirmação/uso não definido nesta etapa. |  |  |  |
| CT-02: Spring Boot 3.x | CTX-01 | Não definido/pendente | Não definido/pendente | Tecnologia contextual registrada; versão menor/critério de confirmação não definido nesta etapa. |  |  |  |
| CT-03: Maven | CTX-01 | Não definido/pendente | Não definido/pendente | Tecnologia contextual registrada; critério de confirmação/uso não definido nesta etapa. |  |  |  |

## Rastreabilidade complementar

- O detalhamento dos critérios condicionais está nos [14 RF](prd.md#requisitos-funcionais), com origem, ator e prioridade.
- Perguntas de domínio, inclusive intervalos, sobreposição, concorrência, aprovação, estados, manutenção, permissões, auditoria, relatórios e notificações: [Q-01 a Q-16](prd.md#perguntas-abertas).
- Regras e limites: [regras de negócio](requisitos/regras-negocio.md).
- Casos de uso descrevem somente os fluxos literalmente conhecidos: [casos de uso](requisitos/casos-de-uso.md).
- Nenhum campo de teste, commit ou CI é preenchido por inferência; atualizar após decisões aprovadas e evidência real.
