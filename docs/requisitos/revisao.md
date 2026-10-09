# Revisão independente de requisitos

**Data:** 2026-10-09
**Branch revisada:** `docs/prd-requisitos`
**Natureza:** revisão documental consultiva; não é QA gate formal de story.

**Resultado resumido:** a documentação apresenta escopo e perfis rastreáveis às fontes REQ-01..REQ-04 e evita declarar como aprovadas regras ainda não decididas. A maturidade é **insuficiente para baseline de arquitetura ou implementação** dos comportamentos centrais: semântica temporal, conflitos por recurso, concorrência, estados, permissões, efeitos de aprovação e manutenção ainda dependem de decisões. Os critérios condicionais não permitem testes de aceite conclusivos até que as perguntas correspondentes tenham respostas aprovadas. Esta conclusão é uma avaliação da documentação, não uma aprovação ou reprovação formal de entrega.

Esta revisão relê integralmente `docs/prd.md`, `docs/rtm.md`, `docs/requisitos/regras-negocio.md`, `docs/requisitos/requisitos-nao-funcionais.md`, `docs/requisitos/casos-de-uso.md`, as três personas, `docs/stakeholders.md` e `docs/visao-geral.md`. A perspectiva atribuída a cada persona é **desk review dos documentos**; não representa entrevistas, pesquisa ou validação com pessoas reais.

## 1. Regras explicitamente definidas

### Baseline encontrado

- **REQ-01 / RN-01:** o sistema gerencia salas, professores e materiais e declara reservas sem conflitos de horários. O invariante existe, mas seu predicado operacional não está definido.
- **REQ-02 / RN-02:** o solicitante, professor ou coordenador, consulta disponibilidade e gerencia suas reservas. “Gerenciar” e “suas” não especificam operações nem autorização/propriedade.
- **REQ-03 / RN-03:** o responsável aprova solicitações especiais, valida alocação de docentes e acompanha retirada e devolução de materiais. Não há critérios, sequência nem efeitos definidos.
- **REQ-04 / RN-04:** o administrador gerencia usuários, salas, professores, materiais, bloqueios e períodos de manutenção. Não há operações ou efeitos definidos.
- **RF-01..RF-13** decompõem essas capacidades declaradas; **RF-14** explicita a derivação contextual de concorrência a partir de REQ-01. Os 14 RF receberam prioridade obrigatória pela quantidade solicitada, sem prioridade relativa de negócio.
- **RN-02..RN-04** documentam atribuições de perfil, não gates nem políticas de autorização. As personas reiteram somente os verbos e perfis conhecidos; não introduzem evidência independente sobre necessidades, dificuldades ou fluxos.
- **RNF-01..RNF-05** registram testes automatizados/TDD/BDD, ATAM, rastreabilidade, responsabilidades/segurança/testabilidade/clareza e CI a partir de CTX/POL. Predominam princípios ou expectativas de processo sem medidas operacionais. CT-01..CT-03 registram contexto tecnológico, não decisões arquiteturais.
- Os casos **UC-01..UC-13** representam as capacidades conhecidas sem inventar fluxos; a concorrência de RF-14 é tratada como extensão derivada. A RTM rastreia RF, RN, RNF e restrições/contexto, mas reserva teste, commit e evidência CI sem preenchimento e marca criticidade/risco como não definidos.

### Síntese pelas lentes solicitadas

| Lente | Leitura documental |
|---|---|
| Analista de negócios | A origem REQ-01..REQ-04 está explícita e a decomposição RF é rastreável. O vocabulário de negócio ainda não fecha os conceitos de disponibilidade, conflito, solicitação especial, validação, acompanhamento e gerenciamento. |
| PM | O limite de escopo e a ausência de prioridade relativa são transparentes. Não há base documental para ordenar trade-offs entre capacidades nem ratificar RF-14 como requisito de baseline sem decisão explícita. |
| Engenheiro de qualidade | IDs e vínculos são úteis, mas critérios condicionais não definem oráculos de teste. Não é possível concluir testes de aceite para requisitos dependentes de Q-IDs antes de respostas aprovadas; a RTM corretamente não inventa evidências. |
| Solicitante — desk review | A documentação só permite concluir que consulta disponibilidade e gerencia reservas próprias. Não permite avaliar filtros/resultado da consulta, operações disponíveis, propriedade da reserva, cancelamento ou o que acontece em disputa concorrente. Não houve entrevista com solicitantes reais. |
| Responsável — desk review | Aprovação, validação docente e acompanhamento de materiais estão nomeados, sem critérios, participantes, registros ou resultados. Não houve entrevista com responsáveis reais. |
| Administrador — desk review | Os objetos gerenciados estão identificados, mas operações e efeitos de bloqueio/manutenção em reservas existentes e futuras não. Não houve entrevista com administradores reais. |

