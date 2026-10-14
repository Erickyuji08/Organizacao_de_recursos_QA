# ADR-009 — Tratamento de erros HTTP

- **Status:** Aceito para o contrato planejado da API
- **Data:** 2026-10-14

## Contexto

API REST é o canal de integração aprovado para o backend. O contrato de endpoints e respostas ainda será implementado; a semântica central de conflito é coberta por ADR-003.

## Problema

Como padronizar validação, autorização, conflito e falha inesperada, sem vazar detalhes internos?

## Requisitos relacionados

RF-01/RF-02/RF-09/RF-14; RNF-04; Q-09/Q-10.

## Alternativas

1. Respostas ad hoc por controller.
2. Mapeamento centralizado para Problem Details (RFC 9457).
3. Propagar exceções ou stack traces de infraestrutura.

## Decisão

Usar rotas REST versionadas sob `/api/v1`, DTOs de entrada/saída e validação de entrada na borda, mantendo regras nos casos de uso/domínio. Mapear exceções centralmente para `application/problem+json` (RFC 9457), com tipo, título, status, detalhe seguro e identificador de correlação; nunca incluir SQL, stack trace, nomes internos de constraints, segredos, tokens ou dados pessoais desnecessários.

Usar `400` para entrada inválida, `401` para autenticação ausente/inválida, `403` para papel sem permissão, `404` para recurso inexistente ou fora do escopo quando revelar existência não for apropriado, `409` para conflito de agenda/estado e `500` genérico para falha inesperada. Erro técnico de banco só vira `409` quando classificado como violação de conflito; não mascarar indisponibilidade ou defeitos como conflito. Listagens que precisem de paginação usam parâmetros `page` e `size`, ordenação estável e limite máximo documentado no contrato implementado. Documentar API com OpenAPI/Swagger quando as dependências compatíveis forem adicionadas; a documentação não afirma endpoints não implementados.

## Justificativa

Mapeamento único torna clientes e testes previsíveis. `409` comunica que o pedido não pode ser confirmado contra o estado atual, sem afirmar qual pedido concorrente tem prioridade.

## Consequências positivas

- Respostas consistentes e testáveis.
- Reduz risco de vazamento de detalhes internos.
- Separa falha do cliente de falha inesperada do servidor.

## Consequências negativas

- Clientes devem tratar erros Problem Details e não depender de mensagens internas.
- A classificação de exceções de persistência deve distinguir conflitos reais de falhas técnicas.

## Riscos

- Converter falhas diversas em 409 pode ocultar defeito operacional.
- Usar 404 para esconder existência de objeto pode afetar diagnóstico/contrato.
- Códigos de status sem corpo estável podem não atender cliente futuro.

## Estratégia de validação

Testar via MockMvc os códigos e tipos Problem Details para validação, autenticação, autorização, conflito e falha inesperada. Verificar ausência de dados técnicos/sensíveis e que falhas técnicas não sejam convertidas em `409`.
