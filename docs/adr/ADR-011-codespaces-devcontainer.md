# ADR-011 — Ambiente Codespaces/devcontainer

- **Status:** Aceito (implementação futura)
- **Data:** 2026-10-14

## Contexto

Codespaces, Java 21, Spring Boot 3.x e Maven são o ambiente de desenvolvimento requerido. A configuração versionada ainda não existe e esta decisão não a cria.

## Problema

Como reproduzir toolchain e testes no Codespaces sem versionar credenciais ou sugerir que o armazenamento local é backup permanente?

## Requisitos relacionados

CTX-01/CT-01/CT-02/CT-03; RNF-01/RNF-05; Q-16.

## Alternativas

1. Configuração manual por desenvolvedor.
2. Dev Container mínimo com Java 21, ferramentas Maven e suporte a Docker/Testcontainers.
3. Container de desenvolvimento que também provisiona ambiente de produção.

## Decisão

Adotar Dev Container mínimo para padronizar Java 21, Maven Wrapper e ferramentas compatíveis com Docker/Testcontainers. O desenvolvimento e os testes usam PostgreSQL; a implementação posterior pode oferecer serviço local Compose com volume nomeado persistente em reconstruções normais. O volume pertence ao Codespace e não é backup: exclusão do Codespace pode eliminar volume, dados e configuração local; nenhum dado local substitui backup externo. Testes devem criar bancos efêmeros em Testcontainers.

Configurações Spring externas e credenciais entram por variáveis de ambiente/gerenciador de secrets do Codespaces; não gravar credenciais pessoais no Dev Container, `devcontainer.json`, Compose ou arquivos versionados. Não configurar serviços ou deploy de produção nesta etapa.

## Justificativa

O Dev Container reduz divergência para participantes e pode ser validado em Codespaces limpo. Persistência local facilita reconstruções usuais, mas não promete recuperação após a exclusão do Codespace.

## Consequências positivas

- Setup previsível e menor dependência de instruções manuais.
- Base reproduzível para execução local e CI.

## Consequências negativas

- Devcontainer adiciona manutenção e tempo de provisionamento.
- PostgreSQL e volume local consomem recursos; volume local é efêmero em relação à vida útil do Codespace.

## Riscos

- Segredos em configuração versionada ou logs.
- Perda de dados locais quando o Codespace é excluído.
- Dependência de imagem/extensão indisponível ou divergência em relação ao ambiente real.

## Estratégia de validação

Na etapa de bootstrap, abrir Codespace limpo, confirmar Java 21/Maven Wrapper, Docker/Testcontainers e conexão PostgreSQL; verificar que secrets não aparecem em arquivos/logs. Testar reconstrução mantendo o volume e documentar explicitamente que excluir o Codespace pode perder esses dados.
