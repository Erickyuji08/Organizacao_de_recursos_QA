# ADR-009 — Tratamento de erros HTTP

- **Status:** Proposto (não aprovado)
- **Data:** 2026-10-14

## Contexto

API HTTP é uma opção arquitetural, não contrato de interface aprovado. A sequência sugere 409 para conflito como candidato; Q-09 não escolhe resultado por requisição. Códigos para entrada inválida, autenticação e autorização também não foram definidos.

## Problema

Como comunicar erros sem expor detalhes internos nem converter erro técnico em sucesso ou em regra de negócio não aprovada?

## Requisitos relacionados

RF-01/RF-02/RF-09/RF-14; RNF-04; Q-09/Q-10.

## Alternativas

1. Respostas específicas ad hoc por controller.
2. Mapeamento centralizado de erros de domínio/aplicação/infraestrutura para um envelope HTTP estável.
3. Propagar exceções/stack traces de infraestrutura.

## Decisão

Propor mapeador centralizado na borda HTTP, com categorias distintas para validação, conflito de domínio, autenticação/autorização e falha inesperada. 409 é candidato para conflito de integridade, sujeito a Q-09; 400 versus 422 e 403 versus 404 ficam pendentes de contrato/Q-10. Nunca expor SQL, stack trace, nomes internos de constraints ou segredos.

## Justificativa

Mapeamento único facilita previsibilidade e testes. A decisão não deve eleger vencedor ou estado concorrente, que é política de produto.

## Consequências positivas

- Respostas consistentes e testáveis.
- Reduz risco de vazamento de detalhes internos.
- Separa falha do cliente de falha inesperada do servidor.

## Consequências negativas

- Contrato precisa ser aprovado antes de clientes dependerem dele.
- Erros de persistência precisam ser classificados sem mascarar falhas reais.

## Riscos

- Converter falhas diversas em 409 pode ocultar defeito operacional.
- Usar 404 para esconder existência de objeto pode afetar diagnóstico/contrato.
- Códigos de status sem corpo estável podem não atender cliente futuro.

## Estratégia de validação

Definir contrato de erro junto à API; testar mapeamento por MockMvc para entradas inválidas, conflito de domínio, acesso negado e falha inesperada. Verificar que respostas não contêm dados técnicos sensíveis e que cada conflito corresponde à política aprovada.
