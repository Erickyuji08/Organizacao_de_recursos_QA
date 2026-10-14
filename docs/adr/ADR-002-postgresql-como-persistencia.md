# ADR-002 — PostgreSQL como persistência relacional

- **Status:** Aceito
- **Data:** 2026-10-14

## Contexto

A integridade de reservas concorrentes é o driver central. PostgreSQL é adotado como banco relacional principal. O modelo de dados ainda depende dos requisitos de domínio; portanto, esta decisão não aprova tabelas ou constraints específicas.

## Problema

Qual persistência e estratégia de acesso suportam dados relacionais, transações e proteção concorrente sem presumir que toda regra de recurso tenha a mesma representação?

## Requisitos relacionados

REQ-01; RF-04/RF-06/RF-07/RF-08/RF-09/RF-14; RN-01; Q-01/Q-02/Q-04/Q-08/Q-16.

## Alternativas

1. PostgreSQL com Spring Data JPA/Hibernate e migrações Flyway.
2. Outro RDBMS ou banco não relacional.
3. Banco em memória como persistência da aplicação.

## Decisão

Adotar PostgreSQL para persistência transacional e Spring Data JPA com Hibernate para o mapeamento relacional. Flyway e migrações SQL versionadas são a estratégia de evolução do esquema (detalhes em ADR-012). Usar constraints relacionais e índices onde o modelo aprovado permita. Transações são delimitadas na camada de aplicação/serviço. Hibernate não deve criar, atualizar ou remover esquema automaticamente; validar o esquema no runtime depois da migração.

Configurar desenvolvimento, testes e execução por propriedades Spring externas, sem credenciais versionadas:

- **Desenvolvimento local/Codespaces:** conexão com PostgreSQL por variáveis de ambiente e configuração local não sensível; credenciais de exemplo são apenas placeholders.
- **Testes de integração:** PostgreSQL real via Testcontainers, com ciclo de vida isolado pelo teste; não reutilizar credenciais ou instância de desenvolvimento. H2 não substitui essa evidência.
- **Execução implantada:** credenciais fornecidas pelo mecanismo de secrets/configuração do ambiente de execução, nunca por valores embutidos em arquivos versionados ou na imagem.

Separar as configurações por perfil/configuração Spring sem duplicar segredos. Hibernate não cria nem atualiza o esquema em nenhum ambiente; Flyway aplica migrações versionadas antes da validação do esquema.

## Justificativa

Transações, chaves estrangeiras e constraints do PostgreSQL suportam a integridade relacional. Testcontainers comprova o comportamento do mecanismo real. A camada JPA não substitui constraints, locks nem consultas explícitas necessárias à concorrência.

## Consequências positivas

- Transações relacionais locais e integridade referencial.
- Possibilidade de provar constraints, locks, migrações e isolamento com Testcontainers.
- Persistência e consultas de aplicação podem manter-se portáveis em nível de domínio, sem prometer portabilidade de SQL/constraints.

## Consequências negativas

- Acoplamento técnico a PostgreSQL e sua operação.
- Extensões/constraints opcionais podem exigir permissões e configuração; não fazem parte da garantia até validação do modelo.
- Material por quantidade não é protegido automaticamente por uma constraint simples de exclusão.

## Riscos

- Um modelo de ocupação incompleto pode proteger sala, mas não docente/material/manutenção.
- Escolher banco sem testar versões, migrações e recursos suportados pode bloquear implantação.
- Falha do banco é ponto de indisponibilidade sem RNF operacional definido.

## Estratégia de validação

Verificar a versão de PostgreSQL usada pelo ambiente de desenvolvimento e Testcontainers; executar migrações Flyway em banco vazio e validar transações/locks/constraints com PostgreSQL real. Hibernate deve falhar se o esquema não corresponder ao estado migrado.
