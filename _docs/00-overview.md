# MRG CONTR ANALISE HORIZONTAL — Visão geral

Modelo semântico de DRE gerencial (análise horizontal e vertical) da operação de bebidas, com margem bruta e margem de contribuição por cliente, representante, produto e região. Tem **duas fontes de fato**: o ERP Oracle (`TB_FatosDRE`) e a planilha da Controladoria (`DRE_Geral - Plan`). O propósito foi inferido a partir da estrutura e dos nomes, pois o modelo não tem descrição declarada.

## Números

| Item | Quantidade |
|---|---|
| Tabelas de dados | 14 |
| Tabelas de medidas | 2 (`_Medidas` e `_Medidas2`) |
| Colunas (tabelas de dados) | 184 |
| Medidas | 79 (40 em `_Medidas`, 37 em `_Medidas2`, 2 em `xSenha`) |
| Relacionamentos de usuário | 14 (fora 3 de data automática do Power BI) |
| Fatos | 2 (`TB_FatosDRE` e `DRE_Geral - Plan`) |
| Cultura | pt-BR |

## Fontes de dados

| Origem | Tabelas |
|---|---|
| Oracle ERP (query nativa, modo Import) | TB_FatosDRE, TB_CLIENTE, TB_FAMRMC, TB_MOTIVO, TB_PRODUTO, TB_REDE, TB_REP |
| Planilha Excel da Controladoria (modo Import) | DRE_Geral - Plan |
| Dataflow do Power BI Service | UF/REGIÃO, Senhas/Gerente |
| Dados embutidos no M | DRE_LV1, DRE_LV2 |
| Calculadas em DAX | CALENDARIO DAX, xSenha |

## Como o modelo funciona

- **Duas famílias de medidas, uma por fato.** `_Medidas` (ou "família Oracle") calcula sobre `TB_FatosDRE`. `_Medidas2` (ou "família da planilha") calcula sobre `DRE_Geral - Plan`, com os mesmos conceitos e nomes com prefixo `01.DRE` / `02.DRE` ou `02. N -`.
- **Estrela:** cada fato se liga a calendário, cliente, produto e representante. `TB_FatosDRE` também se liga a motivo. Cliente se abre em rede e região; produto se abre em família.
- **Uma fato, três tipos de linha:** a coluna `OPERACAO` (Saidas / Entradas) separa venda de devolução. Quase toda medida auxiliar é `CALCULATE` sobre uma medida `01.` filtrando essa coluna.
- **DRE horizontal:** `DRE_LV1` e `DRE_LV2` não se relacionam com os fatos. As medidas `00. DRE` (Oracle) e `00.DRE DRE` (planilha) escolhem o cálculo pelo ID da linha, e as linhas de subtotal (`ST`) acumulam as analíticas.
- **DRE vertical:** pastas `03. Auxiliares Vertical` (Oracle) e `02. Auxiliares Vertical` (planilha), com Margem Bruta e Margem de Contribuição em R$ e %.
- **Acesso por senha:** `Senhas/Gerente`, `xSenha` e a medida `.Check Senha` liberam o relatório ("HORIZONTAL") quando usuário e senha conferem.
- **Filtros ativos:** a medida `HTML_Filtros_Ativos` desenha a faixa de filtros aplicados no topo.

## Estrutura das medidas

| Pasta | `_Medidas` (Oracle) | `_Medidas2` (planilha) |
|---|---|---|
| (sem pasta) | 6 | 3 |
| 01. Totais | 10 | 10 |
| 02. Auxiliares Horizontal (Volume, Receita OP, Devoluções, Impostos, CPV, Despesas Variáveis) | 15 | 15 |
| 02. / 03. Auxiliares Vertical | 9 | 9 |
| **Total** | **40** | **37** |

`xSenha` tem mais 2 medidas (`Valor xSenha` e `.Check Senha`).

## Pontos de atenção

Três pontos factuais encontrados ao ler o DAX estão em [02-medidas.md](02-medidas.md#pontos-de-atenção). Resumo: (1) o subtotal de impostos de `00. DRE` não bate com a linha analítica de impostos devolvidos; (2) o filtro de CFOP de `02.DRE Fretes Tereceiros` não tem efeito; (3) a margem de contribuição da planilha usa verbas da família Oracle.

## Arquivos desta documentação

- `01-tabelas.md`: tabelas e colunas
- `02-medidas.md`: medidas com DAX, explicação e pontos de atenção
- `03-relacionamentos.md`: relacionamentos e diagrama
- `04-dependencias.md`: quem usa quem entre as medidas
- `05-selects.md`: SELECTs de cada tabela (SQL embutido no modelo) e SQLs fora do modelo
