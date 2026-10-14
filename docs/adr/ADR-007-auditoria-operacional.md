# ADR-007 — Auditoria operacional

- **Status:** Aceito (prazo de retenção depende de política institucional)
- **Data:** 2026-10-14

## Contexto

O PRD declara capacidades de administração, aprovação e acompanhamento, mas não exige explicitamente uma trilha de auditoria. Este ADR registra uma decisão arquitetural de rastreabilidade mínima para mutações que venham a ser aprovadas e implementadas; não cria operações de produto. Auditoria não substitui RTM nem logs técnicos. O período de retenção não está definido por requisito ou política institucional.

## Problema

Como registrar o ator, a ação, a entidade afetada, data/hora e resultado sem armazenar credenciais, tokens ou payloads sensíveis?

## Requisitos relacionados

RNF-03 (rastreabilidade documental, distinta); RF-03/RF-04/RF-05/RF-06/RF-07/RF-08/RF-10/RF-12/RF-13; Q-07/Q-11.

## Alternativas

1. Sem trilha operacional.
2. Auditoria mínima das mutações administrativas e mudanças de estado/decisões descritas no PRD.
3. Captura ampla de consultas e payloads.

## Decisão

Registrar atomicamente com a operação os eventos de criação/alteração de usuários, salas, professores, materiais, bloqueios/manutenção e reservas, transições de estado, decisões de aprovação e ações de retirada/devolução que venham a ser implementadas. Cada evento contém apenas identificador do ator, ação, tipo e identificador da entidade, data/hora UTC e resultado; payload integral, senha, JWT, chave e segredo são proibidos. Falha ao persistir o evento reverte a mutação correspondente. Não auditar consultas indiscriminadamente sem requisito. Logs técnicos são estruturados quando úteis, com redação de dados sensíveis, e não substituem essa trilha.

Não apagar eventos automaticamente enquanto a política institucional de retenção não for aprovada; não afirmar retenção legal nem imutabilidade criptográfica. A duração, acesso de leitura e tratamento de dados pessoais permanecem pendências institucionais e devem ser definidos antes de operação com dados reais.

## Justificativa

Uma trilha mínima para mutações já aprovadas e implementadas facilita identificar ator, ação, entidade, instante e resultado sem coletar payloads desnecessários. A decisão é uma salvaguarda arquitetural, não uma nova capacidade de produto nem autorização para presumir CRUD. Atomicidade evita uma mutação coberta confirmada sem seu evento correspondente.

## Consequências positivas

- Permite identificar ator, ação, entidade, instante e resultado das mutações administrativas e decisões cobertas.
- Mantém correspondência transacional entre mudança e trilha.

## Consequências negativas

- Falha de armazenamento/auditoria impede a mutação coberta e reduz disponibilidade.
- Sem prazo institucional, há risco de retenção excessiva; a política é bloqueadora antes de produção.

## Riscos

- Registrar dados excessivos/sensíveis ou manter dados além do prazo institucional.
- Confundir log mutável com trilha imutável ou controle de não repúdio.
- Confundir auditoria com a RTM e rastreabilidade dos requisitos.

## Estratégia de validação

Injetar falha na gravação do evento e provar rollback da mutação. Testar conteúdo mínimo, identidade/resultado e ausência de segredos em eventos/logs. Antes de dados reais, obter política institucional de retenção e autorização de leitura; não inventar prazo legal.
