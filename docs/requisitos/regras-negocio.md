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

## Não decorre das regras confirmadas

As fontes não estabelecem CRUD; intervalo inclusivo ou semiaberto; conflitos por sala/professor/material; prioridade; cancelamento; prazo; tolerância entre reservas; quantidade; multa; retenção; conjunto de estados ou transições; bloqueio de novas/existentes reservas durante manutenção; autenticação ou matriz de permissões; que aprovação preceda confirmação; que “solicitação especial” signifique recurso restrito; relatórios; notificações; nem trilha de auditoria de eventos do produto. Rastreabilidade de requisitos e decisões é uma instrução de processo, não uma regra de negócio de auditoria operacional.

## Perguntas abertas e alternativas não decididas

Nenhuma alternativa está adotada. IDs Q são documentais e sincronizados com a lista no [PRD](../prd.md#perguntas-abertas).

| ID | Pergunta | Alternativas não decididas |
|---|---|---|
| Q-01 | **Aberta:** qual é a semântica temporal, inclusive limites, validação de início/fim, datas permitidas e fuso/calendário? | Para limites: intervalo semiaberto `[início, fim)`; limites inclusivos; ou outra convenção documentada. Para início/fim: exigir início anterior ao fim; permitir outros casos sob validação explicitamente aprovada; ou definir outra validação. Para datas: permitir ou não datas passadas e estabelecer ou não horizonte máximo futuro. Definir fuso horário, calendário de referência e tratamento de mudanças. Nenhuma opção está escolhida. |
| Q-02 | **Aberta:** quais dimensões definem conflito e sobreposição, inclusive adjacência e margem/buffer? | Para salas, considerar conflito somente para a mesma sala em períodos sobrepostos, ou definir outro critério. Para docentes, decidir se o mesmo docente pode ser alocado em recursos distintos no mesmo período. Para materiais, decidir entre controle por item individual ou por tipo/quantidade disponível e definir como demanda e disponibilidade se relacionam. Decidir se intervalos adjacentes conflitam e se há margem/buffer (e sua extensão, se aplicável). São opções para validação, não regras adotadas pelo projeto. |
| Q-03 | Quais operações cada “gerenciar” abrange, por entidade? | Consultar/cadastrar; consultar/cadastrar/alterar/remover; conjunto explicitamente diferente. Sem assumir CRUD. |
| Q-04 | **Aberta:** como representar o ciclo de vida da reserva, decisões e encerramento, e quais registros ocupam agenda? | Como propostas meramente ilustrativas: estado único de reserva com registro da aprovação separado; ciclo explícito com solicitação, decisão e encerramento; ou outro ciclo documentado. Estados, rótulos, transições e efeito na agenda dependem de decisão. Nenhuma proposta é verdadeira por padrão, e nenhuma transição ou rótulo está imposto. |
| Q-05 | **Aberta:** o que define solicitação especial, quais critérios se aplicam e como registrar o resultado da aprovação? | Definir conceito, critérios e representação da decisão, ou outra definição aprovada. A aprovação pode apenas registrar uma decisão ou ter outro efeito a definir, sem presumir que bloqueie ou anteceda reserva. A relação com “recurso restrito” fica em Q-15 e permanece aberta. |
| Q-06 | Quais dados/critérios validam alocação docente e o que a validação altera? | Critérios e efeito a definir; apenas registro do resultado; outro resultado documentado. |
| Q-07 | O que significa acompanhar retirada e devolução e quais dados/atores participam? | Acompanhar eventos; acompanhar dados definidos (por exemplo, pessoa/data/quantidade, somente se aprovados); outro escopo documentado. |
| Q-08 | **Aberta:** como bloqueios e manutenção afetam novas reservas e reservas já existentes? | Alternativas incluem afetar somente novas reservas; preservar reservas existentes sem alteração; ou marcar reservas existentes para análise, reagendamento ou cancelamento conforme decisão. Outro efeito pode ser explicitamente definido. O efeito, intervalo e recurso abrangido precisam de definição; notificações não são inferidas. Nenhuma alternativa está escolhida. |
| Q-09 | **Aberta:** qual resultado é observável quando requisições concorrentes disputam disponibilidade ou conflitam? | Alternativas ilustrativas: aceitar uma e rejeitar outra; aceitar uma e manter outra pendente; ou definir outro resultado. Continuam abertas qual requisição vence (se houver vencedora), o estado de cada requisição e a resposta apresentada ou registrada. Não há prioridade, vencedora, estado ou resposta presumidos. |
| Q-10 | Que permissões por perfil e que identificação de propriedade se aplicam? | Limitar às atribuições declaradas; mapa explícito de permissões; outro modelo aprovado. Definir identidade e escopo de “próprias”. |
| Q-11 | Há auditoria operacional? Que eventos, conteúdo e duração? | Sem trilha de produto; eventos selecionados; eventos ampliados; retenção a definir ou não aplicável. |
| Q-12 | Há relatórios? | Não; sim, com público, conteúdo e período definidos; outro escopo. |
| Q-13 | Há notificações? | Não; sim, com eventos, público e canais definidos; outro escopo. |
| Q-14 | Aplicam-se prioridade, cancelamento, tolerância temporal, prazos, limites de quantidade ou penalidade? | Definir cada regra; declarar não aplicável; manter fora do escopo. Não há valores/regra presumidos. |
| Q-15 | **Aberta:** o que significa “recurso restrito”, qual sua relação com “solicitação especial” e qual pode ser o efeito da aprovação? | Os conceitos podem ser independentes, equivalentes, parcialmente relacionados ou sem relação; não há relação escolhida. Pode não existir classificação de recurso restrito, ou ela pode ser definida por critérios, por responsável/perfil ou por outro modelo aprovado. A aprovação pode apenas registrar uma decisão ou, se isso for explicitamente decidido, condicionar a reserva. Relação, classificação e efeito permanecem abertos. |

## Hipóteses

Nenhuma hipótese foi adotada. As alternativas acima são apenas opções para validação, não defaults nem recomendações.
