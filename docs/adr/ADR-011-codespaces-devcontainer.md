# ADR-011 — Ambiente Codespaces/devcontainer

- **Status:** Proposto (não aprovado)
- **Data:** 2026-10-14

## Contexto

Codespaces, Java 21, Spring Boot 3.x e Maven constam como contexto. Não há `.devcontainer/` no repositório, e não está especificado como PostgreSQL ou dependências de teste devem ser iniciados.

## Problema

Como tornar o ambiente de desenvolvimento reproduzível sem introduzir serviços, credenciais ou comportamento de produção por inferência?

## Requisitos relacionados

CTX-01/CT-01/CT-02/CT-03; RNF-01/RNF-05; Q-16.

## Alternativas

1. Configuração manual documentada por desenvolvedor.
2. Devcontainer para Codespaces com toolchain fixada e serviços de desenvolvimento/teste explicitados.
3. Container de aplicação e serviços completos de produção.

## Decisão

Propor `.devcontainer` para padronizar JDK 21 e Maven e documentar os comandos de build/teste. PostgreSQL local pode ser serviço de desenvolvimento somente após ratificação de ADR-002; não configurar deploy/produção neste container. Segredos devem ser fornecidos pelo gerenciador do ambiente, nunca commitados ou copiados para exemplos.

## Justificativa

CTX-01 associa o projeto a Codespaces. Configuração declarativa reduz divergência de ambiente, porém ainda é preciso validar imagem, recursos, extensões e lifecycle. A existência de contexto Codespaces não aprova nenhuma plataforma de execução de produção.

## Consequências positivas

- Setup previsível e menor dependência de instruções manuais.
- Base reproduzível para execução local e CI.

## Consequências negativas

- Devcontainer adiciona manutenção e tempo de provisionamento.
- PostgreSQL dentro/ao lado do Codespace demanda recursos e gestão de dados de teste.

## Riscos

- Segredos em configuração versionada ou logs.
- Dependência de imagem/extensão indisponível.
- Divergência entre container de desenvolvimento e ambiente real.

## Estratégia de validação

Após aprovação, criar configuração versionada sem credenciais, abrir Codespace limpo, confirmar Java/Maven, conexão local de teste e execução de testes; validar que secrets não aparecem em logs/arquivos. Comparar o ambiente com CI, não com produção inexistente.
