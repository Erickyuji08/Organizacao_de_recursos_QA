# PRD — Projeto Organização de Recursos

**Status:** Rascunho para validação. Não aprovado para arquitetura ou implementação.

## Objetivo e escopo

Registrar, sem ampliar as fontes, as capacidades declaradas para gerenciamento de salas, professores e materiais e reservas sem conflitos; consulta e gerenciamento de reservas pelo solicitante; ações do responsável sobre solicitações especiais, alocação docente e circulação de materiais; e gerenciamento administrativo de cadastros, bloqueios e manutenção. Os 14 RF abaixo atendem à quantidade explicitamente solicitada. RF-01 a RF-13 decompõem capacidades literalmente declaradas; RF-14 é uma derivação contextual para tornar explícito o cenário concorrente pedido, não uma nova regra de negócio nem uma estratégia de implementação.

Não estão definidos neste documento fluxos completos, políticas, estados, permissões, arquitetura, integrações, operações CRUD, relatórios ou notificações. Não se escolhe entre alternativas em aberto.

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
- **POL-05:** regra inicial do usuário: “sem arquitetura nesta etapa”.

POL e CTX são rótulos de rastreabilidade atribuídos nesta documentação; não são identificadores originais das frases. A visão geral declara o enunciado do usuário como única fonte de requisitos de produto. Personas não acrescentam dificuldades ou fluxos.

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
- **Descrição:** representar a invariante ampla declarada de reservas sem conflitos de horários. Não define recurso, intervalo, estados ou resolução de conflito.
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
- **Descrição:** decomposição contextual de RF-09 para requisições concorrentes, solicitada nesta especificação. Não é nova regra de domínio nem define estratégia técnica, pedido vencedor, resposta, estado ou transição.
- **Origem:** derivação explícita de REQ-01 para cobrir o cenário concorrente solicitado; não é requisito textual independente da fonte.
- **Ator:** Sistema; ator operacional e interação não definidos.
- **Prioridade:** conforme acima; prioridade relativa de negócio não fornecida.
- **Aceite (provisório):** Dadas submissões concorrentes e a definição aprovada de conflito e ocupação (Q-01, Q-02 e Q-04), quando as submissões forem processadas, então o estado final não deverá conter duas reservas que violem a definição aprovada; qual pedido prevalece, resposta e transição são lacunas em Q-09. **Não pronto para teste até Q-01/Q-02/Q-04/Q-09.**

## Regras de negócio e qualidade

