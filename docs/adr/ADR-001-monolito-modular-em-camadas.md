# ADR-001 — Monólito modular em camadas

- **Status:** Aceito
- **Data:** 2026-10-14

## Contexto

O sistema precisa manter reservas sem conflitos (REQ-01). A arquitetura definida nesta decisão é um monólito modular; as regras de domínio em aberto continuam nas decisões correspondentes, sem impedir a organização das camadas. A plataforma obrigatória é Java 21, Spring Boot 3.x e Maven.

## Problema

Como organizar responsabilidades e dependências sem distribuir a consistência da agenda entre serviços, mantendo testabilidade e evitando abstrações sem justificativa concreta?

## Requisitos relacionados

REQ-01; RF-01/RF-02/RF-09/RF-14; RNF-04; CT-01/CT-02/CT-03; Q-16.

## Alternativas

1. Monólito modular organizado por domínio funcional e separado em camadas.
2. Monólito sem fronteiras de módulo explícitas.
3. Microserviços por recurso/capacidade.

## Decisão

Adotar uma aplicação única Spring Boot 3.x organizada prioritariamente por domínio funcional, com camadas de apresentação/API, aplicação, domínio e infraestrutura. A direção das dependências é API → aplicação → domínio/portas; a infraestrutura implementa as portas consumidas pela aplicação. O domínio não depende do Spring, da API nem da persistência. A API recebe e retorna DTOs, sem expor entidades JPA. Regras de negócio ficam em domínio/aplicação, nunca em controllers ou adaptadores de persistência.

Módulos só expõem contratos necessários; um módulo não acessa repositórios ou tabelas internas de outro. Dependências entre módulos devem formar um grafo acíclico. Evitar interfaces e abstrações genéricas sem consumidor ou justificativa concreta. Não adotar CQRS, event sourcing, mensageria distribuída ou microserviços sem requisito que os justifique.

Usar Java 21 LTS, Maven Wrapper e a versão estável mais recente compatível da linha Spring Boot 3.x disponível no momento do bootstrap. Fixar a versão exata e verificar compatibilidade de plugins e dependências no build.

## Justificativa

Uma aplicação permite transações locais para gravações que envolvem agenda e seus recursos. A separação por domínio e camadas preserva limites sem antecipar separação de deploy. DTOs limitam o acoplamento do contrato HTTP ao modelo persistido; evitar interfaces sem necessidade mantém a solução simples.

## Consequências positivas

- Transação local potencialmente simples para invariantes relacionadas.
- Separação de protocolo, regras e persistência.
- Implantação e testes iniciais menos complexos que microserviços.

## Consequências negativas

- Escala independente por capacidade não está disponível.
- A modularidade depende de disciplina e verificações contra ciclos e acesso cruzado a dados/regras.
- Os limites internos poderão ser refinados quando operações de produto forem aprovadas, sem mudar o estilo arquitetural.

## Riscos

- Modularização excessiva dos componentes opcionais.
- Acoplamento via banco compartilhado se os limites forem ignorados.
- A arquitetura não substitui a semântica de conflito nem a proteção concorrente definida em ADR-003.

## Estratégia de validação

Validar dependências acíclicas entre módulos; testar regras de domínio sem Spring; verificar que controllers e persistência não contêm regras de negócio; e confirmar que operações de agenda usam transações locais. O bootstrap deverá fixar a versão compatível de Spring Boot 3.x e do Maven Wrapper.
