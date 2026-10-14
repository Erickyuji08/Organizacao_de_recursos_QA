# Avaliação arquitetural ATAM — Ciclo 00

**Projeto:** Organização de Recursos
**Data:** 2026-10-14
**Tipo:** avaliação documental inicial da arquitetura proposta
**Status:** avaliação consultiva; não é aprovação arquitetural nem gate de implementação.

## 1. Escopo, método e limitações

Esta avaliação aplica a estrutura do ATAM (Architecture Tradeoff Analysis Method), conforme Bass, Clements e Kazman: identificar drivers de negócio e atributos de qualidade; estruturar uma utility tree; formular cenários de qualidade; analisar abordagens arquiteturais, pontos de sensibilidade, trade-offs, riscos e não riscos; e indicar experimentos e decisões pendentes. É um **Ciclo 00 documental**, sem workshop com stakeholders, votação de prioridades, código, ambiente implantado ou medições. As classificações Alta/Média/Baixa são julgamentos iniciais da equipe avaliadora, não prioridades ratificadas pelo negócio.

**Nota de atualização:** este ciclo é um snapshot anterior à ratificação arquitetural de 2026-10-14. Referências a ADRs candidatos, mecanismos técnicos pendentes e ausência de baseline descrevem o estado analisado naquele ciclo; para decisões vigentes, prevalecem os ADRs aceitos e a [arquitetura consolidada](../arquitetura.md). Lacunas de produto, evidências operacionais e medidas de qualidade continuam pendentes quando identificadas.

Fontes lidas integralmente: [PRD](../prd.md), [RTM](../rtm.md), [casos de uso](../requisitos/casos-de-uso.md), [regras de negócio](../requisitos/regras-negocio.md), [requisitos não funcionais](../requisitos/requisitos-nao-funcionais.md), [revisão de requisitos](../requisitos/revisao.md), as três [personas](../personas/solicitante.md), [arquitetura proposta](../arquitetura.md), [contexto](../diagramas/contexto.md), [contêineres](../diagramas/conteineres.md), [componentes](../diagramas/componentes.md) e [sequências](../diagramas/sequencias.md).

O PRD/RTM têm status de rascunho. Não foram encontrados decisões arquiteturais aprovadas, implementação ou integração externa existente. Requisitos e regras não foram alterados. Os cenários adicionais solicitados para segurança, auditoria, disponibilidade, desempenho, recuperação e cancelamento são **estímulos de avaliação**, não requisitos novos. Quando não há requisito de origem, isso é indicado. Nenhuma métrica abaixo é meta obrigatória: valores/limiares, caso necessários, dependem de validação em Q-16.

## 2. Drivers arquiteturais

| ID | Driver e origem | Situação e consequência para a avaliação |
|---|---|---|
| D-01 | Reservas sem conflitos: REQ-01, RF-09, RN-01; RF-14 é derivação contextual; UC-09 | Driver de correção mais forte. O predicado de conflito e a ocupação por estado/recurso estão em aberto (Q-01/Q-02/Q-04). |
| D-02 | Disponibilidade e gestão das próprias reservas: REQ-02, RF-01/RF-02; Q-01/Q-03/Q-04/Q-10 | Exige consulta consistente e controle de propriedade; parâmetros, operações e autorização não estão especificados. |
| D-03 | Aprovação, validação docente, retirada/devolução: REQ-03, RF-10..RF-13; Q-05..Q-07/Q-15 | Critérios, transições, efeitos e participantes são lacunas. Não se presume que aprovação condicione reserva. |
| D-04 | Gerenciamento administrativo: REQ-04, RF-03..RF-08; Q-03/Q-08 | Cadastro e manutenção afetam agenda, mas operações e política para reservas existentes não foram decididas. |
| D-05 | Segurança, clareza, testabilidade e responsabilidades: POL-01, RNF-04 | São princípios explícitos, sem modelo de ameaças, autorização, métrica ou critérios de aceitação; Q-10/Q-16. |
| D-06 | Testes automatizados, TDD/BDD, ATAM e CI: CTX-02, RNF-01/RNF-02/RNF-05; rastreabilidade CTX-02/POL-02/RNF-03 | Direciona arquitetura testável e rastreável; escopo e gates continuam pendentes de Q-16. |
| D-07 | Stack contextual: CT-01 Java 21, CT-02 Spring Boot 3.x, CT-03 Maven | Restrições fornecidas, não decisões sobre persistência, implantação ou versões menores. |
| D-08 | Disponibilidade, desempenho, recuperação e escalabilidade | Não há RNF de produto, metas ou volumes nos documentos. São atributos a explorar porque pedidos nesta avaliação, não compromissos existentes. |
| D-09 | Auditoria operacional e integrações | Auditoria de produto depende de Q-11; integração/notificações dependem de Q-13. A rastreabilidade da RTM não é trilha de auditoria de operações. |

## 3. Utility tree priorizada

A prioridade combina valor potencial ao negócio e risco técnico aparente. **Importância** não significa requisito aprovado. **Dificuldade** estima a dificuldade arquitetural de atingir a resposta, considerando o desenho atual e lacunas do domínio; deve ser revisitada com stakeholders e provas técnicas.

```text
Utilidade do sistema
├── Correção e integridade da agenda
│   ├── U-01 Duas requisições simultâneas para a mesma sala [Alta/Alta]
│   ├── U-02 Conflito na agenda do mesmo professor [Alta/Alta]
│   ├── U-03 Reserva versus manutenção [Alta/Alta]
│   ├── U-06 Transição inválida [Alta/Média]
│   └── U-11 Cancelamento de reserva já iniciada [Média/Alta]
├── Segurança e autorização
│   ├── U-04 Aprovação por usuário sem permissão [Alta/Alta]
│   ├── U-05 Alteração de reserva de outra pessoa [Alta/Alta]
│   └── U-12 Acesso não autorizado a dados [Alta/Alta]
├── Integridade e continuidade
│   ├── U-07 Falha na persistência da auditoria [Média/Alta]
│   └── U-08 Indisponibilidade do banco [Média/Alta]
├── Desempenho e escalabilidade
│   ├── U-10 Sobrecarga de consultas de disponibilidade [Média/Alta]
│   └── U-15 Crescimento de volume/concorrência [Baixa/Alta]
├── Integração
│   └── U-09 Falha de integração externa, se uma for aprovada [Baixa/Média]
└── Evolutividade e verificabilidade
    ├── U-13 Alterar política de conflito sem espalhar regra [Média/Média]
    └── U-14 Rastrear requisito até teste reproduzível [Média/Média]
```

Os cenários de acesso têm importância inicial Alta pela consequência potencial de violar POL-01, mas a política concreta não existe em Q-10. U-07 tem prioridade condicional: se auditoria operacional não for aprovada em Q-11, o cenário não se aplica ao produto. U-09 também é condicional, pois não há integração declarada. Disponibilidade, performance, recuperação e escala receberam prioridade provisória Média/Baixa por falta de criticidade e medidas de produto, não por serem garantidamente dispensáveis.

## 4. Cenários ATAM

Em cada cenário, a medida é um **oráculo ou métrica candidata para discussão**; não é limite aprovado. Importância e dificuldade usam as definições da utility tree. “Fonte do estímulo” diferencia fonte documental de contexto deste pedido de avaliação.