## 2. Lacunas que exigem decisão

Severidade abaixo é uma avaliação qualitativa desta revisão, **não** uma classificação já aprovada de criticidade ou risco do produto. “Alta” indica que a lacuna impede definir comportamento central ou um oráculo de aceite; “média” indica escopo ou verificabilidade relevantes, mas sem contradição literal; a justificativa está em cada achado.

| ID | Severidade e justificativa | Achado e impacto | Rastreabilidade |
|---|---|---|---|
| REV-F-01 | **Alta:** a semântica temporal determina disponibilidade e o próprio significado do invariante central; sem ela não há casos-limite nem oráculo reproduzível. | Não estão definidos início/fim, inclusão dos limites, regra para intervalos adjacentes, timezone/calendário, tratamento de mudança de fuso, datas passadas, horizonte futuro ou margem/buffer. | PRD: RF-01, RF-09, RF-14, Q-01; RN-01; UC-01, UC-09. |
| REV-F-02 | **Alta:** cada dimensão pode mudar o conjunto de reservas válidas e o modelo de disponibilidade; a generalização “sem conflitos” não basta para testar esses resultados. | Sobreposição não é definida separadamente para salas, docentes e materiais. Para materiais falta decidir controle por item ou por tipo/quantidade, incluindo como demanda, disponibilidade e contagem se relacionam. | PRD: RF-09, RF-14, Q-02; RN-01; UC-09. |
| REV-F-03 | **Alta:** uma implementação pode aparentar evitar duplicidade sem oferecer resultado observável definido; não se pode verificar qual pedido ou estado deve ser apresentado. | RF-14 declara o cenário concorrente, mas não há resultado para cada requisição, critério de precedência/vencedor, estado final ou resposta comunicada/registrada. | PRD: RF-14, Q-09; RTM: RF-14; extensão de concorrência após UC-13. |
| REV-F-04 | **Alta:** estados e permissões controlam quem pode alterar a ocupação da agenda; sem eles não há teste conclusivo de ciclo de vida nem de acesso. | “Gerenciar próprias reservas” não especifica operações, identidade ou posse; estados/transições e cancelamento (quem, quando e consequências) não estão definidos. As atribuições dos perfis não formam matriz de permissões. | PRD: RF-02, Q-03, Q-04, Q-10, Q-14; RN-02; UC-02. |
| REV-F-05 | **Alta:** uma interpretação incorreta pode tornar uma aprovação obrigatória quando não deveria, ou permitir reserva que dependia dela; os próprios documentos recusam assumir essa relação. | “Solicitação especial” não tem definição, critérios ou resultado. “Recurso restrito” e sua relação com solicitação especial não têm classificação nem autoridade definida; também não se sabe se aprovação influencia/condiciona uma reserva. Critérios de validação docente e efeito do resultado igualmente faltam. | PRD: RF-10, RF-11, Q-05, Q-06, Q-15; RN-03; UC-10, UC-11. |
| REV-F-06 | **Alta:** manutenção e bloqueios podem invalidar reservas confirmadas e alterar disponibilidade; esse efeito afeta comportamento administrativo e a invariante de conflito. | Não se define recurso/intervalo abrangido, se bloqueios impedem novas reservas nem o que ocorre com reservas existentes ou futuras (preservar, revisar, reagendar, cancelar ou outro resultado). | PRD: RF-07, RF-08, Q-08; RN-04; UC-07, UC-08. |
| REV-F-07 | **Média:** as capacidades são rastreáveis, mas não há critério para distinguir registro de eventos, acompanhamento do processo e contabilização de uso/devolução. | Retirada/devolução não define participantes, eventos, quantidade, condição ou contagem de utilização. Rastreabilidade documental por IDs não equivale a auditoria operacional; esta, retenção, relatórios e notificações permanecem sem escopo. | PRD: RF-12, RF-13, Q-07, Q-11, Q-12, Q-13; RN-03; UC-12, UC-13; RNF-03. |
| REV-F-08 | **Alta:** sem critérios independentes das decisões pendentes, testes podem validar interpretações arbitrárias e produzir falsa evidência de aceite. | Os AC dos RF são condicionais. Para RF-01..RF-14, requisitos sem baseline de decisão não têm critério capaz de produzir teste de aceite conclusivo até as respostas aplicáveis serem aprovadas. Os campos de teste/evidência na RTM estão vazios, adequadamente, mas a rastreabilidade ainda não é evidência de cobertura. | PRD: RF-01..RF-14 e Q-01..Q-16; RTM: seções de RF, RN e RNF; UC-01..UC-13. |
| REV-F-09 | **Média:** a distinção é importante para baseline, priorização e governança, mas não constitui comportamento oposto no produto. | RF-14 é uma derivação contextual listada entre RF obrigatórios; “obrigatório” decorre da quantidade solicitada, não de prioridade relativa de negócio. A RTM deixa criticidade/risco não definidos; RN-02..RN-04 são atribuições, não políticas de autorização. Essas categorias precisam permanecer distintas e compreendidas como tal. | PRD: objetivo/escopo, RF-14 e prioridade; RTM: legenda e tabelas; regras de negócio: RN-02..RN-04. |
| REV-F-10 | **Média:** os princípios são identificáveis, mas sem condições verificáveis não sustentam planejamento nem declaração de conformidade. | RNF não especificam escopo, métricas, limiares, evidências ou gates de CI. Também não há baseline para relatórios e notificações; sua ausência é lacuna de escopo, não requisito implícito. | RNF-01..RNF-05, Q-12, Q-13, Q-16; RTM: seção de qualidade/processo. |
| REV-F-11 | **Média:** personas e casos de uso são fiéis às fontes, mas sua concisão limita validação de fluxo e preparação de aceite; não constitui falha de rastreabilidade textual. | UC-01..UC-13 registram ator/objetivo, sem pré/pós-condições, passos ou transições; as personas não contêm pesquisa de dificuldades e explicitamente não as presumem. O material disponível não demonstra validação com usuários. | Casos de uso: UC-01..UC-13; personas Solicitante, Responsável e Administrador; REQ-02..REQ-04. |

