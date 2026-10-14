# Checklist de revisão arquitetural — Organização de Recursos

**Data:** 2026-10-14
**Status:** revisão documental concluída; arquitetura **não aprovada para implementação**.
**Escopo:** requisitos, decisões, diagramas, ATAM, rastreabilidade, riscos e preparação técnica. Não houve código, provisionamento, migração, execução de testes ou CI.

Legenda: **Atendido documentalmente** = há proposta/evidência no documento; não significa validação operacional. **Parcial** = desenho existe, mas depende de decisão, experimento ou requisito aprovado. **Pendente** = não existe decisão/evidência suficiente.

## 1. Alinhamento de requisitos

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Atores e capacidades têm origem rastreável | Atendido documentalmente | REQ-01..REQ-04 em [stakeholders](stakeholders.md), [PRD](prd.md), personas e [casos de uso](requisitos/casos-de-uso.md) |
| Requisitos funcionais mapeados a componentes e testes planejados | Parcial | Matriz em [arquitetura](arquitetura.md#9-matriz-de-rastreabilidade-arquitetural); testes ainda não existem |
| Regras centrais têm semântica verificável | Pendente | Q-01/Q-02/Q-04/Q-09; RN-01/RF-09/RF-14 |
| Aprovação, administração, retirada/devolução cobrem fluxos completos | Pendente | Q-03/Q-05/Q-06/Q-07/Q-08/Q-15; somente capacidades estão declaradas |
| RNF têm métricas e gates | Pendente | RNF-01..RNF-05 sem critérios operacionais; Q-16 |
| Escopo de RF-14 e priorização relativa ratificados | Pendente | RF-14 é derivação contextual; “obrigatório” não determina prioridade de negócio |

## 2. Fundamentos arquiteturais

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Estilo e limites de componentes documentados | Parcial | Monólito modular em camadas proposto; [ADR-001](adr/ADR-001-monolito-modular-em-camadas.md) não aprovado |
| Diagramas de contexto, contêineres, componentes e sequência coerentes | Parcial | [Contexto](diagramas/contexto.md), [contêineres](diagramas/conteineres.md), [componentes](diagramas/componentes.md), [sequências](diagramas/sequencias.md); todos são propostas |
| Dependências e fronteiras transacionais descritas | Parcial | ADR-004 e diagramas descrevem transação local candidata |
| API/contrato e cliente definidos | Pendente | API HTTP é proposta, não contrato aprovado; não há requisito de UI/canal |
| Módulos opcionais não são tratados como baseline | Atendido documentalmente | Auditoria, relatórios e notificações estão condicionados a Q-11/Q-12/Q-13 |

## 3. Stack e persistência

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Java 21/Spring Boot 3.x/Maven considerados | Atendido documentalmente | CT-01..CT-03; versão menor e dependências não fixadas |
| Banco escolhido e validado | Pendente | PostgreSQL é candidato em ADR-002; nenhum banco configurado |
| Modelo de dados/esquema definido | Pendente | Depende de Q-01/Q-02/Q-04/Q-08; não criar migrações antes disso |
| Estratégia de migração definida | Pendente | ADR-012; Flyway/Liquibase não selecionados |
| Backup/restore e disponibilidade operacional definidos | Pendente | Sem RNF/SLO, RTO ou RPO aprovados; cenário ATAM U-08 é estímulo de avaliação |

## 4. Concorrência, segurança e erros

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Dupla reserva tem proteção final no banco além de consulta | Parcial | Direção registrada em ADR-003; mecanismo aguarda Q-01/Q-02/Q-04/Q-08/Q-09 e experimento |
| Corridas reserva/manutenção cobertas | Pendente | Q-08 e ADR-003; teste multi-conexão ainda planejado |
| Autenticação e autorização por objeto aprovadas | Pendente | Q-10 e ADR-005; perfis não constituem matriz de permissões |
| Erros técnicos e de negócio distinguidos | Parcial | Mapeamento proposto em ADR-009; contrato, status e corpo não aprovados |
| Segredos protegidos em ambientes | Parcial | Princípio registrado em ADR-011; configuração e validação ainda não existem |

## 5. Qualidade, operação e desenvolvimento

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Estratégia unitária, integração e API descrita | Parcial | ADR-010; ferramentas/gates ainda dependem de Q-16 |
| PostgreSQL real usado nos testes de integração | Pendente | Testcontainers solicitado e planejado; ainda não configurado |
| CI existe e executa testes | Pendente | CTX-02 cita CI, mas não há `.github/workflows/` |
| Codespaces/devcontainer reproduzível | Pendente | Codespaces consta como contexto; não há `.devcontainer/` |
| Testes de concorrência e falha planejados | Atendido documentalmente | Utility tree/experimentos U-01..U-15 no [ciclo ATAM](avaliacao-arquitetural/ciclo-00.md) |
| UI ou cliente deve ser desenvolvido | Fora do baseline atual | Nenhum requisito de interface aprovado; confirmar escopo antes de desenhar UI |

## 6. ATAM e rastreabilidade

| Verificação | Estado | Evidência / observação |
|---|---|---|
| Drivers, utility tree, cenários, sensibilidade, trade-offs e riscos | Atendido documentalmente | [Ciclo ATAM 00](avaliacao-arquitetural/ciclo-00.md); prioridades são provisórias, sem workshop |
| Riscos críticos ligados a experimentos | Atendido documentalmente | U-01/U-02/U-03/U-04/U-05/U-06/U-12 e matriz cenário→decisão→risco→experimento |
| Requisitos → drivers → ADR → componente → teste planejado | Atendido documentalmente | [Matriz arquitetural](arquitetura.md#9-matriz-de-rastreabilidade-arquitetural) |
| Requisitos sem cobertura ou condicionais explicitados | Atendido documentalmente | Seção “Cobertura ausente ou condicional” da arquitetura |
| Decisões técnicas sem origem normativa explicitadas | Atendido documentalmente | Seção “Decisões propostas sem origem normativa direta” da arquitetura |
| Evidência de teste/CI registrada como execução real | Pendente | RTM mantém campos reservados vazios; nenhum teste ou CI foi executado |

## 7. Critérios de saída para uma futura aprovação

- [ ] Aprovar Q-01, Q-02, Q-04 e Q-09 e ratificar ou retirar RF-14 do baseline.
- [ ] Aprovar Q-10 e matriz de acesso; revisar ameaças e controles por objeto.
- [ ] Aprovar Q-03, Q-05, Q-06, Q-07, Q-08, Q-14 e Q-15 para os fluxos afetados.
- [ ] Decidir Q-11/Q-12/Q-13 antes de implementar auditoria, relatórios ou notificações.
- [ ] Decidir critérios verificáveis de RNF e gates em Q-16.
- [ ] Ratificar ADRs técnicos propostos e fechar os pendentes com evidências.
- [ ] Validar constraints/transações com PostgreSQL/Testcontainers em concorrência e falhas relevantes.
- [ ] Atualizar RTM com IDs de testes e evidências reais somente depois de execução.

**Resultado atual:** **não aprovado para implementação**. Esta avaliação permite planejar uma sequência futura, mas não fecha decisões de produto nem inicia implementação.