### U-01 — Duas solicitações simultâneas para a mesma sala

- **Atributo:** consistência/concorrência.
- **Fonte do estímulo:** usuário desta avaliação; relacionado à invariante REQ-01/RF-09 e à derivação concorrente RF-14.
- **Estímulo:** dois solicitantes submetem reservas para a mesma sala em intervalos conflitantes quase ao mesmo tempo.
- **Ambiente:** aplicação em execução; duas transações/conexões independentes; PostgreSQL é apenas candidato.
- **Artefato afetado:** caso de uso de reserva, política de conflito, repositório/adaptador e constraint/isolamento do banco.
- **Resposta esperada:** o estado persistido não contém par inválido; cada pedido recebe resultado coerente com a política a decidir em Q-09. Não se fixa vencedor, ordem ou estado.
- **Medida da resposta:** proposta: zero pares que violem o predicado aprovado após a corrida; todos os pedidos com resultado rastreável. Carga/latência de disputa pode ser medida em teste, sem limiar até Q-16.
- **Importância / dificuldade:** Alta / Alta.
- **Requisito de origem:** REQ-01, RF-09, RN-01; RF-14 derivado; Q-01/Q-02/Q-04/Q-09.

### U-02 — Reserva conflitante com a agenda de um professor

- **Atributo:** consistência e correção de domínio.
- **Fonte do estímulo:** usuário desta avaliação; REQ-03/RF-11 menciona validação de alocação, mas não define conflito docente.
- **Estímulo:** uma reserva de outro recurso tenta alocar o mesmo professor em horário que já está ocupado.
- **Ambiente:** gravação normal ou concorrente; definição de sobreposição docente ainda pendente.
- **Artefato afetado:** modelo de ocupação, validação de alocação, consultas e mecanismo de concorrência do banco.
- **Resposta esperada:** aplicar o critério aprovado em Q-02; se conflito, não persistir combinação inválida. Não se presume que professor não possa ser alocado em dois recursos sem aprovação dessa regra.
- **Medida da resposta:** proposta: cenários positivos/negativos para a matriz aprovada de recursos/docentes e zero violações no estado final, inclusive em concorrência.
- **Importância / dificuldade:** Alta / Alta.
- **Requisito de origem:** REQ-01/RF-09/RN-01 e REQ-03/RF-11; regra específica é lacuna Q-02/Q-06.

### U-03 — Reserva de recurso em manutenção

- **Atributo:** consistência e disponibilidade funcional.
- **Fonte do estímulo:** usuário desta avaliação; REQ-04/RF-08 prevê gestão de manutenção, sem definir seu efeito.
- **Estímulo:** uma reserva é submetida durante período de manutenção ou manutenção é criada sobre reserva existente.
- **Ambiente:** operações potencialmente concorrentes de reserva e manutenção.
- **Artefato afetado:** caso de uso Manutenção, agenda, ocupação/constraint e transação.
- **Resposta esperada:** seguir política aprovada em Q-08 sem cancelamento/sobrescrita silenciosa; a criação/ativação e a reserva não podem deixar estado contraditório. Política ainda não decidida.
- **Medida da resposta:** proposta: zero reservas confirmadas que violem a política aprovada; provar corrida reserva-versus-manutenção e atomicidade.
- **Importância / dificuldade:** Alta / Alta.
- **Requisito de origem:** REQ-04/RF-08/RF-07/RN-04; Q-01/Q-08.

### U-04 — Aprovação por usuário sem permissão

- **Atributo:** segurança/autorização.
- **Fonte do estímulo:** usuário desta avaliação; atribuição de aprovar está em REQ-03, mas POL-01 pede segurança e Q-10 não define permissões.
- **Estímulo:** usuário autenticado sem autoridade tenta aprovar solicitação especial.
- **Ambiente:** API exposta; política de identidade e papéis configurada após decisão.
- **Artefato afetado:** autenticação, política de autorização, controller, caso de uso Aprovar e persistência.
- **Resposta esperada:** negar antes de qualquer mutação; registrar tentativa apenas se a auditoria de segurança/produto for definida. O código HTTP (403/404) depende do contrato.
- **Medida da resposta:** proposta: zero mutações por tentativa negada; matriz de autorização positiva/negativa completa para operações aprovadas; nenhuma decisão aprovada de acesso hoje.
- **Importância / dificuldade:** Alta / Alta.
- **Requisito de origem:** POL-01/RNF-04, REQ-03/RF-10; política ausente em Q-10.

### U-05 — Alteração de reserva de outro usuário

- **Atributo:** segurança, autorização por objeto e integridade.
- **Fonte do estímulo:** usuário desta avaliação; REQ-02 diz “suas reservas”, sem definir propriedade nem operação de alteração.
- **Estímulo:** solicitante autenticado altera reserva cujo proprietário é outra pessoa, tentando acesso direto por identificador.
- **Ambiente:** API em execução com dados de dois usuários.
- **Artefato afetado:** resolução de identidade/proprietário, autorização do caso de uso e repositório de reserva.
- **Resposta esperada:** negar sem alteração; não confiar somente em ocultar identificadores na interface. Exceções para responsável/administrador dependem da matriz Q-10.
- **Medida da resposta:** proposta: zero alterações fora do escopo autorizado em testes de acesso por objeto; revisar respostas para não revelar dados indevidos.
- **Importância / dificuldade:** Alta / Alta.
- **Requisito de origem:** REQ-02/RF-02/RN-02, POL-01; Q-03/Q-10.

### U-06 — Transição de estado inválida

- **Atributo:** consistência do ciclo de vida.
- **Fonte do estímulo:** usuário desta avaliação; PRD reconhece estados/transições como lacuna Q-04.
- **Estímulo:** comando tenta aprovar, cancelar ou alterar uma reserva em estado de origem não elegível.
- **Ambiente:** operação concorrente ou isolada após ciclo de vida aprovado.
- **Artefato afetado:** máquina de estados do domínio, caso de uso, controle de concorrência e constraint de persistência.
- **Resposta esperada:** rejeitar transição não prevista sem gravações parciais; serializar ou detectar alteração concorrente da mesma reserva.
- **Medida da resposta:** proposta: 100% das arestas válidas/inválidas da tabela de transição cobertas; estado e auditoria sem mudança parcial. Percentual é sugestão de teste, não gate aprovado.
- **Importância / dificuldade:** Alta / Média.
- **Requisito de origem:** REQ-01/RF-09 e REQ-02/RF-02; Q-04/Q-05/Q-14.

### U-07 — Falha durante persistência da auditoria

- **Atributo:** integridade, auditabilidade e consistência transacional.
- **Fonte do estímulo:** usuário desta avaliação; nenhum requisito de auditoria operacional confirmado; Q-11.
- **Estímulo:** gravação do evento de auditoria falha após a gravação principal, antes do commit.
- **Ambiente:** escrita transacional; evento de auditoria adotado e no mesmo banco/gerenciador transacional.
- **Artefato afetado:** caso de uso, adaptador/porta de auditoria, transação e banco.
- **Resposta esperada:** se o evento for obrigatório e atômico, rollback da operação de negócio; se for best-effort, risco de perda deve ser aceito/documentado. A arquitetura recomenda atomicidade condicional, ainda sem aprovação de Q-11.
- **Medida da resposta:** proposta: zero operações de negócio commitadas sem evento obrigatório correspondente, e zero eventos de negócio para operações revertidas; testar falha injetada.
- **Importância / dificuldade:** Média / Alta, condicional a Q-11.
- **Requisito de origem:** Q-11; POL-02/RNF-03 cobre rastreabilidade documental, não auditoria operacional.

