# SPEC-1-003 — Dashboard de baseline do funil

**Fase:** 1 · **Status:** planejada · **Dono:** Ricardo Paiva
**Origem no escopo:** K1–K3, seção 2 do escopo · **Degrau:** construção mínima (consultas SQL sobre dados já importados).

## Resultado observável
Painel com o funil real da FocoInfo: conversão por canal e por mês, volume por etapa, top owners, e indicadores de qualidade de dados — a baseline que falta para medir K1–K4.

## Limites e dependências
- **Inclui:** cards de totais (negócios, vendidos, perdidos, conversão); série mensal (12 meses); quebra por canal oficial (FOCO, GAVAZZI, JC MANANCIAL e combinações); ranking de owners; link para fila de exceções.
- **Fora:** metas e projeções; dados externos ao export.
- **Dependências:** SPEC-1-001.
- **Risco:** valores ausentes (97,7%) → dashboard exibe "sem valor" como categoria explícita, nunca zero implícito.

## Fluxo e regras
1. Dashboard lê apenas dados importados (sem digitação).
2. Conversão = vendidos ÷ total fechado no período (exibida com o denominador).
3. Filtros: período, canal, owner.

| Cenário | Dado | Resultado | Erro |
|---|---|---|---|
| Principal | CSV importado | números batem com CSV (3.046/642/2.293) | — |
| Limite | Filtro sem dados | estado vazio explicado | — |
| Falha | Consulta lenta (>3s) | agregação materializada | — |

## Critérios de aceite
- [ ] **CA-1-006:** totais do dashboard = totais do CSV (3.046 negócios, 642 vendidos, 2.293 perdidos, conversão 2026 ≈ 6%).
- [ ] **CA-1-007:** quebra por canal soma 100% dos negócios.

## TDD da SPEC
| Etapa | Prova | Comando/ação | Resultado | Evidência |
|---|---|---|---|---|
| RED | Consulta antes da importação | abrir dashboard | estado vazio, sem erro | print |
| GREEN | Conferência contra CSV | comparar contagens | valores idênticos | planilha de conferência |
| REGRESSÃO | Re-import não duplica métricas | importar 2x | totais estáveis | print |

## Tasks vinculadas
| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| 1.7 | Views SQL agregadas (funil, canal, owner, mês) | Ricardo | SPEC-1-003 | CA-1-006/007 | GREEN | SQL + prints | 1.2 | ☐ |
| 1.8 | UI dashboard com filtros | Ricardo | SPEC-1-003 | demonstração no preview | GREEN | print | 1.7 | ☐ |