- **RN-01:** invariante geral “reservas sem conflitos de horários”; origem REQ-01. A semântica de conflito não está definida. Ver [regras de negócio](requisitos/regras-negocio.md#regras-confirmadas) e Q-01/Q-02/Q-04.
- **RN-02:** atribuição declarada do solicitante: consultar disponibilidade e gerenciar próprias reservas; origem REQ-02. Não é política de autorização. Ver regras de negócio.
- **RN-03:** atribuições declaradas do responsável: aprovar solicitações especiais, validar alocação de docentes e acompanhar retirada e devolução; origem REQ-03. Não é gate ou política de autorização.
- **RN-04:** atribuição declarada do administrador: gerenciar usuários, salas, professores, materiais, bloqueios e períodos de manutenção; origem REQ-04. Não é política de autorização.

Os RNF com fonte e as lacunas métricas estão em [requisitos não funcionais](requisitos/requisitos-nao-funcionais.md). A tecnologia citada em CTX-01 é registrada como contexto/restrição tecnológica, não como decisão arquitetural feita nesta etapa.

## Perguntas abertas

Todas as alternativas abaixo são **alternativas não decididas**. Nenhuma hipótese foi adotada. Critérios afetados não ficam prontos para teste até resposta e aprovação documental.

| ID | Decisão necessária | Alternativas não decididas |
|---|---|---|
| Q-01 | **Aberta:** como representar início, fim, fuso/calendário e limites do intervalo temporal, e quais validações temporais aplicar? | Para os limites, intervalo semiaberto `[início, fim)`; intervalo com limites inclusivos; ou outra convenção documentada. Para início/fim, exigir início anterior ao fim; permitir outros casos sob validação explicitamente aprovada; ou adotar outra validação documentada. Para datas, permitir ou não datas passadas e definir ou não um horizonte máximo futuro. Definir também fuso horário, calendário de referência e tratamento de mudanças de fuso/calendário. Nenhuma opção foi escolhida. |
| Q-02 | **Aberta:** o que conflita para cada tipo de recurso, como combinar intervalos e qual o efeito de adjacência ou margem? | Para salas, considerar conflito somente quando a mesma sala estiver ocupada em períodos sobrepostos, ou definir outro critério. Para docentes, decidir se o mesmo docente pode ou não ser alocado a recursos distintos em períodos sobrepostos. Para materiais, decidir entre controlar itens individualmente ou disponibilidade por tipo e quantidade, além de definir como comparar disponibilidade e demanda. Definir se intervalos adjacentes conflitam e se há margem/buffer entre eles, incluindo sua extensão se aplicável. São alternativas por dimensão; nenhuma regra do projeto foi escolhida. |
| Q-03 | O que “gerenciar” significa para cada cadastro, bloqueio e manutenção? | Consultar e cadastrar; consultar, cadastrar, alterar e remover; outro conjunto explícito por entidade. Não se presume CRUD. |
| Q-04 | **Aberta:** como representar o ciclo de vida das reservas, suas decisões e seu encerramento, e quais registros afetam a agenda? | Como propostas meramente ilustrativas, pode-se considerar um estado único de reserva com registro separado da aprovação; um ciclo explícito com solicitação, decisão e encerramento; ou outro ciclo documentado. Para qualquer proposta, estados, nomes, transições e efeito sobre a agenda dependem de decisão. Não se impõem rótulos, transições nem uma alternativa como verdadeira. |
| Q-05 | **Aberta:** o que caracteriza uma solicitação especial, quais critérios se aplicam à aprovação e qual resultado deve ser registrado? | Definir a solicitação e os critérios de aprovação; definir como a decisão é representada; ou adotar outra definição aprovada. A aprovação pode apenas registrar uma decisão ou ter outro efeito a definir, sem presumir que bloqueie ou confirme uma reserva. A relação com “recurso restrito” é tratada em Q-15 e permanece aberta. |
| Q-06 | Quais critérios/dados são usados para validar alocação docente e qual efeito tem o resultado? | Validar contra critérios a definir e somente registrar resultado; validar e produzir resultado com efeito a definir; ou estabelecer outro efeito explícito. |
| Q-07 | O que significa acompanhar retirada/devolução, quem participa e que informação se acompanha? | Acompanhar eventos; acompanhar responsável/data/quantidade; outro escopo explícito para retirada e devolução, sem presumir etapas. |
| Q-08 | **Aberta:** como bloqueios e manutenção afetam os recursos e reservas existentes ou futuras? | Alternativas incluem afetar somente novas reservas; preservar reservas existentes sem alteração; ou marcar reservas existentes para análise, reagendamento ou cancelamento conforme decisão. Outro efeito também pode ser definido. O efeito precisa ser especificado, assim como intervalo e recurso abrangidos; não se inferem notificações. Nenhuma alternativa foi escolhida. |
| Q-09 | **Aberta:** qual resultado deve ocorrer quando requisições concorrentes disputam disponibilidade ou entram em conflito? | Alternativas ilustrativas incluem aceitar uma requisição e rejeitar outra; aceitar uma e deixar outra pendente; ou aplicar outro resultado aprovado. Permanecem abertas qual requisição vence (se houver vencedora), qual estado resulta para cada requisição e qual resposta é apresentada ou registrada. Nenhuma prioridade, ordem de vitória, estado ou resposta foi definida. |
| Q-10 | Quais permissões, identidade e escopo de propriedade cabem a cada perfil? | Autorizações apenas conforme atribuições declaradas; permissões adicionais explicitamente mapeadas; outro modelo aprovado. Definir como reconhecer solicitante e “próprias”. Nenhuma opção foi adotada. |
| Q-11 | Que eventos/conteúdo de auditoria de produto são necessários e por quanto tempo? | Registrar eventos de criação/alteração; registrar também consultas e decisões; outro escopo; retenção limitada definida ou sem retenção de produto. Rastreabilidade de requisitos não determina auditoria operacional. |
| Q-12 | São necessários relatórios? Para quem, sobre quais dados e período? | Não incluir relatórios; incluir relatórios com escopo a definir; outro conjunto documentado. |
| Q-13 | São necessárias notificações? Para quem, em quais eventos e por qual canal? | Não incluir notificações; incluir notificações com eventos/canais a definir; outro conjunto documentado. |
| Q-14 | Há prioridades, cancelamentos, tolerâncias entre horários, prazos, limites de quantidade ou penalidades? | Para cada política, definir valor/regra explícita; declarar que não se aplica; ou deixar fora do escopo. Não se pressupõe prioridade, cancelamento, intervalo de tolerância, prazo, quantidade, multa ou retenção. |
| Q-15 | **Aberta:** o que significa “recurso restrito”, como se relaciona com “solicitação especial” e que efeito pode ter a aprovação? | “Solicitação especial” e “recurso restrito” podem ser conceitos independentes, equivalentes, parcialmente relacionados ou sem relação; a relação ainda precisa ser decidida. A classificação de recurso restrito pode não existir ou ser definida por critérios, responsável/perfil ou outra definição aprovada. A aprovação pode apenas registrar uma decisão ou, se explicitamente decidido, condicionar uma reserva; seu efeito permanece aberto. Nenhuma relação, classificação ou condição foi adotada. |
| Q-16 | Quais medidas operacionais tornam verificáveis os RNF e a adoção de processo? | Definir critérios/métricas para testes, TDD/BDD, ATAM, rastreabilidade e CI; documentar verificação qualitativa sem limiar numérico; ou declarar item não aplicável. Alternativas não decididas. |

## Decisões pendentes antes da arquitetura

Antes de qualquer definição arquitetural, validar este rascunho e decidir/documentar, no mínimo, Q-01 a Q-10, que determinam significado temporal e de conflito, estados/ocupação, capacidades, aprovações, validação, circulação de materiais, manutenção/ bloqueios, concorrência e permissões. Decidir também Q-11 a Q-16 para delimitar auditoria, relatórios, notificações, políticas operacionais, recursos restritos e critérios verificáveis de qualidade/processo. Não há arquitetura proposta nem hipótese adotada neste PRD.