### U-08 — Indisponibilidade do banco

- **Atributo:** disponibilidade, resiliência e recuperação.
- **Fonte do estímulo:** usuário desta avaliação; sem RNF de disponibilidade/recuperação nem decisão de implantação.
- **Estímulo:** banco PostgreSQL está inacessível ou perde conexão durante consulta/gravação.
- **Ambiente:** operação normal, falha temporária ou recuperação do serviço de banco.
- **Artefato afetado:** pool de conexões, casos de uso, adaptador PostgreSQL, implantação, backup/restore e observabilidade ainda não desenhados.
- **Resposta esperada:** não declarar escrita concluída sem confirmação durável; não aceitar reserva usando memória/cache como autoridade; falhar de forma controlada. Recuperar sem parcialidade ou duplicação segundo política ainda ausente.
- **Medida da resposta:** propostas: disponibilidade/tempo de recuperação, RTO/RPO, taxa de erro e verificação de integridade após restore. Todos os valores precisam de Q-16 e decisão operacional.
- **Importância / dificuldade:** Média / Alta, prioridade não validada.
- **Requisito de origem:** nenhum RNF operacional; CTX-02/RNF-05 apenas cita CI. Cenário requerido nesta avaliação.

### U-09 — Falha de integração externa

- **Atributo:** disponibilidade, resiliência e acoplamento.
- **Fonte do estímulo:** usuário desta avaliação; nenhuma integração externa existe nos requisitos; notificação é pergunta Q-13.
- **Estímulo:** serviço externo aprovado para um fluxo (se existir) retorna erro, expira ou fica indisponível.
- **Ambiente:** integração síncrona ou assíncrona, estratégia ainda não escolhida.
- **Artefato afetado:** porta/adaptador externo, caso de uso, transação e, se aprovado, outbox/retentativa.
- **Resposta esperada:** comportamento compatível com criticidade aprovada; não manter transação de reserva aberta esperando serviço não essencial. Para integração mandatória, garantir recuperação sem perda/duplicação de efeito conforme contrato futuro.
- **Medida da resposta:** proposta: disponibilidade do fluxo principal, atraso/falha de entrega e recuperação/reprocessamento; limites dependem de Q-13/Q-16.
- **Importância / dificuldade:** Baixa / Média enquanto não houver integração; reclassificar se Q-13 confirmar dependência crítica.
- **Requisito de origem:** nenhum requisito de integração; Q-13/RF não define canal, evento ou provedor.

### U-10 — Sobrecarga de consultas de disponibilidade

- **Atributo:** desempenho e disponibilidade percebida.
- **Fonte do estímulo:** usuário desta avaliação; RF-01 solicita consulta, mas não define carga nem latência.
- **Estímulo:** volume elevado de consultas coincide com gravações concorrentes.
- **Ambiente:** dados e carga representativos; índices, consultas e limites do banco ainda não existem.
- **Artefato afetado:** endpoint/caso de consulta, SQL/índices, pool, banco e capacidade de implantação.
- **Resposta esperada:** consultas refletem a consistência acordada sem esgotar recursos necessários às gravações; degradação e rejeição são controladas, sem cache inconsistente assumido.
- **Medida da resposta:** propostas: latência p50/p95/p99, throughput, erros, saturação de pool/DB e efeito sobre reservas. Carga e limites só após Q-16.
- **Importância / dificuldade:** Média / Alta; sem SLO ou workload aprovados.
- **Requisito de origem:** REQ-02/RF-01; nenhum RNF de desempenho; Q-16.

### U-11 — Cancelamento de reserva já iniciada

- **Atributo:** consistência de estado e disponibilidade do recurso.
- **Fonte do estímulo:** usuário desta avaliação; cancelamento não consta como operação aprovada e está em aberto em Q-14.
- **Estímulo:** solicitante tenta cancelar uma reserva cujo horário de início já ocorreu.
- **Ambiente:** ciclo de vida e política temporal previamente aprovados; pode haver concorrência com uso/retirada.
- **Artefato afetado:** caso de uso de reserva, máquina de estados, ocupação, material e auditoria condicional.
- **Resposta esperada:** aplicar regra de cancelamento aprovada sem liberar retroativamente recurso ou material por inferência; negar ou registrar resultado conforme política. Não há decisão atual.
- **Medida da resposta:** proposta: nenhuma transição fora da tabela aprovada; agenda e disponibilidade consistentes antes/depois; evidência de teste para limites temporais.
- **Importância / dificuldade:** Média / Alta.
- **Requisito de origem:** Q-14, Q-04, Q-01; nenhuma regra de cancelamento aprovada.

### U-12 — Tentativa de acesso não autorizado a dados

- **Atributo:** confidencialidade e autorização por objeto.
- **Fonte do estímulo:** usuário desta avaliação; POL-01/RNF-04 e propriedade de REQ-02 são as relações disponíveis.
- **Estímulo:** usuário consulta, lista ou obtém reserva/dados de pessoa/recurso fora do escopo autorizado.
- **Ambiente:** usuário autenticado ou anônimo; identidade, dados sensíveis e política ainda por definir.
- **Artefato afetado:** autenticação, consultas, filtros por proprietário, controllers e serialização da resposta.
- **Resposta esperada:** negar ou minimizar a resposta conforme política; aplicar controle no servidor/query, não somente na apresentação.
- **Medida da resposta:** proposta: zero registros além do escopo autorizado nos testes negativos e revisão de exposição de campos. Cobertura de ameaças e critérios pendem de Q-10/Q-16.
- **Importância / dificuldade:** Alta / Alta.
- **Requisito de origem:** POL-01/RNF-04, REQ-02/RF-02; Q-10.

### U-13 — Mudança de política de conflito

- **Atributo:** modificabilidade/manutenibilidade.
- **Fonte do estímulo:** avaliador, como cenário de evolução; mudança de requisito deve ser aprovada, não é requisito atual.
- **Estímulo:** política aprovada para conflito de um tipo de recurso muda.
- **Ambiente:** monólito modular candidato, com testes e schema versionado.
- **Artefato afetado:** domínio, caso de uso, adapter/constraint e testes de regressão.
- **Resposta esperada:** política concentrada nos limites relevantes, sem duplicação divergente em controllers, relatórios e serviços; alterações de schema implantáveis de forma controlada.
- **Medida da resposta:** proposta: localizar artefatos/testes impactados e demonstrar regressão automatizada; esforço/tempo de mudança só vira meta após baseline/Q-16.
- **Importância / dificuldade:** Média / Média.
- **Requisito de origem:** POL-01 (clareza), RNF-03; RF-09/RN-01. Cenário de evolução não é requisito de produto.

### U-14 — Rastrear requisito até teste reproduzível

