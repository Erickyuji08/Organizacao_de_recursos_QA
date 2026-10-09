# Visão geral do produto

## Propósito

O Projeto Organização de Recursos é descrito como um sistema para gerenciamento de salas, professores e materiais, com reservas sem conflitos de horários. Este propósito registra o escopo do enunciado, sem definir como a ausência de conflitos será obtida.

## Escopo declarado

O escopo documentado limita-se às capacidades e aos perfis expressamente mencionados na fonte abaixo. Não são especificados processos, regras detalhadas, políticas, integrações ou solução técnica.

## Perfis

- **Solicitante:** professor ou coordenador que consulta disponibilidade e gerencia suas reservas.
- **Responsável:** aprova solicitações especiais, valida alocação de docentes e acompanha retirada e devolução de materiais.
- **Administrador:** gerencia usuários, salas, professores, materiais, bloqueios e períodos de manutenção.

## Capacidades declaradas

- Gerenciamento de salas, professores e materiais.
- Reservas sem conflitos de horários.
- Consulta de disponibilidade e gerenciamento das próprias reservas pelo solicitante.
- Aprovação de solicitações especiais, validação da alocação de docentes e acompanhamento de retirada e devolução de materiais pelo responsável.
- Gerenciamento de usuários, salas, professores, materiais, bloqueios e períodos de manutenção pelo administrador.

As formulações acima resumem o enunciado; não estabelecem sequência de fluxo, regra de aprovação, prioridade, política ou comportamento não descritos.

## Fonte e base documental

A única fonte de requisitos disponível para este documento é o enunciado fornecido pelo usuário nesta tarefa. O `README.md` existente contém somente o título do projeto. As instruções e documentos existentes do AIOX descrevem o framework e não fornecem requisitos do produto.

Os IDs abaixo são identificadores documentais atribuídos aqui para rastreabilidade; não existiam na fonte original:

- [**REQ-01**](stakeholders.md#req-01-projeto-organizacao-de-recursos): “Projeto Organização de Recursos: sistema para gerenciamento de salas, professores e materiais, com reservas sem conflitos de horários.”
- [**REQ-02**](stakeholders.md#req-02-solicitante): “Solicitante: professor ou coordenador que consulta disponibilidade e gerencia suas reservas.”
- [**REQ-03**](stakeholders.md#req-03-responsavel): “Responsável: aprova solicitações especiais, valida alocação de docentes e acompanha retirada e devolução de materiais.”
- [**REQ-04**](stakeholders.md#req-04-administrador): “Administrador: gerencia usuários, salas, professores, materiais, bloqueios e períodos de manutenção.”

## Hipóteses

Nenhuma hipótese de produto foi adotada. Pontos não informados pelo enunciado não devem ser tratados como regras.

## Perguntas em aberto

- Como se define e verifica “sem conflitos de horários”?
- O que caracteriza uma “solicitação especial” e quais critérios se aplicam à aprovação?
- O que significa validar a alocação de docentes e quais informações são consideradas?
- O que envolve acompanhar retirada e devolução de materiais?
- Quais operações estão incluídas em “gerenciar” cada tipo de recurso ou registro?
- Como se relacionam os perfis e quais fluxos, canais ou integrações existem?
- Quais dificuldades específicas os perfis enfrentam?

O enunciado não responde a essas perguntas. Elas ficam registradas como lacunas, sem pressupor respostas ou acrescentar requisitos.
