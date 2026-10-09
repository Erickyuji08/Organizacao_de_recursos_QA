# Casos de uso — Projeto Organização de Recursos

**Status:** inventário mínimo de capacidades declaradas, não especificação de fluxos completos. Os casos não acrescentam passos, pré-condições, resultados ou transições ausentes das fontes.

## Atores e base

- **Solicitante:** professor ou coordenador que consulta disponibilidade e gerencia as próprias reservas (REQ-02).
- **Responsável:** aprova solicitações especiais, valida alocação de docentes e acompanha retirada e devolução de materiais (REQ-03).
- **Administrador:** gerencia usuários, salas, professores, materiais, bloqueios e períodos de manutenção (REQ-04).
- Para a invariante de reservas sem conflitos (REQ-01), a fonte não designa ator operacional.

Os IDs REQ foram atribuídos documentalmente e são reproduzidos em [stakeholders](../stakeholders.md). RF correspondentes estão em [PRD](../prd.md). “Gerenciar” não implica operações CRUD.

## Casos

### UC-01 — Consultar disponibilidade
- **Ator:** Solicitante (professor ou coordenador).
- **Objetivo declarado:** consultar disponibilidade.
- **RF/origem:** RF-01 / REQ-02.
- **Fluxo conhecido:** a fonte declara somente que o solicitante consulta disponibilidade; parâmetros, recurso, período, passos e resultado não são descritos.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

### UC-02 — Gerenciar próprias reservas
- **Ator:** Solicitante.
- **Objetivo declarado:** gerenciar suas reservas.
- **RF/origem:** RF-02 / REQ-02.
- **Fluxo conhecido:** a fonte declara somente o gerenciamento de próprias reservas; não enumera operações nem sequência.
- **Pré-condições:** não definidas, inclusive identificação/autenticação e propriedade.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

### UC-03 — Gerenciar usuários
- **Ator:** Administrador.
- **Objetivo declarado:** gerenciar usuários.
- **RF/origem:** RF-03 / REQ-04.
- **Fluxo conhecido:** somente a atribuição de gerenciamento está declarada; operações e interação não descritas.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

### UC-04 — Gerenciar salas
- **Ator:** Administrador.
- **Objetivo declarado:** gerenciar salas.
- **RF/origem:** RF-04 / REQ-04.
- **Fluxo conhecido:** somente a atribuição de gerenciamento está declarada; operações e interação não descritas.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

### UC-05 — Gerenciar professores
- **Ator:** Administrador.
- **Objetivo declarado:** gerenciar professores.
- **RF/origem:** RF-05 / REQ-04.
- **Fluxo conhecido:** somente a atribuição de gerenciamento está declarada; operações e interação não descritas.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

### UC-06 — Gerenciar materiais
- **Ator:** Administrador.
- **Objetivo declarado:** gerenciar materiais.
- **RF/origem:** RF-06 / REQ-04.
- **Fluxo conhecido:** somente a atribuição de gerenciamento está declarada; operações e interação não descritas.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

### UC-07 — Gerenciar bloqueios
- **Ator:** Administrador.
- **Objetivo declarado:** gerenciar bloqueios.
- **RF/origem:** RF-07 / REQ-04.
- **Fluxo conhecido:** somente a atribuição de gerenciamento está declarada; significado e operações não descritos.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

### UC-08 — Gerenciar períodos de manutenção
- **Ator:** Administrador.
- **Objetivo declarado:** gerenciar períodos de manutenção.
- **RF/origem:** RF-08 / REQ-04.
- **Fluxo conhecido:** somente a atribuição de gerenciamento está declarada; recursos afetados e operações não descritos.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

### UC-09 — Preservar reservas sem conflitos de horários
- **Ator:** não designado na fonte; sistema é contexto, não ator de negócio declarado.
- **Objetivo declarado:** manter reservas sem conflitos de horários.
- **RF/origem:** RF-09 / REQ-01.
- **Fluxo conhecido:** a fonte enuncia a propriedade do sistema, sem fluxo de interação.
- **Pré-condições:** definição de conflito e ocupação não definida.
- **Pós-condições:** invariante desejada declarada; verificação concreta não definida.
- **Estados/transições:** não definidos.

### UC-10 — Aprovar solicitações especiais
- **Ator:** Responsável.
- **Objetivo declarado:** aprovar solicitações especiais.
- **RF/origem:** RF-10 / REQ-03.
- **Fluxo conhecido:** a fonte declara a ação de aprovar; não define solicitação, critérios, resultado negativo, solicitante ou sequência.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas; não se presume que aprovação anteceda reserva.
- **Estados/transições:** não definidos.

### UC-11 — Validar alocação de docentes
- **Ator:** Responsável.
- **Objetivo declarado:** validar alocação de docentes.
- **RF/origem:** RF-11 / REQ-03.
- **Fluxo conhecido:** somente a ação de validar está declarada; critérios, dados e efeito não descritos.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

### UC-12 — Acompanhar retirada de materiais
- **Ator:** Responsável.
- **Objetivo declarado:** acompanhar retirada de materiais.
- **RF/origem:** RF-12 / REQ-03.
- **Fluxo conhecido:** somente o acompanhamento da retirada está declarado; participantes, eventos e dados não descritos.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

### UC-13 — Acompanhar devolução de materiais
- **Ator:** Responsável.
- **Objetivo declarado:** acompanhar devolução de materiais.
- **RF/origem:** RF-13 / REQ-03.
- **Fluxo conhecido:** somente o acompanhamento da devolução está declarado; participantes, eventos e dados não descritos.
- **Pré-condições:** não definidas.
- **Pós-condições:** não definidas.
- **Estados/transições:** não definidos.

## Extensão derivada de concorrência

RF-14 explicita a cobertura contextual de submissões concorrentes solicitada nesta especificação, derivada de RF-09/REQ-01. Não há ator, passos, resposta ou transições declarados para um caso de uso independente. O resultado verificável depende da definição de conflito/ocupação e da decisão de concorrência em Q-01, Q-02, Q-04 e Q-09 no [PRD](../prd.md#perguntas-abertas); não se presume um fluxo feliz.