- **Atributo:** testabilidade e rastreabilidade.
- **Fonte do estímulo:** CTX-02/POL-02; RNF-01/RNF-03.
- **Estímulo:** uma alteração de regra aprovada precisa encontrar decisão, código e testes afetados.
- **Ambiente:** desenvolvimento/CI; a RTM atual tem campos de testes e evidências vazios.
- **Artefato afetado:** IDs da RTM, decisões, cenários de teste, Maven/CI.
- **Resposta esperada:** cadeia requisito → decisão → cenário/teste → evidência identificável, sem declarar teste inexistente como executado.
- **Medida da resposta:** proposta: proporção de requisitos aprovados com vínculos e evidência, ou auditoria amostral; completude e limiar em Q-16.
- **Importância / dificuldade:** Média / Média.
- **Requisito de origem:** CTX-02/POL-02, RNF-01/RNF-03/RNF-05; Q-16.

### U-15 — Crescimento de volume e concorrência

- **Atributo:** escalabilidade e desempenho.
- **Fonte do estímulo:** avaliador; volume, usuários e necessidade de escala não constam das fontes.
- **Estímulo:** carga de consultas e gravações cresce além do baseline inicial.
- **Ambiente:** aplicação monolítica e banco relacional compartilhado; topologia ainda indefinida.
- **Artefato afetado:** API, módulos, pool, índices, banco e estratégia de implantação.
- **Resposta esperada:** escalar a aplicação sem quebrar a invariante transacional; identificar quando o banco vira gargalo antes de decompor serviços.
- **Medida da resposta:** proposta: curva de throughput/latência/erros conforme usuários e concorrência acordados, custo e contenção; sem limites obrigatórios até Q-16.
- **Importância / dificuldade:** Baixa / Alta no estado atual, por ausência de evidência de escala necessária.
- **Requisito de origem:** nenhum RF/RNF de escala; RF-01/RF-09 são fluxo afetado, Q-16.

## 5. Análise de abordagens, sensibilidade e trade-offs

### Pontos de sensibilidade

| Decisão/variável | Mudança e efeito arquitetural | Requisitos/cenários afetados |
|---|---|---|
| Predicado temporal e estados que ocupam agenda | Determina range/índice/constraint, resultados de consulta e cenários de borda; qualquer ajuste muda validade de reservas existentes. | Q-01/Q-04, RF-01/RF-09/RF-14; U-01/U-03/U-06/U-11. |
| Identidade usada na chave de ocupação | Sala, docente e material têm dimensões diferentes; constraint apenas por `room_id` não protege conflito docente ou quantidade. | Q-02/Q-06, RF-09/RF-11; U-01/U-02. |
| Estratégia PostgreSQL: constraint, locks ou serializável | Constraint rejeita no ponto de escrita; locks serializam e podem reduzir throughput; serializável pode abortar/repetir transações. | RF-09/RF-14; U-01/U-02/U-03/U-10. |
| Fronteira transacional de aprovação/manutenção/auditoria | Acrescentar escrita obrigatória pode aumentar contenção; separar banco/evento quebra atomicidade local. | Q-05/Q-08/Q-11; U-03/U-06/U-07. |
| Política de autorização e propriedade | Muda filtros em comandos/consultas e risco de IDOR; autorização apenas no controller pode não cobrir chamadas internas ou outros canais. | Q-10, RF-02/RF-10; U-04/U-05/U-12. |
| Síncrono versus assíncrono para integração | Chamada síncrona amplia latência e indisponibilidade; outbox/retentativa acrescenta estado, worker e consistência eventual. | Q-13; U-09. |
| Cache de disponibilidade | Pode reduzir leitura, mas cria risco de dado obsoleto; não pode ser autoridade para reservar. | RF-01/RF-09; U-01/U-10/U-15. |
| Limites entre módulos | Mais módulos reduzem acoplamento se fronteiras reais existirem; modularização excessiva cria contratos/indireção desnecessários no escopo atual. | POL-01/RNF-04; U-13/U-14. |

### Trade-offs

1. **Constraint de exclusão versus locks.** Constraint PostgreSQL dá arbitragem atômica para intervalos discretos e cobre escritores concorrentes; acopla ao mecanismo/extensão e não resolve automaticamente materiais quantitativos. Locks suportam regras mais ricas, mas dependem de todos os escritores seguirem a mesma disciplina e podem gerar contenção/deadlocks. Uma combinação pode ser adequada, mas só após esquema e semântica aprovados; não está decidida.
2. **Auditoria obrigatória versus disponibilidade da escrita.** Atomicidade no mesmo commit evita negócio sem evento, mas falha da escrita de auditoria aborta a operação. Persistência assíncrona melhora desacoplamento, mas admite atraso e exige mecanismo de entrega/recuperação. Q-11 precisa definir a garantia antes da escolha.
3. **Monólito modular versus serviços distribuídos.** Monólito favorece transação local e simplicidade de operação; limita escala independente. Serviços podem escalar isoladamente, mas tornam reserva multirrecurso e agenda transacional mais difíceis. Sem metas de volume/autonomia, a decomposição distribuída tem custo sem benefício demonstrado.
4. **Consulta/caches versus atualidade.** Cache pode ajudar consulta de disponibilidade, mas estado obsoleto não pode aprovar gravação. Começar sem cache reduz complexidade; performance deve ser medida antes de adicionar cache/invalidação.
5. **Integração síncrona versus assíncrona.** Síncrona é simples e imediata, mas pode estender transação/latência e derrubar o fluxo principal; assíncrona exige consistência eventual e operação de fila/outbox. Nenhuma integração está aprovada.
6. **Decomposição por capacidade versus simplicidade.** Separar reserva, aprovação, materiais, manutenção, segurança, auditoria, notificação e relatório pode criar limites úteis, mas tratá-los como módulos implantáveis desde já é sobrearquitetura. Apenas as separações justificadas por regras/transações devem virar fronteiras físicas; componentes opcionais devem ficar fora do baseline até decisão de produto.

### Riscos arquiteturais

