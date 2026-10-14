# Arquitetura proposta — Organização de Recursos

**Versão:** proposta 0.2, 2026-10-14
**Status:** para validação; não aprovada para implementação.
**Escopo desta entrega:** arquitetura lógica e estratégia de validação. Nenhum código, aplicação Spring Boot, banco ou pipeline foi criado/configurado.

## 1. Fontes e limites

Fontes de produto lidas integralmente: [PRD](prd.md), [RTM](rtm.md), [stakeholders](stakeholders.md), [visão geral](visao-geral.md), três [personas](personas/solicitante.md), [casos de uso](requisitos/casos-de-uso.md), [regras de negócio](requisitos/regras-negocio.md), [requisitos não funcionais](requisitos/requisitos-nao-funcionais.md) e [revisão](requisitos/revisao.md). O `README.md` contém somente o título.

Não foi encontrado ADR ou decisão arquitetural aprovada do produto antes desta revisão. Os arquivos de decisão encontrados pertencem ao framework AIOX e são modelos/ferramentas, não decisões deste sistema. Há diagramas C4 e de sequência em `docs/diagramas/`; não há código da aplicação, `.devcontainer/` nem workflow em `.github/workflows/`. Assim, o conteúdo abaixo é desenho-alvo e recomendação para validação, não descrição de implementação existente.

Os requisitos de produto têm status de rascunho; os AC são condicionais. Em especial, Q-01 a Q-10 deixam por decidir a semântica temporal, os conflitos por recurso, o ciclo de vida, a aprovação, a concorrência e permissões; Q-11 a Q-16 cobrem auditoria, relatórios, notificações e verificabilidade. Não transformamos essas lacunas em regras. Diagramas de fluxo ilustram um desenho técnico possível e identificam pontos condicionados.

## 2. Contexto, atores e drivers

O objetivo documentado é gerenciar salas, professores e materiais com reservas sem conflitos de horários. Os perfis declarados são solicitante (professor ou coordenador), responsável e administrador. Suas atribuições não definem por si só autenticação, autorização ou sequência de interação.

| Driver | Origem | Implicação arquitetural proposta |
|---|---|---|
| Preservar reservas sem conflitos | REQ-01, RF-09, RN-01; cenário concorrente derivado em RF-14 | Uma única fronteira transacional de escrita e uma defesa final de integridade no banco, depois de aprovada a semântica de conflito. |
| Consultar disponibilidade e gerenciar reservas próprias | REQ-02, RF-01/RF-02; Q-01 a Q-04 e Q-10 | Casos de uso separados de consulta e escrita; identidade e propriedade ficam atrás de uma política explícita ainda pendente. |
| Aprovar solicitações especiais e validar alocação docente | REQ-03, RF-10/RF-11; Q-05/Q-06/Q-15 | Componentes de aplicação separados; não fazer a aprovação pré-condição nem alterar ocupação sem decisão de produto. |
| Acompanhar retirada e devolução de materiais | REQ-03, RF-12/RF-13; Q-02/Q-07 | Separar operações de materiais/reservas; controle por item, quantidade e eventos depende de decisão. |
| Gerenciar recursos, usuários, bloqueios e manutenção | REQ-04, RF-03 a RF-08; Q-03/Q-08 | Módulos de cadastro e agenda com validação no domínio; efeito de bloqueios/manutenção sobre reservas existente/futuras permanece configurável por política aprovada, não por inferência. |
| Segurança, testabilidade e clareza | POL-01, RNF-04 | Camadas explícitas, autorização centralizada e portas de persistência/integradores; medidas de conformidade ainda não especificadas. |
| Testes automatizados, TDD/BDD, ATAM, rastreabilidade e CI | CTX-02, POL-02, RNF-01 a RNF-03/RNF-05; Q-16 | Interfaces testáveis e RTM ligando requisito, decisão e teste; não se inventam limiares de cobertura ou qualidade. |
| Stack do projeto | CT-01 Java 21, CT-02 Spring Boot 3.x, CT-03 Maven | Restrições contextuais adotadas no desenho; versão menor/dependências ainda não definidas. |

### Atributos de qualidade prioritários

1. **Correção e integridade da agenda:** prioridade arquitetural mais alta, derivada de REQ-01/RF-09/RF-14; predicado testável continua pendente de Q-01/Q-02/Q-04.
2. **Testabilidade e rastreabilidade:** explicitamente citadas em CTX-02/POL-02; faltam critérios operacionais em Q-16.
3. **Segurança e clareza de responsabilidades:** princípios explícitos em POL-01; modelo de identidade e permissões pendente de Q-10.
4. **Manutenibilidade:** motivação para módulos e interfaces, recomendação técnica e não requisito de produto mensurado.

Disponibilidade operacional, latência, volume, escalabilidade, recuperação, privacidade, acessibilidade e retenção não têm metas aprovadas. Não se afirma conformidade ou capacidade para esses atributos.

