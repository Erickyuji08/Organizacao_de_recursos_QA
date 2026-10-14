# Regras de negócio — Projeto Organização de Recursos

**Status:** rascunho para validação; nenhuma regra além das fontes declaradas foi presumida.

## Fontes

Os identificadores REQ foram atribuídos documentalmente em [stakeholders](../stakeholders.md); não constavam no enunciado original.

- **REQ-01:** “Projeto Organização de Recursos: sistema para gerenciamento de salas, professores e materiais, com reservas sem conflitos de horários.”
- **REQ-02:** “Solicitante: professor ou coordenador que consulta disponibilidade e gerencia suas reservas.”
- **REQ-03:** “Responsável: aprova solicitações especiais, valida alocação de docentes e acompanha retirada e devolução de materiais.”
- **REQ-04:** “Administrador: gerencia usuários, salas, professores, materiais, bloqueios e períodos de manutenção.”

## Regras confirmadas

### RN-01 — Invariante geral de reservas sem conflitos

- **Regra:** reservas sem conflitos de horários.
- **Origem:** REQ-01.
- **Limite:** a fonte não define o que é conflito, quais recursos ou estados são considerados, nem os limites dos intervalos. A regra não determina algoritmo, resposta, prioridade ou estratégia de implementação.
- **Referências:** [RF-09](../prd.md#rf-09--preservar-reservas-sem-conflitos-de-horarios), [RF-14](../prd.md#rf-14--preservar-ausencia-de-conflitos-em-requisicoes-concorrentes).

### RN-02 — Atribuições declaradas do solicitante

- **Regra/atribuição:** professor ou coordenador consulta disponibilidade e gerencia suas reservas.
- **Origem:** REQ-02.
- **Limite:** atribuição declarada, não gate de autorização. “Gerencia” não enumera operações e “suas” não estabelece autenticação nem como a propriedade é reconhecida.

### RN-03 — Atribuições declaradas do responsável

- **Regra/atribuição:** o responsável aprova solicitações especiais, valida alocação de docentes e acompanha retirada e devolução de materiais.
- **Origem:** REQ-03.
- **Limite:** atribuições declaradas, não gates ou políticas de autorização. Não define critérios, sequência, efeitos, atores da retirada/devolução ou estados.

### RN-04 — Atribuições declaradas do administrador

- **Regra/atribuição:** o administrador gerencia usuários, salas, professores, materiais, bloqueios e períodos de manutenção.
- **Origem:** REQ-04.
- **Limite:** atribuição declarada, não gate de autorização. Não enumera operações, campos, regras de integridade ou impacto sobre reservas.

## Limites das fontes e decisões técnicas posteriores

As fontes originais não estabelecem CRUD; semântica de conflito por sala/professor/material; prioridade; cancelamento; prazo; tolerância entre reservas; quantidade; multa; retenção; ciclo funcional completo; impacto de bloqueios sobre reservas; identidade ou matriz de permissões; que aprovação preceda confirmação; que “solicitação especial” signifique recurso restrito; relatórios ou notificações. Decisões técnicas posteriores definem o intervalo semiaberto `[início, fim)`, estados técnicos bloqueantes `PENDENTE`/`APROVADA`, estados não bloqueantes `REJEITADA`/`CANCELADA`, protocolo de locks e autorização básica; isso não completa as regras de produto pendentes em Q-01/Q-02/Q-04/Q-08/Q-10/Q-14/Q-15. A auditoria operacional mínima foi adotada em ADR-007; seu conteúdo e atomicidade são decisão arquitetural, não inferência das fontes. Retenção e acesso permanecem pendentes em Q-11.

## Perguntas abertas e alternativas não decididas

As linhas identificam decisões técnicas já ratificadas, decisões parcialmente resolvidas e perguntas ainda abertas; alternativas não decididas não são defaults. Critérios funcionais afetados permanecem condicionados às respostas de produto e aprovação documental. IDs Q são documentais e sincronizados com a lista no [PRD](../prd.md#perguntas-abertas).

| ID | Pergunta | Alternativas não decididas |
|---|---|---|
| Q-01 | **Aberta:** qual é a semântica temporal, inclusive limites, validação de início/fim, datas permitidas e fuso/calendário? | Para limites: intervalo semiaberto `[início, fim)`; limites inclusivos; ou outra convenção documentada. Para início/fim: exigir início anterior ao fim; permitir outros casos sob validação explicitamente aprovada; ou definir outra validação. Para datas: permitir ou não datas passadas e estabelecer ou não horizonte máximo futuro. Definir fuso horário, calendário de referência e tratamento de mudanças. Nenhuma opção está escolhida. |
| Q-02 | **Aberta:** quais dimensões definem conflito e sobreposição por tipo de recurso, além da margem/buffer? | Para salas, considerar conflito somente para a mesma sala em períodos sobrepostos, ou definir outro critério. Para docentes, decidir se o mesmo docente pode ser alocado em recursos distintos no mesmo período. Para materiais, decidir entre controle por item individual ou por tipo/quantidade disponível e definir como demanda e disponibilidade se relacionam. O intervalo semiaberto ratificado em Q-01 significa que intervalos adjacentes não se sobrepõem; definir se há margem/buffer adicional e sua extensão, se aplicável. São opções para validação, não regras adotadas pelo projeto. |
| Q-03 | Quais operações cada “gerenciar” abrange, por entidade? | Consultar/cadastrar; consultar/cadastrar/alterar/remover; conjunto explicitamente diferente. Sem assumir CRUD. |
| Q-04 | **Parcialmente decidida:** ADR-006 define os estados técnicos mínimos que ocupam agenda (`PENDENTE` e `APROVADA`) e os que não ocupam (`REJEITADA` e `CANCELADA`). O ciclo de vida funcional completo, critérios de transição, atores, cancelamento e consequências continuam abertos. | As propostas de representar aprovação separada ou como parte de um ciclo mais amplo continuam alternativas de produto; não alteram a regra técnica de ocupação já ratificada. Nenhum fluxo funcional adicional é presumido. |
| Q-05 | **Aberta:** o que define solicitação especial, quais critérios se aplicam e como registrar o resultado da aprovação? | Definir conceito, critérios e representação da decisão, ou outra definição aprovada. A aprovação pode apenas registrar uma decisão ou ter outro efeito a definir, sem presumir que bloqueie ou anteceda reserva. A relação com “recurso restrito” fica em Q-15 e permanece aberta. |
| Q-06 | Quais dados/critérios validam alocação docente e o que a validação altera? | Critérios e efeito a definir; apenas registro do resultado; outro resultado documentado. |
| Q-07 | O que significa acompanhar retirada e devolução e quais dados/atores participam? | Acompanhar eventos; acompanhar dados definidos (por exemplo, pessoa/data/quantidade, somente se aprovados); outro escopo documentado. |
| Q-08 | **Aberta:** como bloqueios e manutenção afetam novas reservas e reservas já existentes? | Alternativas incluem afetar somente novas reservas; preservar reservas existentes sem alteração; ou marcar reservas existentes para análise, reagendamento ou cancelamento conforme decisão. Outro efeito pode ser explicitamente definido. O efeito, intervalo e recurso abrangido precisam de definição; notificações não são inferidas. Nenhuma alternativa está escolhida. |
| Q-09 | **Parcialmente decidida (ADR-003/009):** operações incompatíveis são serializadas pelos locks dos recursos e a operação que falha na revalidação retorna HTTP 409. | A política de prioridade/fairness, escolha da vencedora entre concorrentes e estado de negócio detalhado permanecem abertos; a ordem efetiva é a de aquisição do lock, não a de chegada garantida. |
| Q-10 | **Parcialmente decidida (ADR-005):** usar JWT stateless/Spring Security, negar por padrão, limitar papéis às atribuições declaradas e validar propriedade no servidor. | Identidade, provisionamento de contas/credenciais e fluxo de emissão de tokens precisam ser decididos antes de implementar autenticação; não inferir CRUD ou permissões além do PRD. |
| Q-11 | **Parcialmente decidida (ADR-007):** auditoria mínima atômica cobre mutações administrativas, mudanças de reserva, decisões e acompanhamento implementados; somente ator, ação, entidade, data/hora UTC e resultado. | Retenção, acesso de leitura e descarte dependem de política institucional; definir antes de operar com dados reais. Não registrar payload integral, credenciais, tokens ou segredos. |
| Q-12 | Há relatórios? | Não; sim, com público, conteúdo e período definidos; outro escopo. |
| Q-13 | Há notificações? | Não; sim, com eventos, público e canais definidos; outro escopo. |
| Q-14 | Aplicam-se prioridade, cancelamento, tolerância temporal, prazos, limites de quantidade ou penalidade? | Definir cada regra; declarar não aplicável; manter fora do escopo. Não há valores/regra presumidos. |
| Q-15 | **Aberta:** o que significa “recurso restrito”, qual sua relação com “solicitação especial” e qual pode ser o efeito da aprovação? | Os conceitos podem ser independentes, equivalentes, parcialmente relacionados ou sem relação; não há relação escolhida. Pode não existir classificação de recurso restrito, ou ela pode ser definida por critérios, por responsável/perfil ou por outro modelo aprovado. A aprovação pode apenas registrar uma decisão ou, se isso for explicitamente decidido, condicionar a reserva. Relação, classificação e efeito permanecem abertos. |

## Hipóteses

Nenhuma hipótese foi adotada. As alternativas acima são apenas opções para validação, não defaults nem recomendações.