| ID / risco | Decisão arquitetural associada | Efeito e origem | Mitigação/experimento | Requisito/cenários |
|---|---|---|---|---|
| R-01: exclusão PostgreSQL incompleta | Constraint de exclusão como defesa final | Esquema baseado só em sala permite conflito docente; modelo por quantidade não é representável por exclusão simples; extensão/versão pode não estar disponível. | Modelar recursos ocupáveis após Q-02; Testcontainers na versão-alvo; concorrência multi-recurso e teste de migration/extension. | RF-09/RF-14, Q-01/Q-02/Q-04; U-01/U-02/U-03. |
| R-02: semântica aberta vira regra técnica acidental | Cálculo no domínio + constraint/estado | Intervalo, adjacência e estados em aberto podem ser codificados como defaults silenciosos e rejeitar/aceitar reservas incorretamente. | Aprovar tabela de decisão e exemplos-limite antes de schema; transformar cada decisão em teste de domínio e integração. | Q-01/Q-02/Q-04; U-01/U-02/U-06/U-11. |
| R-03: disputa não tem resultado de produto | Constraint/serialização e mapeamento HTTP | Banco pode rejeitar uma gravação, mas resposta/estado vencedor não está definido; API pode violar expectativa do usuário. | Decidir Q-09; teste concorrente verifica estado final e resposta de cada requisição. | RF-14 derivado, Q-09; U-01/U-02. |
| R-04: autorização superficial/IDOR | Segurança proposta na API | Diagrama mostra controle no contêiner, sem regra de propriedade nem matriz; controle somente na rota deixa falhas por objeto/consulta. | Aprovar Q-10 e modelo de identidade; decisão de acesso por objeto no caso de uso/repositório; testes MockMvc negativos e revisão de ameaças. | POL-01/RNF-04, RF-02/RF-10, Q-10; U-04/U-05/U-12. |
| R-05: auditoria condicional inconsistente | Auditoria na mesma transação | A atomicidade recomendada só funciona se evento e escrita partilharem transação local. Falha silenciosa ou segundo store produz lacuna. | Decidir Q-11; injetar falha antes do commit e provar rollback; separar auditoria de segurança, RTM e logs. | Q-11; U-07. |
| R-06: banco único como ponto de indisponibilidade | Monólito + PostgreSQL único candidato | Falha do banco interrompe consulta e escrita; não há HA, backup, restore, RTO/RPO ou degradação especificados. | Definir criticidade/recuperação; testar backup/restore e falha com plano operacional, sem declarar RTO/RPO até aprovação. | Nenhum RNF de disponibilidade; U-08. |
| R-07: contenção/consulta cara | Transação PostgreSQL, consulta de agenda e possíveis locks | Carga de leitura pode saturar pool/DB ou aumentar espera de escrita; não há perfil nem SLO. | JMeter com dados representativos, explain/análise de índices e observação de contenção; acordar carga/limiares em Q-16. | RF-01/RF-09, Q-16; U-10/U-15. |
| R-08: manutenção concorre com reserva | Mesma agenda/constraint e leitura de impacto | Janela entre consultar ocupação e inserir manutenção permite corrida se bloqueios e reservas não compartilharem a mesma serialização. | Teste de corrida reserva-versus-manutenção; usar constraint ou lock comum escolhido após Q-08. | RF-07/RF-08, Q-08; U-03. |
| R-09: integração futura torna reserva dependente de terceiro | Porta de integração opcional | Chamada síncrona não especificada pode manter transação aberta ou transformar indisponibilidade externa em indisponibilidade da reserva. | Não incluir integração sem Q-13; se aprovada, WireMock/fault injection e escolha explícita entre síncrona e outbox. | Q-13; U-09. |
| R-10: cancelamento não tem política | Máquina de estados/domínio ainda não definida | Cancelamento iniciado pode liberar recurso ou estoque incorretamente; nenhuma operação/efeito está aprovado. | Resolver Q-04/Q-14 com casos-limite temporais; teste parametrizado da transição e da disponibilidade resultante. | Q-01/Q-04/Q-14; U-06/U-11. |
| R-11: fragmentação prematura | Muitos módulos opcionais e portas no monólito | Módulos para relatório/notificação/auditoria sem casos confirmados elevam manutenção e contratos internos sem benefício atual. | Reduzir baseline ao que tem requisito; manter opcionalidade documental e reavaliar após Q-11..Q-13. | RF-01..RF-13; Q-11/Q-12/Q-13; U-13. |
| R-12: ausência de critérios verificáveis | RTM sem evidência e RNFs sem metas | Resultados de qualidade não podem ser aceitos/rejeitados objetivamente; falsa confiança em cobertura ou CI. | Q-16 deve atribuir critério, owner e evidência; atualizar RTM somente após teste executado. | RNF-01..RNF-05, Q-16; U-14. |

As classificações de risco são qualitativas e provisórias. Probabilidade não pode ser estimada com evidência de código, carga ou operação inexistente.

### Temas recorrentes de risco

- **Invariantes incompletas:** semântica de tempo, recurso, estado e resultado ainda não forma predicado implementável (R-01/R-02/R-03/R-08/R-10).
- **Controle só em camada:** consulta/check no serviço ou autorização no controller, sem garantia atômica/por objeto, é insuficiente (R-01/R-04/R-08).
- **Dependência do banco:** constraint, transação, extensão, disponibilidade e recuperação concentram garantias num componente ainda candidato (R-01/R-05/R-06/R-07).
- **Requisitos de qualidade sem oráculo:** ausência de SLOs, políticas e evidências impede priorizar trade-offs e dizer se a resposta passou (R-06/R-07/R-12).
- **Complexidade opcional promovida ao baseline:** auditoria, notificações, relatórios e integrações não podem virar capacidade obrigatória por aparecerem em diagramas (R-05/R-09/R-11).

### Não riscos fundamentados para o escopo/documentação atuais

“Não risco” aqui significa que não há evidência suficiente para considerar o item um risco atual do produto; não garante que nunca será risco.

- **Falha de integração externa em produção:** não há integração aprovada ou configurada; U-09 é condicional, não uma dependência atual.
- **Consistência distribuída entre microserviços:** a proposta atual não cria microserviços nem broker; portanto não existe hoje transação distribuída. Não se deve atribuir esse risco ao desenho monolítico.
- **Cache obsoleto hoje:** não há cache proposto no baseline. O risco de stale read só passa a existir se cache for introduzido; não há motivo comprovado para fazê-lo antes de medir.
- **Auditoria operacional ausente como violação confirmada:** Q-11 está aberta e rastreabilidade RTM não equivale a auditoria; não se pode afirmar descumprimento sem decisão de produto.
- **Escalabilidade insuficiente demonstrada:** não há volume, carga ou necessidade de escala. Microserviços e particionamento não são mitigações justificadas até haver cenário medido.

## 6. Coerência entre arquitetura e requisitos

### Cobertura e lacunas

| Requisito/tema | Cobertura na proposta | Avaliação ATAM |
|---|---|---|
| RF-01 consulta disponibilidade | Caso de uso e porta de consulta; sem filtros, predicado, índices ou SLO. | Cobertura estrutural parcial; comportamento e performance não verificáveis (Q-01/Q-02/Q-16). |
| RF-02 gerir reservas próprias | Caso de uso e segurança propostos. | Propriedade e operações sem especificação; risco de acesso por objeto (Q-03/Q-10). |
| RF-03..RF-06 cadastros | Módulo genérico de recursos/cadastros. | Cobertura lógica; operações CRUD não podem ser inferidas (Q-03). |
| RF-07/RF-08 bloqueios/manutenção | Componente de manutenção e transação com agenda. | Efeito em reservas existentes, recurso e intervalo não definido (Q-08). |
| RF-09/RN-01 ausência de conflito | Domínio + constraint PostgreSQL candidata e transação. | Melhor cobertura estrutural, mas a cobertura de docente/quantidade/estados não está demonstrada (Q-01/Q-02/Q-04). |
| RF-10 aprovação especial | Caso de uso próprio e autorização candidata. | Critério, elegibilidade e efeito não definidos; não se pode fechar fluxo (Q-05/Q-10/Q-15). |
| RF-11 validação de docentes | Componente/caso de uso separado. | O que valida e conflito com agenda são lacunas (Q-02/Q-06). |
| RF-12/RF-13 retirada/devolução | Componente de materiais. | Eventos, participantes, estoque, trilha e retenção não definidos (Q-07/Q-11). |
| RF-14 concorrência | Constraint/locks/serializável comparados; nenhum escolhido. | Requisito derivado a ratificar; vencedor e resultado HTTP permanecem em Q-09. |
| Segurança/acesso | Componente candidato de segurança e pontos de autorização nos diagramas. | Princípio existe, matriz/autenticação/propriedade e ameaça não; lacuna crítica de desenho (POL-01/Q-10). |
| Auditoria | Porta opcional e proposta de mesma transação se requerida. | Sem decisão de escopo, integridade exigida ou critérios; não é cobertura confirmada (Q-11). |
| Disponibilidade/desempenho/recuperação/escala | Discutidos como atributos e testes futuros; arquitetura não define HA, backup, caches, pool, índices ou limites. | Não são requisitos existentes; sem metas, cobertura é intencionalmente aberta, não conformidade comprovada (Q-16). |
| Falha de integração | Porta opcional WireMock no plano de teste. | Nenhum sistema externo/canal declarado; desenho não deve acrescentar integração antes de Q-13. |
| Testes/CI/rastreabilidade | Camadas testáveis, ferramentas e RTM são propostas. | RNF existe como processo, mas sem pipeline, evidência e gates no repositório; Q-16. |