## 3. Arquitetura candidata

### Recomendação para validação: monólito modular em camadas

Uma aplicação única Spring Boot 3.x / Java 21 / Maven com módulos de domínio e casos de uso separados, API HTTP como adaptador de entrada e PostgreSQL como candidato a persistência. Persistência e aplicação compartilham transações locais, evitando consistência distribuída na invariante central. O desenho não exige microserviços, broker, cache ou serviço externo.

| Camada / módulo | Responsabilidade e limite |
|---|---|
| API / controllers | Adaptar HTTP para comandos/consultas; validar formato; autenticação/autorização na borda; mapear erros de aplicação para respostas. Não conter regra de conflito. A forma de cliente/API é proposta, não requisito de interface aprovado. |
| Aplicação / casos de uso | Orquestrar consultar disponibilidade, criar/gerenciar reserva, aprovar solicitação, validar alocação, acompanhar materiais, gerenciar cadastros e manutenção. Delimitar transação; chamar portas de persistência e de integração. |
| Domínio | Modelar conceitos e regras aprovadas: intervalos, conflitos, estados, autorização de transição e alocação. A política deve ser completada após as Qs; não codificar regras ainda abertas. |
| Portas e adaptadores de repositório | Interfaces orientadas a casos de uso; adaptadores PostgreSQL executam consultas, gravações e constraints. O domínio/aplicação não depende de detalhes SQL/JPA. |
| Persistência | PostgreSQL candidato para transações relacionais, chaves estrangeiras e constraints de integridade. Uma camada de persistência compartilhada; módulos não acessam tabelas alheias diretamente. Migrações versionadas são necessárias, ferramenta não escolhida. |
| Segurança | Adaptador de autenticação e política de autorização explícita por operação/perfil/identidade. Spring Security é candidato natural no stack, não decisão final; provedor, sessão/token e matriz de acesso estão pendentes de Q-10. |
| Auditoria | Porta para eventos de auditoria, quando escopo/conteúdo/retenção forem aprovados em Q-11. Se exigida para uma escrita, gravar na mesma transação do fato auditado. Não confundir com RTM/telemetria. |
| Notificações | Porta/adaptador opcional somente se Q-13 confirmar necessidade, eventos, destinatários e canal. Não introduzir fila ou provedor agora. |
| Relatórios | Consultas/projeções isoladas dos fluxos transacionais, somente se Q-12 definir público e conteúdo. Evitar consultas analíticas pesadas no caminho de reserva. |

Dependências seguem de fora para dentro: controller → caso de uso → domínio/portas; adaptadores implementam portas. O domínio não importa Spring MVC, JPA ou cliente externo. Módulos candidatos: `reservas`, `recursos`, `aprovacoes`, `materiais`, `manutencao`, `seguranca`, `auditoria`, `notificacoes` e `relatorios`. São limites lógicos; separar em deploys independentes não é recomendado nem necessário com o escopo atual.

### Alternativas avaliadas

| Alternativa | Benefícios | Custos/riscos | Avaliação para este projeto |
|---|---|---|---|
| A. Monólito modular em camadas (recomendação provisória) | Uma transação local para reserva, alocação, bloqueio e auditoria; implantação e teste mais simples; limites internos evolutivos. | Exige disciplina para impedir acesso cruzado a tabelas e dependências; escalar partes separadamente não é possível. | Melhor ponto inicial dado escopo conhecido pequeno e forte invariante transacional. Revisar quando volume/limites operacionais forem requisitos. |
| B. Monólito em camadas sem módulos de domínio explícitos | Menos estrutura inicial e familiaridade Spring. | Regras tendem a concentrar-se em serviços/repositórios, acoplamento e testes de integração aumentam; invariantes ficam dispersas. | Viável, mas menos adequado à rastreabilidade e à complexidade de conflito/manutenção. |
| C. Serviços distribuídos por recurso ou capacidade | Escala e implantação independentes por domínio quando houver necessidade comprovada. | Reserva envolvendo sala, docente e materiais cruza serviços; locks/consistência distribuídos, compensações e disponibilidade parcial dificultam a invariante; mais infraestrutura. | Não recomendado sem requisitos de escala/autonomia; contradiz a simplicidade e o risco de consistência hoje prioritários. |

**Persistência:** PostgreSQL é candidato, não decisão registrada. Sua constraint de exclusão GiST pode rejeitar sobreposição de intervalos no banco, além da validação de aplicação. Um banco relacional sem constraint equivalente ainda permite transações, mas exigiria maior dependência de locking/isolamento e testes mais rigorosos. Banco em memória não é substituto para validar concorrência real. Não há razão documental para NoSQL.

## 4. Integridade, concorrência e erros

### Validação e transação

