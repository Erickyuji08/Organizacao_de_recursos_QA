# ADR-005 — Autenticação e autorização

- **Status:** Pendente (Q-10)
- **Data:** 2026-10-14

## Contexto

Os três perfis e suas atribuições estão declarados, mas não há autenticação, provedor de identidade, matriz de permissões, identidade de proprietário ou regras de acesso por objeto aprovadas. As atribuições não podem ser tratadas como política de autorização.

## Problema

Como autenticar a identidade e controlar acesso por operação e objeto sem inferir permissões dos nomes dos perfis?

## Requisitos relacionados

REQ-02/REQ-03/REQ-04; RF-02/RF-03/RF-10; RN-02/RN-03/RN-04; RNF-04; Q-10.

## Alternativas

1. Provedor de identidade externo com OIDC/OAuth.
2. Identidade gerenciada pela própria aplicação.
3. Autenticação por sessão ou por token, a escolher conforme cliente e ambiente.
4. Adiar controle de acesso (não aceitável para mutações expostas).

## Decisão

Pendente de Q-10. Direção de segurança a validar: negar por padrão, autenticar antes de mutações e autorizar cada operação e objeto no caso de uso/consulta; validar propriedade da reserva no servidor. Spring Security é candidato tecnológico, não decisão aprovada. Provedor, sessão/token e matriz não estão escolhidos.

## Justificativa

POL-01 pede segurança, enquanto REQ-02 usa “suas reservas” sem definir como a posse é determinada. Uma implementação usando atribuições textuais como matriz criaria regras não aprovadas e risco de IDOR.

## Consequências positivas

- Destaca autorização por objeto e controles negativos como requisitos de desenho a satisfazer.
- Evita vincular perfil funcional a privilégios ainda não decididos.

## Consequências negativas

- Sem Q-10 não há fluxo de login, teste de aceite ou política final.
- Integração de identidade adiciona configuração e responsabilidade operacional.

## Riscos

- Acesso indevido por autorização apenas na interface/controller.
- Vazamento de dados em consultas ou respostas de objeto alheio.
- Configuração local/Codespaces pode expor credenciais se segredos forem versionados.

## Estratégia de validação

Aprovar matriz perfil/operação/objeto e modelo de identidade; revisar ameaças; testar anônimo, papel incorreto, proprietário correto/incorreto e administrador/responsável conforme a matriz via testes de serviço e API. Verificar ausência de mutação em negativas e não exposição de segredos.