### Decisões sem justificativa suficiente ou componentes opcionais

- **PostgreSQL e constraint de exclusão:** justificáveis como candidato por RF-09/RF-14 e necessidade transacional, mas não como decisão final antes de semântica/modelo, versão/extensão e teste real. A arquitetura precisa justificar a constraint cobrindo tipos de recurso distintos.
- **API HTTP/controllers:** coerente com o pedido arquitetural e aplicação Spring, mas canal/cliente não consta das fontes; permanece decisão de arquitetura, não requisito de produto.
- **Spring Security:** candidato coerente com POL-01; mecanismo e política não são justificados até Q-10.
- **Auditoria, notificações e relatórios:** Q-11/Q-13/Q-12 são perguntas, não requisitos. Os componentes são desnecessários no baseline até confirmação; não os implantar como fato.
- **Nove módulos candidatos:** pode ser excesso de decomposição para produto sem código nem fluxo aprovado. Validar fronteiras de reserva, recurso, material e manutenção; evitar módulo implantável por cada substantivo.
- **Módulo de relatórios lê por porta genérica de repositório:** pode acoplar consulta analítica à persistência transacional. Se houver relatório, avaliar projeções/queries próprias; não criar agora.

### Dependências excessivas e riscos de segurança/consistência

- O banco é fonte de verdade para gravações, mas o modelo de ocupação ainda não define chave composta por tipo de recurso, professor, sala ou unidade de material. Repositórios ou constraints limitados à sala deixam lacuna de RF-09.
- Locks em linhas de reserva não protegem intervalos vazios/phantom; a estratégia precisa travar entidade-pai, usar constraint ou isolamento apropriado. Todo writer (reserva e manutenção) precisa compartilhar a proteção.
- Segurança mostrada entre controller e use case pode virar autorização apenas na borda. Propriedade, autorização por objeto e filtros de leitura devem ser verificados no serviço/consulta conforme política; a fonte atual não especifica quem pode ver o quê.
- Auditoria no mesmo banco só é atômica se usar a mesma transação e não realizar commit independente. Papel “append-only” reduz erro acidental, mas não resiste a administrador privilegiado; não chamar isso de imutabilidade forte.
- Sem plano de falhas, conexão perdida após commit pode tornar resultado ao cliente ambíguo. Idempotência/reconciliação são alternativas a avaliar se chamadas repetidas forem requisito; não são requeridas pelas fontes atuais.
- Disponibilidade de leitura não pode ser preservada por cache sem contrato de frescor. Cache nunca deve arbitrar gravação concorrente.

## 7. Matriz cenário → decisão → risco → experimento

Os cenários e experimentos são candidatos de validação. A matriz não converte métricas em RNF nem escolhe política de negócio.

| Cenário | Decisão a avaliar | Risco relacionado | Experimento/validação proposta |
|---|---|---|---|
| U-01 mesma sala concorrente | Constraint de exclusão versus lock/serializável; predicado Q-01/Q-02/Q-04; resultado Q-09 | R-01/R-02/R-03 | Testcontainers/PostgreSQL, duas conexões sincronizadas, repetição de corrida; verificar zero sobreposição inválida e as duas respostas. |
| U-02 professor ocupado | Modelagem de professor como recurso ocupável; decisão Q-02/Q-06 | R-01/R-02 | Teste de domínio + concorrência no PostgreSQL com reserva em outra sala; cobrir casos aceitos/rejeitados depois de aprovar a regra. |
| U-03 manutenção versus reserva | Política Q-08 e proteção comum a ambos writers | R-02/R-08 | Disparar reserva e manutenção em paralelo; testar transações, constraint/locks, rollback e resultado sem efeitos parciais. |
| U-04 aprovar sem permissão | Identidade, perfil e autorização de ação Q-10 | R-04 | MockMvc com usuário autorizado/não autorizado; comprovar que a negativa não grava decisão. Revisar matriz com PO/segurança. |
| U-05 alterar reserva alheia | Propriedade e autorização por objeto Q-10 | R-04 | Teste negativo com duas identidades e ID conhecido; validar consulta e mutação, não só ocultação de UI. |
| U-06 transição inválida | State machine Q-04/Q-05 e lock/versionamento | R-02/R-10 | Tabela de transições parametrizada; concorrência de duas decisões; verificar nenhuma gravação parcial. |
| U-07 auditoria falha | Atomicidade obrigatória versus best-effort Q-11 | R-05 | Injetar falha no adaptador auditável durante transação; conferir commit/rollback e par negócio-evento conforme política. |
| U-08 banco indisponível | Topologia, retry, backup/restore e RTO/RPO a decidir | R-06 | Fault injection de conexão e teste de restore/recuperação em ambiente isolado; só comparar com valores depois de Q-16. |
| U-09 integração externa falha | Síncrona versus assíncrona/outbox; criticidade Q-13 | R-09 | Apenas se integração aprovada: WireMock com timeout/5xx; observar efeito no caso de reserva e reprocessamento sem duplicar efeitos. |
| U-10 consulta sobrecarregada | Consulta/índices/pool e eventual cache após medição | R-07 | JMeter com perfil de carga acordado; capturar p50/p95/p99, erros, saturação e impacto em escrita; aprovar limites antes do gate. |
| U-11 cancelar iniciado | Política de cancelamento e transições Q-01/Q-04/Q-14 | R-10 | Casos parametrizados antes/no/depois do início conforme regra aprovada; validar ocupação e materiais sem inventar resultado. |
| U-12 acesso não autorizado a dados | Autenticação, autorização, filtros e minimização Q-10 | R-04 | Testes MockMvc/API de identidade anônima e usuário fora do escopo; revisar objetos/campos de resposta e ameaças. |
| U-13 mudar política de conflito | Fronteiras de módulo, domínio e migrações | R-11 | Exercício de mudança controlada após regra aprovada; inspeção de dependências/testes impactados e regressão automatizada. |
| U-14 rastrear requisito até teste | Convenção de IDs/RTM e gate CI Q-16 | R-12 | Auditoria amostral de requisito aprovado até decisão, teste e execução CI; registrar lacunas sem fabricar evidência. |
| U-15 crescer volume/concorrência | Monólito horizontal e limite do banco; eventual particionamento/decomposição | R-06/R-07 | Teste escalonado com carga/projeção acordadas; medir throughput/latência/contenção/custo e validar invariante com múltiplas instâncias. |