Separar validação amigável de disponibilidade (consulta usada para feedback) da garantia final no instante da gravação. Entre consulta e gravação existe uma janela de corrida; apenas verificar em Java não garante RF-09/RF-14. O caso de uso de escrita deve transacionar a gravação da reserva e de todas as alocações/ocupações necessárias. A decisão sobre quais estados ocupam agenda e quais recursos conflitam deve anteceder qualquer constraint (Q-01/Q-02/Q-04).

| Estratégia | Proteção e vantagens | Limitações / uso indicado |
|---|---|---|
| Constraint de exclusão PostgreSQL | Restrição atômica no banco sobre chave do recurso e intervalo; protege escritores concorrentes, inclusive em processos distintos. Com range temporal e igualdade composta, GiST; igualdade para tipos/IDs pode demandar `btree_gist`. | Vincula solução ao PostgreSQL; depende da semântica de intervalo, recurso e estados; implantação pode exigir extensão/permissão. Materiais por quantidade não se reduzem a uma simples exclusão. É a opção preferida para recursos discretos se domínio e forma de dados permitirem. |
| Lock pessimista | `SELECT ... FOR UPDATE` sobre registro-pai do recurso antes de consultar e gravar; serializa alterações por recurso. Para múltiplos recursos, adquirir locks em ordem estável para reduzir deadlocks. Útil para estoque/quantidade e políticas mais elaboradas. | Lock precisa existir mesmo sem reserva concorrente (registro-pai); reduz throughput, pode bloquear consultas/escritas e exige timeout/retry e tratamento de deadlock. A lógica deve ser seguida por todo escritor. |
| Isolamento serializável | O banco detecta anomalias de serialização; aborta uma das transações em conflito. Pode proteger consulta + gravação sem lock explícito por recurso. | Abortos/retries são parte normal do fluxo; custo e comportamento precisam ser medidos. Não substitui definição do resultado de negócio Q-09. |
| Lock otimista/versão | Controle de versão detecta alteração concorrente do mesmo registro; adequado para editar uma reserva existente. | Não evita, por si só, duas novas linhas que conflitam nem phantom reads; insuficiente isoladamente para dupla reserva. |

**Direção recomendada, ainda não aprovada:** constraint PostgreSQL como última linha de defesa para ocupação exclusiva por recurso discreto, acompanhada de pré-validação no domínio. Para quantidade de materiais, manutenção ou decisões complexas, avaliar locking por recurso e/ou serializável. Só definir estratégia após Q-01/Q-02/Q-04/Q-08/Q-09 e protótipo de migração/teste; evitar afirmar que a constraint cobre todo o domínio.

### Bloqueios e estados

Se bloqueios e reservas compartilharem uma representação de ocupação, a mesma constraint pode impedir sobreposição de novos registros. Isso não define o que fazer ao criar manutenção sobre reserva já existente: rejeitar bloqueio, preservar, marcar para análise ou outra política são alternativas de Q-08. Não remover/cancelar reservas automaticamente sem decisão explícita.

Estados devem ser definidos como máquina de transições no domínio, com comandos que validem origem/destino e ator permitido. Uma constraint de banco pode reforçar valores válidos e unicidade de decisão, mas não substitui autorização nem a política da transição. Bloqueio otimista ou lock de linha protege aprovação concorrente da mesma solicitação. Estados, terminalidade, idempotência e efeito na agenda permanecem em Q-04/Q-05/Q-09.

### Mapeamento HTTP proposto

Uma violação de disponibilidade/constraint pode ser mapeada para **409 Conflict**; o significado e resposta por requisição precisam ser decididos em Q-09. Dados inválidos podem retornar **400** ou **422** conforme contrato a aprovar; autenticação/autorização dependem de Q-10. Não expor SQL, stack trace nem nomes internos de constraint. Resposta de conflito deve ser estável e semântica, mas não inventar qual pedido vence ou seu estado. Erro inesperado continua distinto de conflito de domínio.

### Integridade de auditoria

Se Q-11 exigir auditoria, persistir evento e alteração de negócio na mesma transação para evitar fato sem trilha ou trilha de operação revertida. Uma role de banco somente de inserção para eventos reduz alteração acidental; não protege contra administrador de banco privilegiado. Hash encadeado/armazenamento externo imutável só se houver ameaça e requisito de não repúdio definidos. Retenção, conteúdo sensível, acesso e política de correção aguardam Q-11.

## 5. Testabilidade e qualidade

