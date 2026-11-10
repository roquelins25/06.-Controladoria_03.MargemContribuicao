# MRG CONTR ANALISE HORIZONTAL — Visão geral

Modelo semântico de DRE gerencial (análise horizontal e vertical) da operação de bebidas, com margem bruta e margem de contribuição por cliente, representante, produto e região. O propósito foi inferido a partir da estrutura e dos nomes, pois o modelo não tem descrição declarada.

## Números

| Item | Quantidade |
|---|---|
| Tabelas documentadas | 14 (fora 3 tabelas de data automática do Power BI) |
| Colunas | 153 |
| Medidas | 39 |
| Relacionamentos | 10 (fora 2 de data automática) |
| Cultura | pt-BR |

## Fontes de dados

| Origem | Tabelas |
|---|---|
| Oracle ERP (query nativa, modo Import) | TB_FatosDRE, TB_CLIENTE, TB_FAMRMC, TB_MOTIVO, TB_PRODUTO, TB_REDE, TB_REP |
| Dataflow do Power BI Service | UF/REGIÃO, Senhas/Gerente |
| Dados embutidos no M | DRE_LV1, DRE_LV2 |
| Calculadas em DAX | CALENDARIO DAX, xSenha |

## Como o modelo funciona

- **Estrela simples:** `TB_FatosDRE` no centro, ligada a calendário, cliente, produto, representante e motivo. Cliente se abre em rede e região; produto se abre em família.
- **Uma fato, três tipos de linha:** a coluna `OPERACAO` (Saidas / Entradas) separa venda de devolução. Quase toda medida auxiliar é `CALCULATE` sobre uma medida `01.` filtrando essa coluna.
- **DRE horizontal:** `DRE_LV1` e `DRE_LV2` não se relacionam com o fato. A medida `00. DRE` escolhe o cálculo pelo ID da linha, e as linhas de subtotal (`ST`) acumulam as analíticas.
- **DRE vertical:** pasta `03. Auxiliares Vertical`, com Margem Bruta e Margem de Contribuição em R$ e %.
- **Acesso por senha:** `Senhas/Gerente`, `xSenha` e a medida `.Check Senha` liberam o relatório ("HORIZONTAL") quando usuário e senha conferem.
- **Filtros ativos:** a medida `HTML_Filtros_Ativos` desenha a faixa de filtros aplicados no topo.

## Estrutura das medidas

| Pasta | Medidas |
|---|---|
| (sem pasta) | 5 |
| 01. Totais | 10 |
| 02. Auxiliares Horizontal\00.Volume | 3 |
| 02. Auxiliares Horizontal\01.Receita OP | 1 |
| 02. Auxiliares Horizontal\02. Devolucoes | 1 |
| 02. Auxiliares Horizontal\03. Impostos | 2 |
| 02. Auxiliares Horizontal\04. CPV | 3 |
| 02. Auxiliares Horizontal\05. Despesas Variaveis | 5 |
| 03. Auxiliares Vertical | 9 |

## Arquivos desta documentação

- `01-tabelas.md`: tabelas e colunas
- `02-medidas.md`: medidas com DAX e explicação
- `03-relacionamentos.md`: relacionamentos e diagrama
- `04-dependencias.md`: quem usa quem entre as medidas
- `05-selects.md`: SELECTs de cada tabela (SQL embutido no modelo) e SQLs fora do modelo