## 8. Recomendações priorizadas antes da implementação

1. **Bloqueio de arquitetura:** aprovar Q-01/Q-02/Q-04/Q-09: intervalo, conflito por sala/professor/material, quais estados ocupam agenda e resultado por requisição concorrente. Sem esses dados não há constraint nem oráculo de aceitação confiável.
2. **Segurança:** decidir Q-10 com matriz por operação e objeto: quem autentica, quem aprova, quem pode consultar/alterar cada reserva e como identificar propriedade. Testar recusas antes de considerar a arquitetura segura.
3. **Manutenção e ciclo de vida:** decidir Q-08 e Q-14 junto de Q-04, incluindo efeito em reservas existentes e cancelamento iniciado. Testar concorrência entre manutenção e reserva antes de fixar mecanismo.
4. **Persistência/concorrência:** prototipar o modelo candidato em PostgreSQL/Testcontainers; provar constraint, locks ou isolamento para sala, docente, manutenção e materiais. Verificar versão, extensão e comportamento de rollback; não declarar a exclusão suficiente antes desses resultados.
5. **Auditoria e integrações:** decidir Q-11/Q-13. Manter auditoria, notificações e relatórios fora do baseline até confirmação; se auditoria for obrigatória, testar atomicidade; se integração for crítica, escolher síncrona/assíncrona deliberadamente.
6. **Resiliência e qualidade operacional:** em Q-16, definir se disponibilidade, desempenho, recuperação e escala são atributos requeridos, seus workloads e medidas. Incluir RTO/RPO apenas se stakeholders os aprovarem. Executar teste de falha/restore e JMeter depois de definidos critérios.
7. **Simplificar estrutura:** validar fronteiras de módulos com os casos aprovados; reduzir componentes opcionais e evitar um módulo/deploy por entidade. Confirmar que controllers não concentram regra e que todo caminho de escrita usa a mesma política de consistência/autorização.
8. **Fechar rastreabilidade:** registrar decisões como ADR após validação por produto, arquitetura e stakeholders; vincular cenário, decisão e teste na RTM somente quando houver evidência executada.

## 9. Conclusão do ciclo

O desenho modular monolítico com PostgreSQL é uma hipótese razoável para preservar transações locais, mas a avaliação não o aprova: seus benefícios dependem de modelo de ocupação completo, segurança definida e validação concorrente real. Os riscos mais severos são a semântica incompleta da invariante, cobertura insuficiente de conflitos de docentes/manutenção, autorização sem matriz e falsa confiança de que uma constraint por sala cobre todo o domínio. Disponibilidade, performance, recuperação e escala permanecem sem critérios; não se pode concluir que a arquitetura atende ou falha nesses atributos.

**Decisões a rever antes de implementar:** Q-01/Q-02/Q-04/Q-08/Q-09/Q-10; em seguida Q-11/Q-13/Q-14/Q-16. Os ADRs foram agora formalizados como propostos ou pendentes; nenhum foi aprovado. Aprovar a arquitetura e qualquer ADR somente após experimento PostgreSQL de concorrência, revisão da matriz de autorização e acordo sobre atributos de qualidade. Nenhum requisito ou regra de negócio foi alterado por este relatório.

## Referências metodológicas

- Bass, Len; Clements, Paul; Kazman, Rick. *Software Architecture in Practice*. 4ª ed. Addison-Wesley Professional, 2021.
- Kazman, Rick; Klein, Mark; Clements, Paul. *ATAM: Method for Architecture Evaluation*. Carnegie Mellon University, Software Engineering Institute, CMU/SEI-2000-TR-004, 2000.
- Clements, Paul; Kazman, Rick; Klein, Mark. *Evaluating Software Architectures: Methods and Case Studies*. Addison-Wesley Professional, 2002.

## 10. Revisão multidisciplinar documental

Esta revisão foi feita por lentes especializadas sobre as fontes documentais; não representa entrevistas, pareceres de pessoas externas, pentest ou execução real de testes. As prioridades são provisórias e não substituem aprovação de produto.

| Especialidade | Aspectos positivos fundamentados | Objeções técnicas | Riscos identificados | Alternativas consideradas | Evidências e requisitos |
|---|---|---|---|---|---|
| Arquiteto de software | O desenho de camadas/portas favorece transação local e preserva o domínio de detalhes Spring; trade-offs de monólito versus serviços estão descritos. | Limites dos módulos, contrato HTTP e configuração ainda não estão aprovados; separar todo substantivo em módulo pode sobrearquitetar. | Regra de conflito espalhada, acesso cruzado entre módulos, interfaces/camadas sem benefício mensurado. | Monólito modular recomendado provisoriamente; monólito simples como opção menor; serviços distribuídos somente com justificativa de escala/autonomia. | PRD RF-01..RF-14; RNF-04; CT-01..CT-03; [arquitetura](../arquitetura.md); U-13/U-14; ADR-001. |
| Engenheiro Java/Spring Boot | Java 21, Spring Boot 3.x e Maven são restrições/contexto explícitos; controllers, casos de uso, domínio e adaptadores estão separados conceitualmente. | Não há aplicação, versão menor, dependências, API ou provider de identidade. As propostas não devem ser descritas como implementação existente. | Uso de Spring/JPA em regras de domínio, transação fora da unidade de caso de uso, dependência da stack sem prova de versão/extensão. | Spring Boot modular em uma aplicação; alternativa de estrutura simples sem fronteiras explícitas; nenhuma escolha de ORM ou migração foi feita. | CT-01..CT-03; RF-01..RF-14; Q-03/Q-10; diagramas de componentes e sequências; ADR-001/004/009/012. |
| Banco de dados e concorrência | A documentação corretamente separa consulta informativa da defesa final de gravação e alerta que pré-consulta não elimina corrida. | PostgreSQL permanece candidato; semântica temporal, estados ocupantes, alocação docente e materiais por item/quantidade ainda impedem constraint final. Lock em linhas existentes não resolve phantom sem estratégia apropriada. | Dupla reserva, concorrência reserva-manutenção, constraint parcial/incompleta, deadlock, contenção e resultado de chamada ambíguo após commit. | Constraint de exclusão para recursos discretos; lock pessimista por recurso para capacidade; serializável com abortos/retries; estratégia híbrida após prova. | REQ-01, RF-09/RF-14, RN-01, Q-01/Q-02/Q-04/Q-08/Q-09; U-01/U-02/U-03; ADR-002/003/004. |
| Engenharia de segurança | POL-01 cita segurança; ATAM inclui acesso sem permissão, alteração de reserva alheia e acesso não autorizado; diagrama agora distingue autenticação de autorização por objeto. | Perfis são atribuições, não matriz de acesso; Q-10 não define identidade, autenticação, propriedade, provedor ou respostas 403/404. | IDOR, acesso indevido a leitura/mutação, segredos expostos no ambiente e falsa confiança em autorização só no controller. | Provedor externo OIDC/OAuth ou identidade local; sessão ou token conforme canal; negar por padrão e autorização de objeto como direção, não política concluída. | POL-01/RNF-04; REQ-02..REQ-04; Q-10; U-04/U-05/U-12; ADR-005/009/011. |
| Qualidade e testes | IDs RF/RN/RNF/Q e RTM já permitem rastreabilidade; cenários ATAM e Testcontainers são planejados para validar o mecanismo real. | RTM não contém testes/evidência; critérios condicionais não permitem aceite. Não há gate CI, limiar, suite ou workload. | Testes unitários ou H2 serem confundidos com prova de concorrência PostgreSQL; testes instáveis; evidência presumida. | Testes de domínio + integração Testcontainers/PostgreSQL + API/BDD aprovada; testes de carga fora do gate rápido até decisão de métricas. | CTX-02; RNF-01..RNF-03/RNF-05; Q-16; RTM; U-01/U-14/U-15; ADR-010. |
| DevOps | Codespaces e stack são contexto; roteiro separa ambiente dev, migrações, testes e CI; princípios de segredos estão documentados. | Não há `.devcontainer/`, workflow CI nem migrations; PostgreSQL e ferramentas não foram configurados. Os arquivos AIOX existentes não são pipeline do produto. | Drift entre Codespaces/CI, segredos versionados, migration aplicada fora de ordem, declarar build ou deploy sem evidência. | Devcontainer mínimo e pipeline Maven reprodutível como propostas; configuração manual documentada enquanto não ratificada; nenhum deploy/serviço externo no container. | CTX-01/CTX-02; CT-01..CT-03; RNF-05; Q-16; ADR-011/012; ausência de `.devcontainer/` e `.github/workflows/`. |
| Facilitador ATAM | Utility tree U-01..U-15, cenários, pontos de sensibilidade, trade-offs, riscos e experimentos estão documentados; entradas de cenário externo são explicitamente distinguíveis de requisito. | Este ciclo é documental, sem workshop, votação, personas reais ou medidas; classificações Alta/Média/Baixa não são ratificadas. | Tratar priorização da equipe como decisão de negócio; assumir meta de desempenho/disponibilidade; confundir risco potencial com falha comprovada. | Workshop ATAM com stakeholders e atualização de utility tree; manter hipóteses como estímulos até aprovação. | ATAM Ciclo 00, Q-16, RF-09/RF-14; RNF-01..RNF-05; U-01..U-15. |