| Ferramenta solicitada | Uso proposto e evidência |
|---|---|
| JUnit 5 | Testes unitários de domínio e casos de uso; portas fake para testar sem Spring/DB; cada regra ligada a RF/RN/Q aprovado. |
| Testes parametrizados | Matrizes de limites de intervalo, adjacência, recursos, estados e permissões após Q-01/Q-02/Q-04/Q-10; casos ainda não decididos não viram expectativa. |
| Testcontainers + PostgreSQL | Testar constraints, migrations, rollback, locks/isolation e corrida concorrente contra versão real compatível; nunca substituir essa validação por H2/in-memory. |
| WireMock | Simular somente integrações HTTP externas que forem aprovadas, por exemplo um provedor de notificação se Q-13 exigir. Não há integração externa declarada hoje. |
| MockMvc | Testar controllers, validação de contrato HTTP, autorização e mapeamento de conflitos/erros sem iniciar servidor real. |
| Cucumber-JVM | Cenários legíveis de aceitação derivados de casos de uso e critérios aprovados; manter exemplos e IDs RF/RN nos cenários. Não automatizar lacunas como regras. |
| Testes de concorrência | Dois ou mais clientes/conexões independentes, sincronização por barreira, submissões com recurso/intervalo conflitantes; verificar invariante final e resultado de cada chamada após Q-09. Repetir e testar commit/rollback, deadlock/retry e manutenção. |
| JMeter | Teste de carga do caminho de consulta/reserva e contenção após Q-16 fixar workload, volume, concorrência e limiar. Sem SLO, teste mede/estabelece baseline e não pode ser declarado aprovado por um threshold inventado. |
| JaCoCo e SonarCloud | JaCoCo coleta cobertura; SonarCloud agrega análise estática/qualidade se configurado. Definir exclusões e limiares em Q-16; cobertura não prova correção da constraint. |
| GitHub Actions | Pipeline Maven candidato: compilação/testes unitários; integração com serviço PostgreSQL/Testcontainers; análise/cobertura; opcional JMeter fora do gate rápido. Workflow não existe no repositório e não foi criado nesta tarefa. Secrets e política de branch necessitam configuração/decisão. |

Separação de testes: domínio sem infraestrutura; integração para adaptadores e banco; contrato/API com MockMvc; BDD para fluxos aprovados; sistema/concorrência e desempenho com ambiente realista. TDD/BDD e ATAM estão no contexto, mas escopo/critério e gates dependem de Q-16. RTM deve referenciar IDs de teste e evidência real quando existirem.

## 6. Matriz de rastreabilidade proposta

Drivers e IDs abaixo vêm de PRD/RTM; `D-*` são chaves desta proposta, sem criar requisito. Decisões são recomendações pendentes de validação, não aprovações.

| Requisito | Driver / origem | Decisão proposta (pendente) | Componente | Estratégia de validação |
|---|---|---|---|---|
| RF-01 | Consulta de disponibilidade; REQ-02, Q-01/Q-02 | Caso de consulta separado de escrita; disponibilidade segue predicado aprovado | API, Consulta de Disponibilidade, Reserva/portas | JUnit parametrizado + MockMvc + integração PostgreSQL |
| RF-02 | Reservas próprias; REQ-02, Q-03/Q-04/Q-10 | Caso de uso de gestão com identidade/propriedade autorizadas por matriz ainda a definir | API, Gestão de Reservas, Segurança | JUnit + MockMvc (permitido/negado) + Cucumber após definir operações |
| RF-03 | Gestão de usuários; REQ-04, Q-03/Q-10 | Módulo de usuários atrás de caso de uso/política, operações a definir | Cadastros, Segurança | JUnit + MockMvc e integração de persistência |
| RF-04 | Gestão de salas; REQ-04, Q-03 | Módulo de recursos; preservar integridade referencial com alocações | Recursos, Persistência | JUnit + integração PostgreSQL |
| RF-05 | Gestão de professores; REQ-04, Q-03/Q-06 | Cadastro separado de validação de alocação | Recursos, Alocação Docente | JUnit + cenários de aceite aprovados |
| RF-06 | Gestão de materiais; REQ-04, Q-02/Q-03/Q-07 | Separar cadastro de disponibilidade/retirada; decidir item versus quantidade | Materiais, Reservas | JUnit parametrizado + PostgreSQL/Testcontainers |
| RF-07 | Gestão de bloqueios; REQ-04, Q-03/Q-08 | Representar bloqueio como escrita de agenda sob política aprovada | Manutenção, Agenda | JUnit + integração/concorrência PostgreSQL |
| RF-08 | Períodos de manutenção; REQ-04, Q-01/Q-03/Q-08 | Validar intervalo e impacto no mesmo limite transacional da agenda | Manutenção, Agenda | JUnit parametrizado + Testcontainers |
| RF-09 / RN-01 | Invariante sem conflitos; REQ-01, Q-01/Q-02/Q-04 | Pré-validar no domínio e reforçar no banco quando semântica/modelo permitirem | Domínio de Reserva, Persistência | Parametrizados + constraint real PostgreSQL |
| RF-10 | Aprovação de solicitações especiais; REQ-03, Q-04/Q-05/Q-15 | Caso de uso de decisão separado; não condicionar reserva por inferência | Aprovações, Segurança, Auditoria condicional | JUnit transições + MockMvc + Cucumber aprovado |
| RF-11 | Validação docente; REQ-03, Q-06 | Regra/resultado isolados da aprovação de solicitação | Alocação Docente, Domínio | JUnit parametrizado + Cucumber aprovado |
| RF-12 | Acompanhar retirada; REQ-03, Q-02/Q-07/Q-11 | Operação de material; registro de eventos condicionado à definição | Materiais, Auditoria condicional | JUnit + integração PostgreSQL |
| RF-13 | Acompanhar devolução; REQ-03, Q-02/Q-07/Q-11 | Operação distinta de retirada; quantidade e estado aguardam decisão | Materiais, Auditoria condicional | JUnit + integração/Cucumber aprovado |
| RF-14 | Concorrência derivada de REQ-01, Q-01/Q-02/Q-04/Q-09 | Defesa atômica no banco; resultado por pedido pendente de decisão | Caso Criar Reserva, Repositório PostgreSQL | Teste concorrente multi-conexão + verificar resultado HTTP decidido |
| RNF-01 | Testes/TDD/BDD; CTX-02, Q-16 | Pirâmide unitário/integração/aceite; sem porcentagem inventada | Maven/CI | JUnit, Testcontainers, Cucumber; gate a definir |
| RNF-02 | ATAM; CTX-02, Q-16 | Avaliar cenários de qualidade e trade-offs antes de aprovação arquitetural | Arquitetura / registro de decisões | Revisão ATAM documentada; critério pendente |
| RNF-03 | IDs rastreáveis; CTX-02/POL-02 | IDs RF/RN/Q vinculados a decisão e teste na RTM | Documentação/RTM | Revisão automatizável ou checklist; completude pendente |
| RNF-04 | Segurança, testabilidade, clareza; POL-01/Q-10/Q-16 | Fronteiras de camada e política de autorização explícita | Segurança, API, módulos | Testes de autorização + revisão; critérios pendentes |
| RNF-05 | CI; CTX-02/Q-16 | Maven + Actions candidatos com gates unitário/integração/análise | GitHub Actions (não existente) | Execuções verdes e artefatos registrados após configurar |
| CT-01/02/03 | Java 21, Spring Boot 3.x, Maven; CTX-01 | Respeitar stack declarada; fixar versões menores em decisão posterior | Build e aplicação | Build/teste Maven em JDK 21; workflow ainda ausente |

