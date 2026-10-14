# Checklist de revisão arquitetural — Organização de Recursos

**Data:** 2026-10-14
**Status:** arquitetura documental ratificada para orientar o bootstrap futuro; PRD continua rascunho para regras de negócio.
**Escopo:** revisão de requisitos/decisões e alinhamento documental. Não houve código, provisionamento, migração, execução de testes ou CI.

Legenda: **Atendido documentalmente** = há proposta/evidência no documento; não significa validação operacional. **Parcial** = desenho existe, mas depende de decisão, experimento ou requisito aprovado. **Pendente** = não existe decisão/evidência suficiente.

## 1. Alinhamento de requisitos

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Atores e capacidades têm origem rastreável | Atendido documentalmente | REQ-01..REQ-04 em [stakeholders](stakeholders.md), [PRD](prd.md), personas e [casos de uso](requisitos/casos-de-uso.md) |
| Requisitos funcionais mapeados a componentes e testes planejados | Parcial | Matriz em [arquitetura](arquitetura.md#9-rastreabilidade-das-decisoes); testes ainda não existem |
| Regras centrais têm semântica verificável | Parcial | ADR-003/006 fixa intervalo, locks e estados técnicos; conflitos por tipo/regras funcionais seguem em Q-02/Q-04/Q-08 |
| Aprovação, administração, retirada/devolução cobrem fluxos completos | Pendente | Q-03/Q-05/Q-06/Q-07/Q-08/Q-15; somente capacidades estão declaradas |
| RNF têm métricas e gates | Parcial | ADR-010 define gates de engenharia (80% linhas, 70% branches e rastreabilidade de 100% dos requisitos críticos); criticidade ainda não foi classificada e SLOs/medidas operacionais continuam pendentes em Q-16 |
| Escopo de RF-14 e priorização relativa ratificados | Pendente | RF-14 é derivação contextual; “obrigatório” não determina prioridade de negócio |

## 2. Fundamentos arquiteturais

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Estilo e limites de componentes documentados | Atendido documentalmente | Monólito modular em camadas aceito em [ADR-001](adr/ADR-001-monolito-modular-em-camadas.md) |
| Diagramas de contexto, contêineres, componentes e sequência coerentes | Atendido documentalmente | [Contexto](diagramas/contexto.md), [contêineres](diagramas/conteineres.md), [componentes](diagramas/componentes.md), [sequências](diagramas/sequencias.md); alinhados aos ADRs, sem representar implementação existente |
| Dependências e fronteiras transacionais descritas | Atendido documentalmente | ADR-001/003/004 definem dependências e transações |
| API/contrato e cliente definidos | Parcial | REST/Problem Details decididos; endpoints não implementados e UI fora do baseline |
| Módulos opcionais não são tratados como baseline | Atendido documentalmente | Auditoria mínima é baseline (ADR-007); relatórios e notificações não foram aprovados (Q-12/Q-13). Retenção e acesso à auditoria seguem pendentes em Q-11 |

## 3. Stack e persistência

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Java 21/Spring Boot 3.x/Maven considerados | Atendido documentalmente | ADR-001 fixa Java 21, Spring Boot 3.x e Maven Wrapper |
| Banco escolhido e validado | Parcial | PostgreSQL/JPA/Hibernate aceitos em ADR-002; ainda não configurados/validados |
| Modelo de dados/esquema definido | Pendente | Depende de Q-01/Q-02/Q-04/Q-08; não criar migrações antes disso |
| Estratégia de migração definida | Atendido documentalmente | Flyway com SQL versionado em ADR-012; nenhum esquema criado |
| Backup/restore e disponibilidade operacional definidos | Pendente | Sem RNF/SLO, RTO ou RPO aprovados; cenário ATAM U-08 é estímulo de avaliação |

## 4. Concorrência, segurança e erros

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Dupla reserva tem estratégia de proteção definida | Atendido documentalmente | ADR-003 especifica locks ordenados e revalidação; prova PostgreSQL ainda não executada |
| Corridas reserva/manutenção cobertas | Parcial | Protocolo definido; política de manutenção aberta e teste planejado |
| Autenticação e autorização por objeto definidas | Parcial | Spring Security/JWT e menor privilégio em ADR-005; escopo fora do PRD permanece negado |
| Erros técnicos e de negócio distinguidos | Atendido documentalmente | Problem Details/mapeamento HTTP em ADR-009; não implementado |
| Segredos protegidos em ambientes | Parcial | Princípio registrado em ADR-011; configuração e validação ainda não existem |

## 5. Qualidade, operação e desenvolvimento

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Estratégia unitária, integração e API descrita | Atendido documentalmente | ADR-010; execução futura |
| PostgreSQL real usado nos testes de integração | Parcial | Testcontainers definido; ainda não configurado nem executado |
| CI existe e executa testes | Pendente | GitHub Actions e gates definidos; workflow não existe |
| Codespaces/devcontainer reproduzível | Parcial | Dev Container definido em ADR-011; não criado nesta etapa |
| Testes de concorrência e falha planejados | Atendido documentalmente | Utility tree/experimentos U-01..U-15 no [ciclo ATAM](avaliacao-arquitetural/ciclo-00.md) |
| UI ou cliente deve ser desenvolvido | Fora do baseline atual | Nenhum requisito de interface aprovado; confirmar escopo antes de desenhar UI |

## 6. ATAM e rastreabilidade

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Drivers, utility tree, cenários, sensibilidade, trade-offs e riscos | Atendido documentalmente | [Ciclo ATAM 00](avaliacao-arquitetural/ciclo-00.md); prioridades são provisórias, sem workshop |
| Riscos críticos ligados a experimentos | Atendido documentalmente | U-01/U-02/U-03/U-04/U-05/U-06/U-12 e matriz cenário→decisão→risco→experimento |
| Requisitos → drivers → ADR → componente → teste planejado | Atendido documentalmente | [Rastreabilidade das decisões](arquitetura.md#9-rastreabilidade-das-decisoes) |
| Requisitos sem cobertura ou condicionais explicitados | Atendido documentalmente | Seção 8 da [arquitetura](arquitetura.md#8-riscos-e-pendencias-de-produto), PRD e RTM |
| Decisões técnicas sem origem normativa explicitadas | Atendido documentalmente | [Rastreabilidade das decisões](arquitetura.md#9-rastreabilidade-das-decisoes) |
| Evidência de teste/CI registrada como execução real | Pendente | RTM mantém campos reservados vazios; nenhum teste ou CI foi executado |

## 7. Pendências antes da implementação funcional

- [ ] Refinar perguntas de produto ainda abertas antes das funcionalidades correspondentes.
- [ ] Classificar requisitos críticos para aplicar o gate de rastreabilidade de 100%.
- [ ] Executar testes PostgreSQL concorrentes e rollback antes de liberar reservas.
- [ ] Definir política institucional de retenção/leitura antes de dados reais.

**Resultado atual:** arquitetura documental ratificada para orientar o bootstrap futuro. As decisões de produto ainda abertas bloqueiam apenas funcionalidades dependentes delas. Nenhuma aplicação, infraestrutura, migration, teste ou CI foi implementado/executado nesta revisão.
