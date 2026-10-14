# PRD — Projeto Organização de Recursos

**Status:** Rascunho para validação de produto. As decisões técnicas foram ratificadas nos ADRs; isso não aprova as regras de negócio ainda abertas nem afirma que exista implementação.

## Objetivo e escopo

Registrar, sem ampliar as fontes, as capacidades declaradas para gerenciamento de salas, professores e materiais e reservas sem conflitos; consulta e gerenciamento de reservas pelo solicitante; ações do responsável sobre solicitações especiais, alocação docente e circulação de materiais; e gerenciamento administrativo de cadastros, bloqueios e manutenção. Os 14 RF abaixo atendem à quantidade explicitamente solicitada. RF-01 a RF-13 decompõem capacidades literalmente declaradas; RF-14 é uma derivação contextual para tornar explícito o cenário concorrente pedido, não uma nova regra de negócio nem uma estratégia de implementação.

Fluxos completos, políticas de negócio, operações CRUD, critérios de aprovação e efeitos de manutenção continuam condicionados às perguntas abertas abaixo. Decisões técnicas arquiteturais estão registradas em [ADRs](adr/README.md); elas não ampliam o escopo funcional.

## Fontes e identificadores

Os IDs REQ são identificadores documentais criados nos documentos do projeto para rastreabilidade, não IDs presentes no enunciado original.