## 7. Riscos, conflitos e pendências

| Risco/lacuna | Efeito | Ação antes de implementação |
|---|---|---|
| Q-01/Q-02 não definem intervalo e conflito por tipo de recurso | Constraint e disponibilidade podem codificar predicado errado | Aprovar semântica temporal, adjacência/margem, docentes e materiais. |
| Q-04/Q-05/Q-09 não definem estados, aprovação e disputa | Não há resultado observável; uma API/constraint pode contrariar o fluxo desejado | Ratificar estados/transições, o que ocupa agenda e resultado por requisição concorrente. |
| Q-08 não define impacto de manutenção | Bloqueio pode conflitar com reservas existentes ou deixá-las inconsistente | Definir política para novas e existentes antes de ativar bloqueio. |
| Q-10 não define autenticação/permissões | Exposição/negação indevida e testes de segurança inconclusivos | Aprovar identidade, propriedade e matriz por operação. |
| Q-11 a Q-13 deixam auditoria, relatórios e notificações em aberto | Implementar integrações/retention sem necessidade, ou perder evidência esperada | Decidir inclusão, conteúdo, público, canal e retenção. |
| RNF não têm SLOs nem gates (Q-16) | JMeter, JaCoCo/SonarCloud e CI não têm critério de aprovação | Definir evidências e limiares antes de declarar conformidade. |
| Exclusion constraint depende de PostgreSQL e modelo | Extensão/constraints parciais não cobrem automaticamente quantidade ou todos estados | Validar versão/extensões e prototipar com Testcontainers antes da decisão final. |

Não foi identificada contradição literal entre os requisitos. A revisão aponta maturidade insuficiente para baseline de implementação; RF-14 é derivação contextual a ratificar, e “obrigatório” nos RF não representa prioridade relativa. Não há dados de escala, desempenho, disponibilidade ou volume para justificar microserviços ou metas.

### Decisões necessárias para aprovação

1. PO/stakeholders: fechar Q-01 a Q-10 e ratificar RF-14 como escopo.
2. Arquiteto: validar módulo/camadas, PostgreSQL e a estratégia híbrida de concorrência em prova técnica; confirmar versões/extensões.
3. PO/QA/equipe: decidir Q-11 a Q-16, critérios verificáveis e gates.
4. Usuário/stakeholders: aprovar a proposta e registrar decisão/ADR antes da implementação.

Até esses passos, a arquitetura é uma recomendação técnica documentada, não autorização para implementar regras abertas.