## 3. Requisitos conflitantes

**Não foi encontrada contradição literal entre requisitos**, isto é, duas declarações aprovadas que exijam resultados mutuamente incompatíveis para a mesma condição. O que há são tensões de classificação, escopo e maturidade:

- **RF-14 e origem:** RF-14 é uma derivação contextual de REQ-01 para explicitar concorrência e aparece junto aos RF obrigatórios. Isso exige ratificação de escopo/origem, mas não contradiz RF-09 ou RN-01.
- **Prioridade:** todos os RF são “obrigatórios” pela quantidade solicitada. Isso não fornece prioridade relativa de negócio nem permite concluir que todas as capacidades tenham precedência equivalente em planejamento.
- **RTM:** criticidade e risco estão corretamente identificados como não definidos/pendentes. A ausência de classificação é uma lacuna de gestão e priorização, não uma declaração conflitante.
- **RN-02..RN-04:** são atribuições dos perfis e têm ressalvas explícitas de que não são políticas de autorização. Interpretá-las como matriz de acesso criaria uma regra não presente, mas os documentos não apresentam regra oposta.
- **RNFs/processos:** contexto e princípios são registrados sem métricas. A diferença entre intenção e critério operacional é insuficiência de especificação, não oposição entre RNFs.

## 4. Hipóteses ainda não aprovadas

O PRD declara que nenhuma hipótese foi adotada, e a leitura das regras e personas mantém essa posição. Esta revisão não promove candidatos a hipóteses aprovadas.

