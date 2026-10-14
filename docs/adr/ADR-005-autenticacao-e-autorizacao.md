# ADR-005 — Autenticação e autorização

- **Status:** Aceito (matriz de operações não declaradas permanece fora do escopo)
- **Data:** 2026-10-14

## Contexto

O PRD declara três perfis e suas capacidades, mas não fornecedor de identidade nem credenciais. A API precisa autenticar e autorizar no servidor, com negação por padrão e menor privilégio. As atribuições textuais não autorizam operações adicionais não mencionadas.

## Problema

Como emitir e validar identidade stateless para a API e autorizar operações declaradas por perfil e propriedade, sem adicionar um provedor externo não requerido?

## Requisitos relacionados

REQ-02/REQ-03/REQ-04; RF-02/RF-03/RF-10; RN-02/RN-03/RN-04; RNF-04; Q-10.

## Alternativas

1. Provedor externo de identidade OIDC.
2. Spring Security com JWT de acesso stateless, emitido e validado pela aplicação.
3. Sessões stateful.

## Decisão

Adotar Spring Security e JWTs de acesso stateless. A aplicação emite tokens assinados e valida assinatura, emissor, audiência e expiração usando componentes JWT compatíveis com Spring Security; não confiar em claims não verificadas. Usar assinatura assimétrica RS256 com chave privada apenas no emissor e chave pública no validador; guardar ambas em configuração externa/secret store, nunca no repositório ou logs. Incluir `kid` para suportar rotação; validar somente chaves publicadas no conjunto de confiança configurado.

Tokens contêm somente `sub`, papéis necessários, `iss`, `aud`, `iat` e `exp`, sem dados pessoais ou segredos. Expiração máxima do token de acesso: 15 minutos. Não criar refresh tokens nesta etapa. Credenciais locais, se forem escolhidas no bootstrap, armazenam-se com BCrypt e comparação segura; nenhuma senha é reversível.

Mapear papéis para capacidades literalmente atribuídas no PRD: Solicitante pode consultar disponibilidade e gerenciar somente suas próprias reservas; Responsável pode aprovar solicitações especiais, validar alocação docente e acompanhar retirada/devolução; Administrador pode gerenciar somente as categorias enumeradas no RF-03..RF-08. A matriz de operações CRUD e o escopo detalhado além dessas frases não são inferidos. Verificar propriedade/escopo no servidor e no caso de uso/consulta; ausência de regra explícita significa negar. Não confiar em ocultação na interface.

Esta decisão não aprova cadastro público, recuperação de credenciais, gestão de contas ou provedor de identidade externo. A origem/provisionamento da identidade, credenciais e o fluxo de emissão do token devem ser especificados antes da implementação de autenticação, sem ampliar as capacidades de produto do PRD.

## Justificativa

REQ-02 exige “suas reservas”, portanto a propriedade deve derivar da identidade autenticada no servidor, nunca de um identificador fornecido pelo cliente sem validação. JWTs curtos e assimétricos limitam exposição e permitem validação local sem chamada externa por requisição. O controle por papéis é restrito às atribuições declaradas.

## Consequências positivas

- Destaca autorização por objeto e controles negativos como requisitos de desenho a satisfazer.
- Evita vincular perfil funcional a privilégios ainda não decididos.

## Consequências negativas

- Emissão e rotação de chaves exigem configuração segura fora do repositório.
- A matriz detalhada para operações que o PRD não define permanece sem implementação até decisão de produto.

## Riscos

- Acesso indevido por autorização apenas na interface/controller.
- Vazamento de dados em consultas ou respostas de objeto alheio.
- Configuração local/Codespaces pode expor credenciais se segredos forem versionados.

## Estratégia de validação

Testar tokens expirados, assinatura inválida, emissor/audiência incorretos, rotação de `kid`, usuário anônimo, papel insuficiente e acesso a reserva de outro proprietário. Verificar que negativas não mutam estado e que logs/respostas não contêm JWT, chave, senha ou dados pessoais sensíveis. O bootstrap deverá provar carregamento de chave por secret/config externo.