## 8. Registro de decisões

Os ADRs abaixo registram recomendações e dependências para validação. **Nenhum está aprovado** por esta revisão. “Proposto” indica uma direção técnica recomendada que ainda requer ratificação; “Pendente” indica que uma decisão de produto ou validação técnica é pré-condição para fechar o registro.

| ADR | Tema | Status | Bloqueio/condição |
|---|---|---|---|
| [ADR-001](adr/ADR-001-monolito-modular-em-camadas.md) | Monólito modular em camadas | Proposto | Ratificação arquitetural |
| [ADR-002](adr/ADR-002-postgresql-como-persistencia.md) | PostgreSQL | Proposto | Ratificação e prova de integridade |
| [ADR-003](adr/ADR-003-protecao-contra-dupla-reserva.md) | Concorrência e dupla reserva | Pendente | Q-01/Q-02/Q-04/Q-08/Q-09 e experimento PostgreSQL |
| [ADR-004](adr/ADR-004-fronteiras-transacionais.md) | Transações | Proposto | Ratificação; regras de domínio aprovadas |
| [ADR-005](adr/ADR-005-autenticacao-e-autorizacao.md) | Autenticação/autorização | Pendente | Q-10 e revisão de segurança |
| [ADR-006](adr/ADR-006-ciclo-de-vida-e-estados.md) | Estados e transições | Pendente | Q-04/Q-05/Q-08/Q-14/Q-15 |
| [ADR-007](adr/ADR-007-auditoria-operacional.md) | Auditoria operacional | Pendente | Q-11 |
| [ADR-008](adr/ADR-008-notificacoes-e-integracoes.md) | Notificações e integrações | Pendente | Q-13; nenhuma integração foi declarada |
| [ADR-009](adr/ADR-009-erros-http.md) | Erros HTTP | Proposto | Contrato HTTP e Q-09/Q-10 |
| [ADR-010](adr/ADR-010-estrategia-de-testes.md) | Testes automatizados | Proposto | Q-16 para gates/limiares |
| [ADR-011](adr/ADR-011-codespaces-devcontainer.md) | Codespaces/devcontainer | Proposto | Validar configuração de ambiente e segurança |
| [ADR-012](adr/ADR-012-migracoes-de-banco.md) | Migrações de banco | Pendente | Escolher ferramenta após modelo de dados |

## 9. Matriz de rastreabilidade arquitetural