### Divergências registradas entre as lentes

1. **Constraint vs. locks/serializável:** a lente Java favorece integração coesa com uma aplicação Spring; banco/concorrência insiste que constraint só cobre o predicado e forma de dados que forem modelados. Síntese: ADR-003 permanece pendente; testar alternativas, não escolher algoritmo antes das Qs.
2. **Módulos vs. simplicidade:** arquitetura favorece limites explícitos para regra de agenda; implementação alerta contra criar módulos por substantivo antes de existir fluxo. Síntese: um único deploy, poucos limites lógicos justificados e nenhum serviço independente.
3. **Auditoria transacional vs. disponibilidade:** auditoria recomenda atomicidade se exigida; produto/QA não têm requisito para auditoria. Síntese: não criar trilha operacional até Q-11; se aprovada como obrigatória, comparar garantia atômica e custo de falha.
4. **Autenticação local vs. provedor externo:** segurança não pode escolher sem público, identidade e operação; DevOps precisará de configuração segura independente da opção. Síntese: mecanismo pendente de Q-10, mantendo controles por objeto como critério de validação.
5. **Erros HTTP e critérios de CI:** QA deseja contrato e gates reproduzíveis; produto não decidiu 409/422/403 nem limiares. Síntese: candidatos técnicos documentados em ADR-009/010, sem fixar status/gates antes do contrato e Q-16.
6. **ATAM:** as classificações iniciais guiam experimentos, mas o facilitador não as eleva a prioridades aprovadas sem participantes/stakeholders.

## 11. Verificação de consistência documental

| Relação verificada | Resultado | Observação/correção |
|---|---|---|
| PRD ↔ regras de negócio | Coerente | RF-01..RF-13 decompõem REQ-01..REQ-04; RN-01..RN-04 preservam limites. Nenhuma nova regra foi escrita. |
| PRD ↔ RTM | Coerente | RF-14 segue identificado como derivação contextual; campos de testes/CI continuam vazios. |
| PRD ↔ personas/stakeholders/casos de uso | Coerente, com escopo limitado | Perfis e verbos são fiéis às fontes; fluxos e autorizações continuam ausentes e não são presumidos. |
| Arquitetura ↔ C4 | Coerente após revisão | Corrigida a afirmação obsoleta de que não havia diretório de diagramas. Diagramas existentes continuam como proposta, não sistema implantado. |
| Componentes ↔ sequências | Coerente após revisão | Fluxo de consulta de conflito agora atravessa porta/adaptador em vez de acessar banco diretamente; autenticação é distinta de autorização em casos de uso. |
| Sequências ↔ regras/estados | Parcial/condicional | Códigos HTTP, aprovação, resultado de disputa e efeito de manutenção seguem marcados como pendentes de Q-04/Q-05/Q-08/Q-09/Q-10. |
| Utility tree/cenários ↔ requisitos | Coerente com ressalvas | U-01..U-15 incluem estímulos desta avaliação; cenários sem fonte (disponibilidade, escala e integração) estão explicitamente tratados como explorações, não novos RNFs. |
| ADRs ↔ arquitetura/RTM | Coerente | ADR-001..ADR-012 têm status proposto/pendente; nenhuma decisão está marcada aprovada e a matriz aponta testes planejados, não executados. |
| Requisitos aprovados ↔ baseline | Ressalva de fonte | PRD declara status “rascunho para validação”; a revisão registra que não foi encontrado baseline de requisito aprovado. A revisão não modifica PRD nem regras. |

## 12. Resultado e status das decisões

### Aprovadas

**Nenhuma.** Não há evidência de ratificação por produto/stakeholders nem experimento técnico concluído.

### Propostas para ratificação

- Arquitetura de monólito modular em camadas (ADR-001).
- PostgreSQL como persistência relacional (ADR-002).
- Fronteira transacional local por caso de uso de escrita (ADR-004).
- Mapeamento centralizado de erros HTTP, sem fixar códigos pendentes (ADR-009).
- Testes em camadas com integração PostgreSQL/Testcontainers e CI Maven propostos, limiares em aberto (ADR-010).
- Devcontainer Codespaces mínimo e sem segredos versionados (ADR-011).

### Pendentes de decisão/prova

- Proteção de dupla reserva (ADR-003): Q-01/Q-02/Q-04/Q-08/Q-09 e experimento.
- Identidade e autorização (ADR-005): Q-10.
- Estados/transições (ADR-006): Q-04/Q-05/Q-08/Q-14/Q-15.
- Auditoria operacional (ADR-007): Q-11.
- Notificações/integrações (ADR-008): Q-13; relatórios Q-12 fora do baseline.
- Migrações/tooling (ADR-012): modelo PostgreSQL e comparação de ferramentas.
- Critérios de qualidade e evidência CI: Q-16.

**Conclusão do painel:** **CONCERNS / não aprovado para implementação**. A recomendação de arquitetura é utilizável como proposta para discussão, mas decisões de negócio, autorização e mecanismo concorrente ainda impedem um baseline seguro e verificável. Consulte [arquitetura](../arquitetura.md), [checklist](../arquitetura-checklist.md) e o [índice de ADRs](../adr/README.md). Não houve inicialização do projeto, código, migração, execução de testes ou commit.