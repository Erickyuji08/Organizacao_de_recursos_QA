# C4 — Contexto do sistema

**Status:** proposta arquitetural; atores e capacidades vêm de REQ-01 a REQ-04. O canal de interação não foi especificado. Não há integrações externas aprovadas. Autenticação/autorização depende de Q-10 ([ADR-005](../adr/ADR-005-autenticacao-e-autorizacao.md)); notificações/integrações dependem de Q-13 ([ADR-008](../adr/ADR-008-notificacoes-e-integracoes.md)).

```mermaid
C4Context
    title Organização de Recursos — Contexto
    Person(solicitante, "Solicitante", "Professor ou coordenador; consulta disponibilidade e gerencia as próprias reservas (REQ-02).")
    Person(responsavel, "Responsável", "Aprova solicitações especiais, valida alocação docente e acompanha retirada/devolução (REQ-03).")
    Person(administrador, "Administrador", "Gerencia usuários, salas, professores, materiais, bloqueios e manutenção (REQ-04).")
    System(org, "Organização de Recursos", "Gerencia recursos e reservas; deve preservar a invariante declarada de ausência de conflitos (REQ-01).")

    Rel(solicitante, org, "Consulta disponibilidade e gerencia reservas")
    Rel(responsavel, org, "Aprova, valida e acompanha")
    Rel(administrador, org, "Gerencia cadastros e períodos")
```

**Leitura:** usuário e sistema são os únicos elementos conhecidos. Interface web/mobile, identidade externa, correio, calendário e demais integrações não foram declarados e não são representados como sistemas existentes. A autorização dos perfis depende de Q-10; ações de aprovação e manutenção não implicam, por si, efeito sobre a ocupação (Q-05/Q-08).