`D-*` referencia os drivers da avaliação ATAM em [ciclo-00](avaliacao-arquitetural/ciclo-00.md#2-drivers-arquiteturais). Os testes são **planejados**, não existentes nem executados. Uma linha com ADR pendente não significa cobertura implementada.

| Requisito | Driver / origem | ADR relacionado | Componente proposto | Teste/experimento planejado |
|---|---|---|---|---|
| RF-01 | D-02, REQ-02, Q-01/Q-02 | ADR-001, ADR-002, ADR-003 | Consulta de disponibilidade, domínio, adaptador PostgreSQL | Testes parametrizados do predicado aprovado; integração PostgreSQL; contrato HTTP |
| RF-02 | D-02, REQ-02, Q-03/Q-04/Q-10 | ADR-001, ADR-004, ADR-005, ADR-006 | Gestão de reservas e segurança | Testes de casos de uso, autorização por objeto e fluxos aprovados |
| RF-03 | D-04, REQ-04, Q-03/Q-10 | ADR-001, ADR-005 | Cadastro de usuários | Teste de operações aprovadas, autorização e persistência |
| RF-04 | D-04, REQ-04, Q-03 | ADR-001, ADR-002, ADR-012 | Cadastro de salas / adaptador de persistência | Testes de domínio e integração com migrações PostgreSQL |
| RF-05 | D-04, REQ-04, Q-03/Q-06 | ADR-001, ADR-006 | Cadastro de professores e alocação docente | Testes parametrizados após critérios aprovados |
| RF-06 | D-04, REQ-04, Q-02/Q-03/Q-07 | ADR-001, ADR-002, ADR-003, ADR-006 | Cadastro e operações de materiais | Testes por item/quantidade conforme decisão; integração PostgreSQL |
| RF-07 | D-04, REQ-04, Q-03/Q-08 | ADR-003, ADR-004, ADR-006 | Bloqueios / agenda | Testes de domínio, transação e corrida bloqueio-reserva |
| RF-08 | D-04, REQ-04, Q-01/Q-03/Q-08 | ADR-003, ADR-004, ADR-006 | Manutenção / agenda | Testes temporais e concorrentes com rollback PostgreSQL |
| RF-09 | D-01, REQ-01, RN-01, Q-01/Q-02/Q-04 | ADR-002, ADR-003, ADR-004, ADR-006 | Domínio de reserva, agenda e PostgreSQL | Testes de limites/conflitos aprovados; constraints/integração no banco |
| RF-10 | D-03, REQ-03, Q-04/Q-05/Q-10/Q-15 | ADR-005, ADR-006, ADR-007 | Caso de uso de aprovação e segurança; auditoria condicional | Testes de transição e autorização; teste de atomicidade se Q-11 exigir |
| RF-11 | D-03, REQ-03, Q-02/Q-06 | ADR-001, ADR-003, ADR-006 | Validação de alocação docente | Testes de domínio/concorrência conforme critérios aprovados |
| RF-12 | D-03, REQ-03, Q-07/Q-11 | ADR-006, ADR-007 | Acompanhamento de retirada de materiais | Testes de fluxo e persistência após definir dados/eventos |
| RF-13 | D-03, REQ-03, Q-07/Q-11 | ADR-006, ADR-007 | Acompanhamento de devolução de materiais | Testes de fluxo e persistência após definir dados/eventos |
| RF-14 | D-01, derivação de REQ-01, Q-01/Q-02/Q-04/Q-09 | ADR-002, ADR-003, ADR-004, ADR-009 | Escrita de reserva / adaptador PostgreSQL / API | Teste concorrente multi-conexão; verificar estado final e resposta definida |
| RN-01 | D-01, REQ-01, Q-01/Q-02/Q-04 | ADR-002, ADR-003, ADR-006 | Domínio de reserva e integridade PostgreSQL | Propriedades temporais e constraint real sob concorrência |
| RN-02 | D-02, REQ-02, Q-03/Q-10 | ADR-005, ADR-006 | Gestão de reservas / autorização | Testes de propriedade e operações depois de aprovadas |
| RN-03 | D-03, REQ-03, Q-05/Q-06/Q-07/Q-15 | ADR-005, ADR-006 | Aprovações, alocação e materiais | Testes de critérios/transições aprovados |
| RN-04 | D-04, REQ-04, Q-03/Q-08 | ADR-005, ADR-006 | Administração de recursos, bloqueios e manutenção | Testes de autorização, integridade e impacto de manutenção |
| RNF-01 | D-06, CTX-02, Q-16 | ADR-010 | Testes de domínio, API e integração | JUnit; integração com Testcontainers; gates só após Q-16 |
| RNF-02 | D-06, CTX-02, Q-16 | ADR-001 a ADR-012 | Avaliação arquitetural / ADRs | Reavaliação ATAM com evidências após decisões e experimentos |
| RNF-03 | D-06, CTX-02/POL-02, Q-16 | ADR-010 e todos os ADRs | RTM, ADRs e documentação | Auditoria amostral requisito → ADR → teste → evidência; sem declarar evidência inexistente |
| RNF-04 | D-05, POL-01, Q-10/Q-16 | ADR-001, ADR-005, ADR-009, ADR-010 | Camadas, segurança, API e testes | Revisão de ameaças e testes negativos; critérios qualitativos/limiares pendentes |
| RNF-05 | D-06, CTX-02, Q-16 | ADR-010 | CI Maven | Pipeline inicial com build/testes; aprovação dos gates e evidências pendente |
| CT-01: Java 21 | D-07, CTX-01 | ADR-001, ADR-010 | Build da aplicação | Build e testes com JDK 21 |
| CT-02: Spring Boot 3.x | D-07, CTX-01 | ADR-001, ADR-005, ADR-009 | Aplicação/API/segurança | Testes de contexto, API e compatibilidade na versão menor escolhida |
| CT-03: Maven | D-07, CTX-01 | ADR-010, ADR-011 | Build e ambiente de desenvolvimento | Build reproduzível local/Codespaces e execução de testes |

### Cobertura ausente ou condicional

- **Sem cobertura funcional baseline:** RF-01..RF-14 e RN-01..RN-04 permanecem condicionados às Qs relacionadas; as propostas técnicas não fecham comportamento de produto.
- **Escopo ainda não aprovado:** relatórios (Q-12) e notificações/integrações (Q-13) não têm requisito funcional e, por isso, não têm componente obrigatório nem teste de aceite.
- **Auditoria operacional:** não é requisito aprovado; ADR-007 e testes de atomicidade só se aplicam se Q-11 aprovar escopo e garantia.
- **Qualidade operacional:** RNF-01..RNF-05 carecem de critérios/gates mensuráveis (Q-16); disponibilidade, desempenho, recuperação e escala não têm requisito de produto nem limiar.
- **Interface:** não há requisito de UI, cliente, endpoints ou contrato; API HTTP é proposta e qualquer interface visual depende de definição de escopo.
- **Evidência de execução:** não há aplicação, testes, migrations ou CI; as estratégias da matriz são experimentos planejados.

### Decisões propostas sem origem normativa direta

Estas são escolhas técnicas recomendadas, e não requisitos escondidos. A origem explica por que foram avaliadas; não as converte em obrigação de produto.

| Proposta | Origem disponível | O que falta aprovar |
|---|---|---|
| Monólito modular em camadas (ADR-001) | D-01/D-05; simplicidade e transação local derivadas de REQ-01/POL-01 | Ratificação arquitetural e limites dos módulos |
| PostgreSQL (ADR-002) | Experimento/restrições técnicas para integridade de D-01; nenhum requisito de banco | Aprovação técnica e prova contra o modelo de conflito aprovado |
| API HTTP (ADR-009) | Nenhum protocolo exigido por REQ; sequência atual é proposta | Confirmar canal/contrato e decisões Q-09/Q-10 |
| Spring Security/provedor de identidade (ADR-005) | POL-01 pede segurança, mas não nomeia tecnologia ou identidade | Q-10, ameaça, modelo de identidade e tecnologia |
| Ferramenta de migração (ADR-012) | Necessidade de versionar esquema; ferramenta não escolhida | Seleção após modelo aprovado |
| Testcontainers / CI / limiares (ADR-010) | Testes/CI em CTX-02 e pedido atual de integração com Testcontainers; limiares não definidos | Q-16 para gates e evidência operacional |
| Devcontainer/Codespaces (ADR-011) | CTX-01 e pedido atual; arquivos de configuração inexistentes | Ratificar configuração, serviços e política de segredos |

## 10. Sequência técnica proposta para implementação futura

Esta sequência não autoriza iniciar implementação. Cada etapa pressupõe story validada e dependências aprovadas; a decisão concorrente e de segurança é risco bloqueador antes de reservas reais.

| Ordem | Etapa | Dependências / gate de entrada | Resultado de validação planejado |
|---:|---|---|---|
| 0 | Fechar semântica temporal/conflito/estados/concorrência (Q-01/Q-02/Q-04/Q-09), segurança (Q-10), operações (Q-03), manutenção/aprovação (Q-05..Q-08/Q-15), escopo RF-14 e qualidade (Q-16) | Decisões de negócio e ratificação; não alterar PRD/regras sem aprovação | Exemplos normativos e oráculos de teste aprovados; baseline/RTM atualizados por seus responsáveis |
| 1 | Inicializar esqueleto Spring Boot 3.x, Java 21 e Maven | ADR-001 ratificado; story aprovada | Build limpo, estrutura modular/camadas mínima e teste de contexto |
| 2 | Configurar PostgreSQL e ambientes local/Codespaces | ADR-002/011 ratificados; credenciais fora do repositório | Conexão isolada por ambiente; teste de saúde sem expor segredos |
| 3 | Configurar ferramenta e convenção de migrações | Modelo de dados aprovado; ADR-012 decidido | Aplicação/reversão de migração em banco vazio e compatibilidade verificada |
| 4 | Integrar Testcontainers com PostgreSQL real | Ambiente e migrations funcionais | Testes de integração reproduzíveis de constraints, rollback e concorrência |
| 5 | Criar CI inicial | Comandos Maven determinísticos e estratégia ADR-010 ratificada | Build, testes e relatório de evidência; gate sem limiares inventados |
| 6 | Implementar autenticação e autorização | Q-10 fechado; identidade, matriz por papel/objeto e ADR-005 aprovados | Testes de acesso negado/permitido, propriedade e ausência de mutação não autorizada |
| 7 | Implementar cadastros de recursos/usuários | Q-03 fechado; autorização disponível | Testes de operações aprovadas e integridade relacional |
| 8 | Implementar disponibilidade e ciclo de reserva | Q-01/Q-02/Q-04/Q-09; domínio e estados aprovados | Testes de fronteira, conflitos e casos de uso; RF-14 ratificado |
| 9 | Implementar proteção concorrente | ADR-003 decidido; modelo relacional e Testcontainers | Corridas multi-conexão, reserva versus manutenção, estado final sem conflito |
| 10 | Implementar aprovações, alocação docente e manutenção | Q-05/Q-06/Q-08/Q-15; Q-10; estados definidos | Testes de transição, autorização e operação concorrente/atômica |
| 11 | Implementar auditoria operacional | Somente se Q-11 aprovar eventos, retenção e garantia; ADR-007 aprovado | Injeção de falha provando atomicidade ou garantia escolhida |
| 12 | Implementar relatórios/notificações | Somente se Q-12/Q-13 aprovarem escopo, público, canal e consistência | Consultas isoladas; falha/reprocessamento de integração sem corromper reserva |
| 13 | Interface e documentação de uso/API | Definição/aprovação de canal e público; não há UI no escopo atual | Contrato e jornada aprovados; docs alinhadas com testes e RTM |

**Portões críticos:** não fechar schema/migrações antes das Qs de conflito e estados; não habilitar mutações antes da matriz de acesso; não alegar cobertura antes de executar testes; não criar UI, notificações, relatórios ou auditoria operacional por inferência.