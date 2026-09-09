# SPEC-1-002 — Cadastro e kanban de projetos

**Fase:** 1 · **Status:** planejada · **Dono:** Ricardo Paiva
**Origem no escopo:** R1, K3 · **Degrau:** construção mínima (Supabase + React, padrão do repo).

## Resultado observável
Projetos cadastráveis com etapa, responsável, prazo e próxima ação, organizados em kanban com movimentação registrada em histórico.

## Limites e dependências
- **Inclui:** CRUD de projeto; colunas de kanban = etapas acordadas (Onboarding, Em execução, Aguardando cliente, Concluído, Pausado); drag-and-drop com histórico; vínculo opcional com negócio importado.
- **Fora:** criação automática a partir de venda (Fase 2); follow-ups automáticos (Fase 3).
- **Dependências:** SPEC-1-001 (dados importados), G2 (etapas validadas pelo cliente).
- **Rollback:** etapas são dados, não código — ajustáveis sem deploy.

## Fluxo e regras
1. Usuário cria projeto (título, empresa vinculada, responsável, prazo).
2. Projeto aparece no kanban na etapa inicial.
3. Movimentação de coluna exige confirmação e registra quem/quando.
4. Projeto vinculado a negócio "Vendido" exibe valor e canal do HubSpot quando existirem.

| Cenário | Dado | Resultado | Erro |
|---|---|---|---|
| Principal | Projeto criado | visível no kanban | — |
| Limite | Projeto sem empresa vinculada | permitido, com flag | — |
| Falha | Movimentação sem permissão | bloqueada por RLS | mensagem |

## Critérios de aceite
- [ ] **CA-1-004:** criar, mover e concluir projeto com histórico visível.
- [ ] **CA-1-005:** RLS impede usuário de ver projeto de outro dono (quando papel exigir).

## TDD da SPEC
| Etapa | Prova | Comando/ação | Resultado | Evidência |
|---|---|---|---|---|
| RED | Mover projeto sem auth | chamada anônima à API | 401 | log |
| GREEN | CRUD completo via UI | fluxo manual | projeto movido com histórico | print |
| REGRESSÃO | Re-import não apaga projetos | rodar import 2x | projetos intactos | contagem |

## Tasks vinculadas
| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| 1.5 | Migração projetos + histórico + RLS | Ricardo | SPEC-1-002 | migração limpa, RLS ativa | RED da SPEC | SQL | 1.1 | ☐ |
| 1.6 | UI kanban com drag-and-drop e histórico | Ricardo | SPEC-1-002 | CA-1-004 | GREEN | print | 1.5 | ☐ |
