# Fase 1 — Dados centralizados e projetos visíveis

**Status:** aberta após aprovação humana em 09/09/2026

## Objetivo

Importar o export real do HubSpot sem depender de API, normalizar os dados, disponibilizar projetos em kanban e criar o baseline operacional da FocoInfo.

## Entrega palpável

Os 3.046 negócios e 2.690 empresas do arquivo de 31/08/2026 ficam navegáveis no sistema, com relatório de qualidade, fila de duplicidades, kanban de projetos e dashboard de conversão.

## Inclui

- Importação CSV idempotente por ID do HubSpot.
- Normalização e deduplicação de empresas.
- Cadastro e kanban de projetos com histórico.
- Dashboard de baseline por período, canal e responsável.

## Fora desta fase

- API/fluxo do HubSpot.
- Follow-ups automáticos, NPS e Teams.
- CRM de vendas próprio.

## Critérios de aceite da fase

- As três SPECs da fase demonstram seus CAs.
- Reimportar o CSV não duplica negócios nem apaga projetos.
- Totais do dashboard conferem com o arquivo de origem.

## Tasks

| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| 1.1 | Schema empresas/negocios/importacoes/excecoes + RLS | Ricardo | SPEC-1-001 | migração aplica e reverte limpa | migração no Supabase | SQL aplicado | G1 | ☐ |
| 1.2 | Parser CSV + normalização de nomes | Ricardo | SPEC-1-001 | 3.046 linhas, 0 erro não tratado | GREEN da SPEC | log do parser | 1.1 | ☐ |
| 1.3 | Dedup + fila de exceções | Ricardo | SPEC-1-001 | duplicidades conforme regra | REGRESSÃO da SPEC | fila populada | 1.2 | ☐ |
| 1.4 | Tela de upload + relatório de qualidade | Ricardo | SPEC-1-001 | CA-1-001..003 | demo no preview | prints | 1.3 | ☐ |
| 1.5 | Migração projetos + histórico + RLS | Ricardo | SPEC-1-002 | RLS ativa, migração limpa | RED da SPEC | SQL | 1.1 | ☐ |
| 1.6 | UI kanban com drag-and-drop e histórico | Ricardo | SPEC-1-002 | CA-1-004/005 | GREEN | print | 1.5 | ☐ |
| 1.7 | Views SQL agregadas do funil | Ricardo | SPEC-1-003 | CA-1-006/007 | GREEN | SQL + prints | 1.2 | ☐ |
| 1.8 | UI dashboard com filtros | Ricardo | SPEC-1-003 | demo no preview | GREEN | print | 1.7 | ☐ |

## Levas

**Leva A** (1.1) → **Leva B** (1.2, 1.5 — independentes após 1.1) → **Leva C** (1.3, 1.6, 1.7 — independentes) → **Leva D** (1.4, 1.8 — independentes).
