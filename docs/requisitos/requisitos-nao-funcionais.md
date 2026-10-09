# Requisitos não funcionais e restrições

**Status:** rascunho para validação. Os documentos de origem não fixam métricas operacionais; nenhum SLA ou limiar foi inferido.

## Fontes e IDs

- **CTX-01** (ID documental atribuído nesta especificação): contexto inicial do usuário: “repositório Codespaces; projeto adotará Java 21, Spring Boot 3.x e Maven”.
- **CTX-02** (ID documental atribuído): contexto inicial do usuário: “aplicação de testes automatizados, TDD/BDD, ATAM, rastreabilidade e CI”.
- **POL-01** (ID documental atribuído): regra inicial do usuário: “preservar responsabilidades, segurança, testabilidade e clareza”.
- **POL-02** (ID documental atribuído): regra inicial do usuário: “relacionar requisitos, regras, decisões e testes por identificadores rastreáveis”.
- **POL-03** (ID documental atribuído): usar documentos aprovados como fonte.
- **POL-04** (ID documental atribuído): não inventar requisitos/regras e registrar divergências.
- **POL-05** (ID documental atribuído): sem arquitetura nesta etapa.

Esses IDs são atribuídos aqui para rastreabilidade; não são IDs preexistentes na fonte. REQ-01 a REQ-04 são requisitos de negócio em [stakeholders](../stakeholders.md), não fontes de limiares de qualidade.

## Requisitos de qualidade/processo com fonte

### RNF-01 — Testes automatizados e práticas TDD/BDD

- **Declaração:** o contexto estabelece aplicação de testes automatizados e TDD/BDD.
- **Origem:** CTX-02.
- **Critério/medida:** processo e cobertura, escopo de testes, proporção, ferramentas, limiar de cobertura e gate não especificados. Não é possível derivar um critério operacional quantitativo. **Não pronto para verificação de conformidade até Q-16.**

### RNF-02 — Consideração de ATAM

- **Declaração:** o contexto cita ATAM. Registrar como método/processo de avaliação a considerar; não define atributos de qualidade escolhidos nem exige resultado ou etapa específica além da menção.
- **Origem:** CTX-02.
- **Critério/medida:** escopo, momento, participantes, evidências e critérios de conclusão não especificados. **Não pronto para verificação operacional até Q-16.**

### RNF-03 — Rastreabilidade por identificadores

- **Declaração:** relacionar requisitos, regras, decisões e testes por identificadores rastreáveis.
- **Origem:** CTX-02 e POL-02.
- **Critério/medida:** relação documental está definida como objetivo; ferramenta, granularidade, completude exigida e evidência de atualização não especificadas. **Critério operacional pendente de Q-16.**

### RNF-04 — Preservar responsabilidades, segurança, testabilidade e clareza

- **Declaração:** preservar as quatro qualidades/princípios citados, sem reinterpretá-los como níveis mensuráveis ou políticas específicas.
- **Origem:** POL-01.
- **Critério/medida:** definição de responsabilidades, modelo/ameaças de segurança, práticas de testabilidade e critérios de clareza não especificados. Nenhum padrão, controle, métrica ou limiar de segurança é presumido. **Não pronto para verificação objetiva até Q-16.**

### RNF-05 — Integração a CI

- **Declaração:** o contexto inclui CI.
- **Origem:** CTX-02.
- **Critério/medida:** plataforma, verificações executadas, eventos de execução, condição de falha e limiares não especificados. **Não pronto para verificação operacional até Q-16.**

## Restrições/contexto tecnológico (não são RNF nem decisões arquiteturais desta etapa)

- **CT-01:** Java 21 — origem CTX-01.
- **CT-02:** Spring Boot 3.x — origem CTX-01.
- **CT-03:** Maven — origem CTX-01.

As três restrições são contexto fornecido pelo usuário. Não se seleciona versão menor de Spring Boot, dependência, estrutura, implantação, arquitetura, banco ou configuração.

## Qualidades de produto não especificadas

Não há requisitos de produto explícitos com metas para disponibilidade, desempenho/latência, capacidade, escalabilidade, segurança mensurável, privacidade, acessibilidade, usabilidade, compatibilidade, resiliência, recuperação, portabilidade ou retenção. POL-01 cita segurança, testabilidade e clareza como princípios, mas não define padrões nem critérios operacionais; isso não autoriza inventá-los. A ausência de metas permanece registrada, sem transformar a lista em requisitos.

## Perguntas abertas

### Q-16 — Medidas e verificação operacional

Que evidências tornam verificáveis RNF-01 a RNF-05? **Alternativas não decididas:** definir métricas, processos e evidências explícitos; adotar verificação qualitativa documentada sem limiar numérico; declarar algum item não aplicável. Para cada RNF, faltam escopo, responsável, momento e critério de conclusão. Não há default.

Outros atributos de qualidade de produto podem ser discutidos como requisitos novos, mas precisam de fonte/aprovação antes de entrar no escopo; a lista de lacunas acima não é uma solicitação implícita para implementá-los.