- **RF-14:** deve ser tratado como derivação/interpretação de escopo a ratificar antes de integrar o baseline, não como requisito independente provado pela fonte. Se ratificado, ainda depende das decisões sobre conflito e concorrência.
- **Prioridade relativa:** não foi fornecida. O rótulo obrigatório para cumprir a quantidade solicitada não determina ordem de negócio, urgência ou valor relativo.
- **Alternativas em Q-01..Q-16:** são perguntas/opções, não defaults. Não se deve inferir intervalo semiaberto, rejeição de disputa, estado pendente, CRUD, aprovação prévia, cancelamento, permissões, auditoria, relatório, notificação ou qualquer outra alternativa sem decisão registrada.
- **Personas:** não há evidência de que necessidades/dificuldades além das frases REQ tenham sido confirmadas com pessoas reais. Os três pontos de vista desta revisão são análise documental, não validação humana.

## 5. Perguntas que precisam ser respondidas

Os IDs Q existentes permanecem inalterados. Os IDs `REV-Q-*` identificam pacotes de perguntas desta revisão e apontam para as decisões já abertas; não criam requisitos.

| ID | Pergunta para decisão | IDs documentais relacionados |
|---|---|---|
| REV-Q-01 | Qual é a convenção de início/fim, limites, adjacência, timezone/calendário e tratamento de horários passados, horizonte futuro e margem/buffer? | Q-01, Q-02, Q-14; RF-01, RF-09, RF-14. |
| REV-Q-02 | O que configura overlap para cada recurso: mesma sala, mesmo docente em recursos distintos e materiais por item ou quantidade? Como demanda/contagem afetam disponibilidade? | Q-02, Q-07; RF-09, RF-11..RF-13. |
| REV-Q-03 | Sob concorrência, qual resultado observável haverá para cada requisição, incluindo eventual vencedora, estado resultante e resposta? | Q-04, Q-09; RF-09, RF-14. |
| REV-Q-04 | Quais estados e transições existem, quais reservas ocupam agenda e quem pode cancelar, em que momento e com quais consequências? | Q-04, Q-14; RF-02, RF-09. |
| REV-Q-05 | Quais operações cada “gerenciar” cobre e como se identifica solicitante e reserva “própria”? Qual matriz de permissões está aprovada para cada perfil? | Q-03, Q-10; RF-02..RF-08; RN-02..RN-04. |
| REV-Q-06 | O que caracteriza solicitação especial e recurso restrito, quem classifica/decide, e se/quando a aprovação condiciona uma reserva? Quais critérios e efeitos se aplicam à validação docente? | Q-05, Q-06, Q-15; RF-10, RF-11. |
| REV-Q-07 | Como bloqueios/manutenção delimitam recurso e período; como afetam novas reservas e reservas futuras/existentes, inclusive quando já confirmadas? | Q-08; RF-07..RF-09. |
| REV-Q-08 | O que significa acompanhar retirada/devolução, quem registra ou participa, se há contagem de utilização e se há auditoria operacional distinta da rastreabilidade documental? | Q-07, Q-11; RF-12, RF-13, RNF-03. |
| REV-Q-09 | São necessários relatórios e notificações? Para cada um, qual público, evento/conteúdo, período ou canal faz parte do escopo? | Q-12, Q-13. |
| REV-Q-10 | Quais evidências, métricas ou critérios qualitativos verificam cada RNF e processo (TDD/BDD, ATAM, rastreabilidade e CI), e qual criticidade/risco se atribui aos requisitos? | Q-16; RNF-01..RNF-05; campos de criticidade/risco na RTM. |
| REV-Q-11 | RF-14 será ratificado como requisito do baseline ou mantido somente como derivação contextual? Como a prioridade obrigatória por solicitação será distinguida da prioridade relativa de negócio? | RF-14; prioridade em RF-01..RF-14; REQ-01. |

## 6. Recomendações justificadas

As recomendações abaixo são **propostas para decisão**, não requisitos, regras de negócio, políticas existentes nem escolhas de arquitetura. Em cada ponto, as opções são neutras; a decisão deve ser registrada pelos responsáveis pelo produto antes de ser incorporada ao baseline.

