# SPEC-1-001 — Importação e normalização do export HubSpot

**Fase:** 1 · **Status:** planejada · **Dono:** Ricardo Paiva (dev FocoInfo)
**Origem no escopo:** R1, R5 (seção 4 e 5 do escopo)
**Degrau da solução:** construção mínima — parser CSV próprio em Supabase Edge Function; sem API externa.

## Resultado observável
Os 3.046 negócios do export HubSpot (31/08/2026) estão no Supabase, normalizados, com empresas deduplicadas e relatório de qualidade de dados visível.

## Limites e dependências
- **Inclui:** upload do CSV; parser das 8 colunas; normalização de nomes (sufixo "Novo(a) Deal", formato "alias | razão social"); dedup por razão social normalizada; fila de exceções de duplicidade; relatório de qualidade (valor ausente, nome vazio/alias); re-importação idempotente por ID do HubSpot.
- **Fora de escopo:** API/fluxo HubSpot; enriquecimento externo; edição em massa.
- **Entradas:** `hubspot-crm-exports-todos-2026-08-31.csv` (fornecido).
- **Saídas:** tabelas `empresas`, `negocios`, `importacoes`, `excecoes_dedup`; tela de relatório de qualidade.
- **Dependências:** G1 (Supabase ativo).
- **Risco/Plano B:** CSV com colunas novas → parser tolerante + log de colunas desconhecidas.
- **Rollback:** importação marcada com lote; re-import substitui por ID sem duplicar.

## Fluxo e regras
1. Usuário autenticado faz upload do CSV.
2. Sistema valida colunas obrigatórias (ID, Nome, Etapa).
3. Cada linha vira/atualiza `negocios` (chave: ID do registro); empresa é criada ou reaproveitada por razão social normalizada.
4. Conflitos de dedup (mesma razão social, dados divergentes) vão para fila de exceções.
5. Relatório mostra: total importado, duplicidades, registros sem valor, nomes problemáticos.

| Cenário | Dado/condição | Resultado esperado | Caminho de erro |
|---|---|---|---|
| Principal | CSV completo (3.046 linhas) | 3.046 negócios, ~2.690 empresas | — |
| Limite | Linha sem nome | Importada com flag de qualidade | Não bloqueia lote |
| Falha | CSV sem coluna "ID do registro" | Rejeitado com mensagem clara | Nada é gravado |

## Checklist de execução
- [ ] G1 confirmado
- [ ] Parser implementado e testado com o CSV real
- [ ] Relatório de qualidade visível
- [ ] Re-import idempotente demonstrado

## Critérios de aceite
- [ ] **CA-1-001:** upload do CSV real importa 3.046 negócios sem duplicar em re-execução.
- [ ] **CA-1-002:** fila de exceções lista as duplicidades de razão social (292 empresas com >1 negócio como referência).
- [ ] **CA-1-003:** relatório de qualidade mostra % de negócios sem valor (baseline: 97,7%).

## TDD da SPEC
| Etapa | Prova | Comando/ação | Resultado esperado | Evidência |
|---|---|---|---|---|
| RED | Importar CSV sem coluna ID | fixture CSV inválido | erro 422, nada gravado | log do teste |
| GREEN | Importar CSV real 2x | upload repetido | contagem idêntica na 2ª | print do painel |
| REGRESSÃO | CSV com coluna extra | fixture com coluna nova | importa ignorando coluna, log registra | log |

**Dados/fixtures:** o próprio CSV fornecido + variantes inválidas.
**Evidência exigida:** print do relatório de qualidade + contagem de registros.

## Tasks vinculadas
| ID | Task | Dono | SPEC | Critério | Recorte da prova | Evidência esperada | Pré-condições | Status |
|---|---|---|---|---|---|---|---|---|
| 1.1 | Criar schema (empresas, negocios, importacoes, excecoes_dedup) com RLS | Ricardo | SPEC-1-001 | migração aplica e reverte limpa | migração roda no Supabase | SQL aplicado | G1 | ☐ |
| 1.2 | Implementar parser + normalização de nomes | Ricardo | SPEC-1-001 | 3.046 linhas parseadas, 0 erros não tratados | GREEN da SPEC | log do parser | 1.1 | ☐ |
| 1.3 | Implementar dedup + fila de exceções | Ricardo | SPEC-1-001 | duplicidades detectadas conforme regra | REGRESSÃO da SPEC | fila populada | 1.2 | ☐ |
| 1.4 | Tela de upload + relatório de qualidade | Ricardo | SPEC-1-001 | CA-1-001..003 visíveis na UI | demonstração no preview | prints | 1.3 | ☐ |