- **REQ-01:** “Projeto Organização de Recursos: sistema para gerenciamento de salas, professores e materiais, com reservas sem conflitos de horários.” Fonte: enunciado do usuário, reproduzido em [visão geral](visao-geral.md) e [stakeholders](stakeholders.md#req-01-projeto-organizacao-de-recursos).
- **REQ-02:** “Solicitante: professor ou coordenador que consulta disponibilidade e gerencia suas reservas.” Fonte: enunciado do usuário, reproduzido em [stakeholders](stakeholders.md#req-02-solicitante).
- **REQ-03:** “Responsável: aprova solicitações especiais, valida alocação de docentes e acompanha retirada e devolução de materiais.” Fonte: enunciado do usuário, reproduzido em [stakeholders](stakeholders.md#req-03-responsavel).
- **REQ-04:** “Administrador: gerencia usuários, salas, professores, materiais, bloqueios e períodos de manutenção.” Fonte: enunciado do usuário, reproduzido em [stakeholders](stakeholders.md#req-04-administrador).
- **CTX-01:** contexto inicial fornecido pelo usuário: “repositório Codespaces; projeto adotará Java 21, Spring Boot 3.x e Maven”. ID atribuído aqui, não original.
- **CTX-02:** contexto inicial fornecido pelo usuário: “aplicação de testes automatizados, TDD/BDD, ATAM, rastreabilidade e CI”. ID atribuído aqui, não original.
- **POL-01:** regra inicial do usuário: “preservar responsabilidades, segurança, testabilidade e clareza”.
- **POL-02:** regra inicial do usuário: “relacionar requisitos, regras, decisões e testes por identificadores rastreáveis”.
- **POL-03:** regra inicial do usuário: “usar documentos aprovados como fonte”.
- **POL-04:** regra inicial do usuário: “não inventar requisitos/regras; registrar divergências”.
- **POL-05:** a solicitação anterior limitava arquitetura naquela etapa; a autorização posterior permite apenas ratificação documental nesta etapa, sem bootstrap ou implementação funcional.

POL e CTX são rótulos de rastreabilidade atribuídos nesta documentação; não são identificadores originais das frases. A visão geral declara o enunciado do usuário como fonte de requisitos de produto. Personas não acrescentam dificuldades ou fluxos. Decisões técnicas posteriores estão em ADR-001..ADR-012.

## Requisitos funcionais

Prioridade atribuída a todos os RF: **Obrigatório nesta entrega por solicitação explícita de 14 RF obrigatórios**. A prioridade relativa de negócio não foi fornecida. Onde uma decisão de domínio é necessária, o critério é provisório, condicionado à pergunta citada e **não está pronto para teste até que ela seja decidida**.

### RF-01 — Consultar disponibilidade
- **Descrição:** permitir ao solicitante (professor ou coordenador) consultar disponibilidade. Não se define objeto, filtros, período ou formato da consulta.
- **Origem:** REQ-02.
- **Ator:** Solicitante.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dado que os parâmetros e o significado de disponibilidade sejam acordados em Q-01 e Q-02, quando o solicitante consultar o escopo acordado, então o resultado deverá representar a disponibilidade desse escopo; mensuração e exemplo de resultado ficam condicionados à decisão. **Não pronto para teste até Q-01/Q-02.**

### RF-02 — Gerenciar próprias reservas
- **Descrição:** permitir ao solicitante gerenciar suas reservas. “Gerenciar” não especifica operações.
- **Origem:** REQ-02.
- **Ator:** Solicitante.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dado que as operações abrangidas e a identificação de “próprias” estejam acordadas em Q-03 e Q-10, quando o solicitante executar uma operação acordada sobre reserva identificada como própria, então o sistema deverá suportar a operação acordada; conjunto de operações e resultado ficam condicionados. **Não pronto para teste até Q-03/Q-10.**

### RF-03 — Gerenciar usuários
- **Descrição:** permitir ao administrador gerenciar usuários; operações não especificadas.
- **Origem:** REQ-04.
- **Ator:** Administrador.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dado que as operações de gerenciamento de usuários sejam definidas em Q-03, quando o administrador executar cada operação aprovada, então o sistema deverá suportá-la conforme o resultado acordado. **Não pronto para teste até Q-03.**

### RF-04 — Gerenciar salas
- **Descrição:** permitir ao administrador gerenciar salas; operações não especificadas.
- **Origem:** REQ-04.
- **Ator:** Administrador.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dado que as operações de gerenciamento de salas sejam definidas em Q-03, quando o administrador executar cada operação aprovada, então o sistema deverá suportá-la conforme o resultado acordado. **Não pronto para teste até Q-03.**

### RF-05 — Gerenciar professores
- **Descrição:** permitir ao administrador gerenciar professores; operações não especificadas.
- **Origem:** REQ-04.
- **Ator:** Administrador.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dado que as operações de gerenciamento de professores sejam definidas em Q-03, quando o administrador executar cada operação aprovada, então o sistema deverá suportá-la conforme o resultado acordado. **Não pronto para teste até Q-03.**

### RF-06 — Gerenciar materiais
- **Descrição:** permitir ao administrador gerenciar materiais; operações não especificadas.
- **Origem:** REQ-04.
- **Ator:** Administrador.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dado que as operações de gerenciamento de materiais sejam definidas em Q-03, quando o administrador executar cada operação aprovada, então o sistema deverá suportá-la conforme o resultado acordado. **Não pronto para teste até Q-03.**

### RF-07 — Gerenciar bloqueios
- **Descrição:** permitir ao administrador gerenciar bloqueios; significado, escopo e operações não especificados.
- **Origem:** REQ-04.
- **Ator:** Administrador.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dado que o significado e as operações de bloqueio sejam definidos em Q-03 e Q-08, quando o administrador executar uma operação aprovada, então o sistema deverá suportá-la conforme o resultado acordado. **Não pronto para teste até Q-03/Q-08.**

### RF-08 — Gerenciar períodos de manutenção
- **Descrição:** permitir ao administrador gerenciar períodos de manutenção; recursos afetados e operações não especificados.
- **Origem:** REQ-04.
- **Ator:** Administrador.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dado que as operações, intervalos e efeitos da manutenção sejam definidos em Q-03 e Q-08, quando o administrador executar uma operação aprovada, então o sistema deverá suportá-la conforme o resultado acordado. **Não pronto para teste até Q-03/Q-08.**

### RF-09 — Preservar reservas sem conflitos de horários
- **Descrição:** representar a invariante ampla declarada de reservas sem conflitos de horários. ADR-003/006 definem intervalo técnico e estados de ocupação; não definem quais dimensões por tipo de recurso constituem conflito.
- **Origem:** REQ-01.
- **Ator:** Sistema; não há ator operacional declarado.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dada a definição aprovada de conflito e de quais reservas contam como ocupação (Q-01, Q-02 e Q-04), quando reservas forem consideradas em conjunto, então o conjunto resultante não deverá conter um par que viole essa definição. **Não pronto para teste até Q-01/Q-02/Q-04.**

### RF-10 — Aprovar solicitações especiais
- **Descrição:** permitir ao responsável aprovar solicitações especiais; definição, critérios, resultado e sequência não especificados. Não presume aprovação como condição para reservar.
- **Origem:** REQ-03.
- **Ator:** Responsável.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dada uma solicitação classificada como especial conforme definição a decidir em Q-05, quando o responsável executar a ação de aprovação conforme critérios e resultado decididos em Q-05, então essa ação deverá ser representada conforme a decisão. **Não pronto para teste até Q-05.**

### RF-11 — Validar alocação de docentes
- **Descrição:** permitir ao responsável validar alocação de docentes; critérios, dados e efeito da validação não especificados.
- **Origem:** REQ-03.
- **Ator:** Responsável.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dada uma alocação e os critérios acordados em Q-06, quando o responsável executar a validação, então o resultado deverá corresponder aos critérios acordados, sem presumir aprovação, rejeição ou alteração da alocação. **Não pronto para teste até Q-06.**

### RF-12 — Acompanhar retirada de materiais
- **Descrição:** permitir ao responsável acompanhar retirada de materiais; participantes, informação e significado de acompanhamento não especificados.
- **Origem:** REQ-03.
- **Ator:** Responsável.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dado que o escopo de acompanhamento da retirada seja definido em Q-07, quando o responsável realizar a ação acordada, então o sistema deverá apresentar ou registrar apenas o acompanhamento definido. **Não pronto para teste até Q-07.**

### RF-13 — Acompanhar devolução de materiais
- **Descrição:** permitir ao responsável acompanhar devolução de materiais; participantes, informação e significado de acompanhamento não especificados.
- **Origem:** REQ-03.
- **Ator:** Responsável.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dado que o escopo de acompanhamento da devolução seja definido em Q-07, quando o responsável realizar a ação acordada, então o sistema deverá apresentar ou registrar apenas o acompanhamento definido. **Não pronto para teste até Q-07.**

### RF-14 — Preservar ausência de conflitos em requisições concorrentes
- **Descrição:** decomposição contextual de RF-09 para requisições concorrentes, solicitada nesta especificação. Não é nova regra de domínio. O protocolo técnico e HTTP 409 para conflito seguem ADR-003/009; não se define prioridade/fairness nem estado de negócio detalhado.
- **Origem:** derivação explícita de REQ-01 para cobrir o cenário concorrente solicitado; não é requisito textual independente da fonte.
- **Ator:** Sistema; ator operacional e interação não definidos.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dadas submissões incompatíveis e uma definição aprovada de conflito/ocupação (Q-01/Q-02/Q-04), quando forem processadas concorrentemente, então a proteção de locks/revalidação deve impedir confirmações incompatíveis e o pedido que falhar na revalidação recebe HTTP 409 (ADR-003/009). A prioridade/fairness, as demais transições de negócio e os critérios por tipo de recurso permanecem por definir. **Não pronto para teste de aceite até Q-02/Q-04 e estados de negócio correspondentes.**

## Regras de negócio e qualidade

- **RN-01:** invariante geral “reservas sem conflitos de horários”; origem REQ-01. A semântica de conflito não está definida. Ver [regras de negócio](requisitos/regras-negocio.md#regras-confirmadas) e Q-01/Q-02/Q-04.
- **RN-02:** atribuição declarada do solicitante: consultar disponibilidade e gerenciar próprias reservas; origem REQ-02. Não é política de autorização. Ver regras de negócio.
- **RN-03:** atribuições declaradas do responsável: aprovar solicitações especiais, validar alocação de docentes e acompanhar retirada e devolução; origem REQ-03. Não é gate ou política de autorização.
- **RN-04:** atribuição declarada do administrador: gerenciar usuários, salas, professores, materiais, bloqueios e períodos de manutenção; origem REQ-04. Não é política de autorização.

Os RNF com fonte e as lacunas métricas estão em [requisitos não funcionais](requisitos/requisitos-nao-funcionais.md). A tecnologia citada em CTX-01 é registrada como contexto/restrição tecnológica, não como decisão arquitetural feita nesta etapa.

## Perguntas abertas

As linhas identificam decisões técnicas já ratificadas, decisões parcialmente resolvidas e perguntas ainda abertas; alternativas não decididas não são defaults. Critérios funcionais afetados permanecem condicionados às respostas de produto e aprovação documental.

| ID | Decisão necessária | Alternativas não decididas |
|---|---|---|
| Q-01 | **Parcialmente decidida (ADR-003/006):** início inclusivo e fim exclusivo, intervalo `[início, fim)`, portanto intervalos adjacentes não conflitam. | Permanecem para produto: fuso/calendário de apresentação, política para datas passadas/horizonte futuro e validações de domínio além de início anterior ao fim. A persistência técnica normaliza instantes em UTC; não define o calendário institucional. |
| Q-02 | **Aberta:** o que conflita para cada tipo de recurso e como combinar ocupações/margens? | Para salas, considerar conflito somente quando a mesma sala estiver ocupada em períodos sobrepostos, ou definir outro critério. Para docentes, decidir se o mesmo docente pode ou não ser alocado a recursos distintos em períodos sobrepostos. Para materiais, decidir entre controlar itens individualmente ou disponibilidade por tipo e quantidade, além de definir como comparar disponibilidade e demanda. O intervalo semiaberto já ratificado em Q-01 significa que intervalos adjacentes não se sobrepõem; definir se há margem/buffer adicional e sua extensão, se aplicável. Nenhuma dimensão de conflito por tipo de recurso foi escolhida. |
| Q-03 | O que “gerenciar” significa para cada cadastro, bloqueio e manutenção? | Consultar e cadastrar; consultar, cadastrar, alterar e remover; outro conjunto explícito por entidade. Não se presume CRUD. |
| Q-04 | **Parcialmente decidida (ADR-006):** para proteção da agenda, `PENDENTE` e `APROVADA` ocupam; `REJEITADA` e `CANCELADA` não ocupam. Toda transição para estado ocupante revalida disponibilidade na transação. | Permanecem para produto: atores e transições permitidas além do mínimo, condições de cancelamento, terminalidade e relação entre aprovação especial e reserva. Aprovação especial não é pré-condição global. |
| Q-05 | **Aberta:** o que caracteriza uma solicitação especial, quais critérios se aplicam à aprovação e qual resultado deve ser registrado? | Definir a solicitação e os critérios de aprovação; definir como a decisão é representada; ou adotar outra definição aprovada. A aprovação pode apenas registrar uma decisão ou ter outro efeito a definir, sem presumir que bloqueie ou confirme uma reserva. A relação com “recurso restrito” é tratada em Q-15 e permanece aberta. |
| Q-06 | Quais critérios/dados são usados para validar alocação docente e qual efeito tem o resultado? | Validar contra critérios a definir e somente registrar resultado; validar e produzir resultado com efeito a definir; ou estabelecer outro efeito explícito. |
| Q-07 | O que significa acompanhar retirada/devolução, quem participa e que informação se acompanha? | Acompanhar eventos; acompanhar responsável/data/quantidade; outro escopo explícito para retirada e devolução, sem presumir etapas. |
| Q-08 | **Aberta:** como bloqueios e manutenção afetam os recursos e reservas existentes ou futuras? | Alternativas incluem afetar somente novas reservas; preservar reservas existentes sem alteração; ou marcar reservas existentes para análise, reagendamento ou cancelamento conforme decisão. Outro efeito também pode ser definido. O efeito precisa ser especificado, assim como intervalo e recurso abrangidos; não se inferem notificações. Nenhuma alternativa foi escolhida. |
| Q-09 | **Decisão técnica adotada (ADR-003/009):** serializar operações pelo lock dos recursos; a primeira transação que adquire locks e confirma revalidação poderá gravar; operações incompatíveis concorrentes não confirmam juntas e as perdedoras recebem HTTP 409. | Não há garantia de prioridade/fairness entre pedidos. A ordem depende da aquisição do lock; a política de negócio para fila/prioridade permanece fora do escopo. |
| Q-10 | **Parcialmente decidida (ADR-005):** JWT stateless, Spring Security, negar por padrão; Solicitante limitado às próprias reservas, Responsável às capacidades de aprovar/validar/acompanhar, Administrador às categorias de gestão listadas no PRD. Propriedade é verificada no servidor. | Não conceder CRUD ou permissões além das capacidades textuais. Fonte/provisionamento de identidade, credenciais e fluxo de emissão de token, além do escopo de operações que o PRD não descreve, devem ser definidos antes da implementação de autenticação. |
| Q-11 | **Parcialmente decidida (ADR-007):** auditar atomicamente mutações administrativas de usuários, salas, professores, materiais, bloqueios/manutenção e reservas, mudanças de estado, decisões de aprovação e ações de retirada/devolução implementadas; campos mínimos ator, ação, entidade, data/hora UTC e resultado. Não registrar credenciais, JWTs ou payloads sensíveis. | Período de retenção, acesso e descarte dependem de política institucional; definir antes de operar com dados reais. |
| Q-12 | São necessários relatórios? Para quem, sobre quais dados e período? | Não incluir relatórios; incluir relatórios com escopo a definir; outro conjunto documentado. |
| Q-13 | São necessárias notificações? Para quem, em quais eventos e por qual canal? | Não incluir notificações; incluir notificações com eventos/canais a definir; outro conjunto documentado. |
| Q-14 | Há prioridades, cancelamentos, tolerâncias entre horários, prazos, limites de quantidade ou penalidades? | Para cada política, definir valor/regra explícita; declarar que não se aplica; ou deixar fora do escopo. Não se pressupõe prioridade, cancelamento, intervalo de tolerância, prazo, quantidade, multa ou retenção. |
| Q-15 | **Aberta:** o que significa “recurso restrito”, como se relaciona com “solicitação especial” e que efeito pode ter a aprovação? | “Solicitação especial” e “recurso restrito” podem ser conceitos independentes, equivalentes, parcialmente relacionados ou sem relação; a relação ainda precisa ser decidida. A classificação de recurso restrito pode não existir ou ser definida por critérios, responsável/perfil ou outra definição aprovada. A aprovação pode apenas registrar uma decisão ou, se explicitamente decidido, condicionar uma reserva; seu efeito permanece aberto. Nenhuma relação, classificação ou condição foi adotada. |
| Q-16 | **Decidida para gates de engenharia (ADR-010):** JUnit 5, Spring Boot Test/MockMvc, Testcontainers/PostgreSQL, JaCoCo com 80% de linhas e 70% de branches, GitHub Actions com `./mvnw -B verify` em Java 21, rastreabilidade de 100% dos requisitos críticos e nenhum defeito/vulnerabilidade crítica no gate. | A classificação dos requisitos críticos deve ser feita pelos responsáveis do produto, sem arbitrar criticidade no código. Não foram definidos SLOs de disponibilidade/desempenho, RTO/RPO, SonarCloud ou gate de carga. |

## Pendências de produto antes das funcionalidades

As decisões arquiteturais estão ratificadas em [ADRs](adr/README.md), mas isso não aprova o PRD como especificação funcional completa. Permanecem pendentes questões de domínio em Q-02 a Q-08, Q-12 a Q-15 e parcelas de Q-01/Q-04/Q-10/Q-11/Q-16. Refinar e validar cada decisão com produto antes da implementação da funcionalidade afetada. Não criar interface visual, relatórios, notificações, CRUD, regra de prioridade ou política de manutenção por inferência.
