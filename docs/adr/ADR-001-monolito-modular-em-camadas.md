# ADR-001 — Monólito modular em camadas

- **Status:** Proposto (não aprovado)
- **Data:** 2026-10-14

## Contexto

O sistema precisa manter reservas sem conflitos (REQ-01), mas a definição de conflito, estados e concorrência está aberta. A stack contextual é Java 21, Spring Boot 3.x e Maven. Não há código nem necessidade de implantação independente demonstrada.

## Problema

Como organizar responsabilidades sem distribuir a consistência da agenda entre serviços, mantendo testabilidade, segurança e espaço para evolução?

## Requisitos relacionados

REQ-01; RF-01/RF-02/RF-09/RF-14; RNF-04; CT-01/CT-02/CT-03; Q-16.

## Alternativas

1. Monólito modular em camadas com domínio/casos de uso e adaptadores.
2. Monólito simples com regras em serviços e repositórios sem fronteiras de módulo.
3. Microserviços por recurso/capacidade.

## Decisão

Recomendar uma aplicação única Spring Boot organizada em módulos lógicos, com dependências direcionadas: API → casos de uso → domínio/portas; adaptadores implementam portas. Nenhum serviço distribuído, broker ou cache é parte do baseline. Limites finais de módulos serão confirmados conforme operações aprovadas.

## Justificativa

Uma aplicação permite transações locais para gravações que podem envolver reserva, alocações e manutenção. Serviços distribuídos introduziriam consistência e operação adicionais sem requisitos de escala/autonomia. O direcionamento das dependências favorece testes do domínio sem infraestrutura.

## Consequências positivas

- Transação local potencialmente simples para invariantes relacionadas.
- Separação de protocolo, regras e persistência.
- Implantação e testes iniciais menos complexos que microserviços.

## Consequências negativas

- Escala independente por capacidade não está disponível.
- A modularidade depende de disciplina contra acesso cruzado a dados e regras.
- Limites de módulos ainda podem exigir revisão após definição de operações.

## Riscos

- Modularização excessiva dos componentes opcionais.
- Acoplamento via banco compartilhado se os limites forem ignorados.
- A arquitetura não resolve por si a semântica de conflito nem concorrência (Q-01/Q-02/Q-04/Q-09).

## Estratégia de validação

Revisar dependências de módulos em teste/CI; demonstrar que a escrita de reserva e alocações participa de uma transação local; executar experimento concorrente PostgreSQL planejado em ADR-003. Ratificação arquitetural necessária antes da implementação.