1. **Fechar primeiro o vocabulário temporal e de conflito.** Decidir e documentar a convenção de intervalo, fuso/calendário e datas; em separado, critérios por sala, docente e material, além de adjacência e eventual buffer. Podem ser comparadas convenções alternativas de limites e modelos por item/quantidade sem presumir uma delas. Isso cria casos de borda reproduzíveis, melhora a rastreabilidade entre RF-09/RN-01 e os testes e reduz o risco de soluções incompatíveis com o domínio.

2. **Definir o resultado observável de disputa concorrente.** Escolher, dentre resultados possíveis ou outro explicitamente descrito, o resultado para cada pedido e como será percebido/registrado, sem impor uma estratégia técnica ou prioridade de vencedor. Sem esse contrato, não há critério de aceite para RF-14 nem evidência confiável de que a invariante foi preservada.

3. **Aprovar um modelo de ciclo de vida e permissões como artefatos separados.** Uma opção é explicitar estados/transições; outra é registrar apenas decisões e efeitos definidos sem criar estados adicionais. Em qualquer caso, especificar ocupação da agenda, cancelamento (ator, momento, efeito) e matriz de permissões/identidade, inclusive o significado de “próprias”. Separar atribuição (RN) de autorização evita transformar perfis em política por inferência e possibilita testes positivos e negativos rastreáveis.

4. **Resolver a fronteira entre solicitação especial e recurso restrito.** Decidir se são conceitos independentes, relacionados ou equivalentes, se haverá classificação de recurso restrito, quem tem autoridade e se aprovação apenas registra decisão ou condiciona uma reserva. Não escolher uma relação implícita reduz risco de bloqueio indevido de reservas e permite critérios objetivos para RF-10/RF-11.

5. **Especificar os efeitos de bloqueio e manutenção sobre a agenda.** Comparar alternativas como afetar apenas novas reservas, preservar as existentes ou submetê-las a uma decisão explícita de tratamento. Registrar escopo temporal/recurso e resultado esperado sem presumir notificação ou cancelamento. Isso torna RF-07/RF-08 verificáveis e previne divergência entre disponibilidade consultada e reservas já registradas.

6. **Delimitar circulação, contagem e auditoria operacional.** Decidir que eventos/dados de retirada e devolução precisam ser acompanhados e se quantidade/uso deve ser contabilizada; tratar separadamente eventual trilha operacional, conteúdo e retenção. A rastreabilidade de requisitos por IDs não deve ser usada como substituto de auditoria de eventos. Essa distinção sustenta testes de RF-12/RF-13 e reduz ambiguidades de inventário e evidência.

7. **Transformar cada resposta aprovada em critério de aceite verificável e atualizar a RTM.** Para cada RF dependente de Q, registrar resposta aprovada, cenários/resultado observável e vínculo com UC/RN/RNF; preencher campos de teste e CI somente quando houver evidência real. Até lá, manter os AC explicitamente condicionais e não declarar cobertura conclusiva. Isso preserva rastreabilidade sem fabricar testes ou aprovações.

8. **Ratificar escopo e priorização antes do planejamento de implementação.** Decidir se RF-14 pertence ao baseline, distinguir obrigatoriedade da solicitação de prioridade relativa e classificar criticidade/risco pela governança do produto, não por inferência desta revisão. A clareza permite sequenciar trabalho e testes sem reinterpretar a origem dos requisitos.

9. **Definir verificabilidade dos RNFs e confirmar necessidades de relatórios/notificações.** Para cada RNF, escolher critérios quantitativos, evidência qualitativa documentada ou não aplicabilidade; para relatórios e notificações, explicitar inclusão ou exclusão de escopo e, se incluídos, público e conteúdo/eventos. Opções explícitas facilitam rastreabilidade e planejamento de CI sem inventar limiares nem transformar lacunas em funcionalidades obrigatórias.

10. **Validar as perspectivas das personas com stakeholders reais antes de tratar necessidades como confirmadas.** A revisão documental sustenta apenas os papéis e verbos publicados. Entrevistas ou validação apropriada podem confirmar dificuldades e fluxos; registrar resultados como fonte aprovada antes de ampliar requisitos evita atribuir às personas necessidades que não foram observadas.