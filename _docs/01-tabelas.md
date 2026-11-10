# Tabelas

O modelo tem **16 tabelas** (14 de dados e 2 de medidas), com **184 colunas**. Ficam de fora as tabelas de data automática do Power BI (`LocalDateTable_*` e `DateTableTemplate_*`).

| Tabela | Tipo | Colunas | Origem |
|---|---|---|---|
| [TB_FatosDRE](#tb_fatosdre) | Fato | 33 | Oracle (ERP) via query nativa, modo Import |
| [DRE_Geral - Plan](#dre_geral---plan) | Fato | 31 | Planilha Excel da Controladoria (`DRE CLIENTE_10112026.xlsx`, aba `DRE_Geral - Jan26 a Set26`) |
| [TB_CLIENTE](#tb_cliente) | Dimensão | 34 | Oracle (ERP) via query nativa, modo Import |
| [TB_FAMRMC](#tb_famrmc) | Dimensão | 3 | Oracle (ERP) via query nativa, modo Import |
| [TB_MOTIVO](#tb_motivo) | Dimensão | 3 | Oracle (ERP) via query nativa, modo Import |
| [TB_PRODUTO](#tb_produto) | Dimensão | 27 | Oracle (ERP) via query nativa, modo Import |
| [TB_REDE](#tb_rede) | Dimensão | 4 | Oracle (ERP) via query nativa, modo Import |
| [TB_REP](#tb_rep) | Dimensão | 11 | Oracle (ERP) via query nativa, modo Import |
| [CALENDARIO DAX](#calendario-dax) | Calendário | 16 | Calculada (DAX) |
| [UF/REGIÃO](#ufregião) | Dimensão | 7 | Dataflow do Power BI Service |
| [Senhas/Gerente](#senhasgerente) | Dimensão | 6 | Dataflow do Power BI Service |
| [DRE_LV1](#dre_lv1) | Estrutura da DRE | 4 | Tabela embutida no M (dados fixos) |
| [DRE_LV2](#dre_lv2) | Estrutura da DRE | 4 | Tabela embutida no M (dados fixos) |
| [xSenha](#xsenha) | Auxiliar | 1 | Calculada (DAX) |
| [_Medidas](#_medidas) | Medidas | 0 | Sem dados |
| [_Medidas2](#_medidas2) | Medidas | 0 | Sem dados |

## TB_FatosDRE

**Fato** · Oracle (ERP) via query nativa, modo Import

Fato do ERP. Junta três blocos do SQL (Saídas, Verbas e Entradas) em um UNION ALL; a coluna `OPERACAO` diz de qual bloco a linha veio. Grão: 1 linha por item de nota fiscal (produto/NF), mais as linhas de movimento de verba. Carrega valores de venda, CPV, impostos, comissão, frete, verbas e bonificação. Período do SQL: 01/01/2026 a 31/12/2026.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| OPERACAO | string |  | Saidas / Entradas: separa venda de devolução |
| FILIAL | int64 |  |  |
| MES_ANO | string |  |  |
| DATA_NF | dateTime |  |  |
| TIPO_NOTA | string |  |  |
| CODIGO_REPRES_NF | double |  |  |
| NOTA_FISCAL | double |  |  |
| COD_CLIENTE | int64 |  |  |
| CFOP | string |  |  |
| DESCRICAO_CFOP | string |  |  |
| CODPRODUTO | string |  |  |
| QTD_ITEM | double |  |  |
| CPV_ITEM_VENDA | double |  |  |
| VALOR_VENDA | double |  |  |
| VALOR_CPV_BONIF | double |  |  |
| VALOR_DESCONTO | double |  |  |
| VALOR_OUTRAS | double |  |  |
| VALOR_IPI | double |  |  |
| VALOR_ICMS | double |  |  |
| VALOR_FCP | double |  |  |
| VALOR_ST | double |  |  |
| VALOR_PIS_VENDA | double |  |  |
| VALOR_COFINS | double |  |  |
| TOTAL_IMPOSTOS | double |  |  |
| VALOR_COMISSAO | double |  |  |
| CPV_PROC_FIXO | double |  |  |
| DESP_OPER_FIXA | double |  |  |
| CRED_IMPOSTOS | double |  |  |
| VERBA_FIXA | double |  |  |
| VERBA_VARIAVEL | double |  |  |
| FRETE | double |  |  |
| PLACA | string |  |  |
| USU_CODMAR | string |  |  |

## DRE_Geral - Plan

**Fato** · Planilha Excel da Controladoria (`DRE CLIENTE_10112026.xlsx`, aba `DRE_Geral - Jan26 a Set26`)

Fato alternativo, com o mesmo papel do `TB_FatosDRE`, mas carregado da planilha que a Controladoria mantém. Cobre janeiro a setembro de 2026. Liga-se às mesmas dimensões (cliente, produto, representante, calendário) e alimenta as medidas da família `_Medidas2`. Os nomes das colunas seguem o padrão da planilha.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| Operacao | string |  | Saidas / Entradas: separa venda de devolução |
| Filial | int64 |  |  |
| Mes_Ano | dateTime |  |  |
| Data_NF | dateTime |  |  |
| Tipo_Nota | string |  |  |
| Codigo_repres_NF | double |  |  |
| Nota_Fiscal | int64 |  |  |
| Cod_Cliente | int64 |  |  |
| CFOP | string |  |  |
| CodProduto | string |  |  |
| Qtd_Item | double |  |  |
| CPV_Item_Venda | double |  |  |
| Valor_Venda | double |  |  |
| Valor_CPV_Bonif | double |  |  |
| Valor_Desconto | double |  |  |
| Valor_Outras | double |  |  |
| Valor_IPI | double |  |  |
| Valor_ICMS | double |  |  |
| Valor_FCP | double |  |  |
| Valor_ST | double |  |  |
| Valor_PIS_Venda | double |  |  |
| valor_COFINS | double |  |  |
| Total_Impostos | double |  |  |
| Valor_Comissao | double |  |  |
| CPV_Proc_Fixo | double |  |  |
| DESP_Oper_Fixa | double |  |  |
| CRED_Impostos | double |  |  |
| Verba_Fixa | double |  |  |
| Verba_Variavel | double |  |  |
| Frete | double |  |  |
| Motivo_Devolucao | string |  |  |

## TB_CLIENTE

**Dimensão** · Oracle (ERP) via query nativa, modo Import

Cadastro de clientes, com rede, representante, região/UF e situação financeira. A coluna `XREGSENHA` liga o cliente à região de acesso.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| CODCLI | int64 |  |  |
| NOMCLI | string |  |  |
| XNOMCLI | string |  |  |
| CGCCPF | int64 |  |  |
| APECLI | string |  |  |
| USU_PERDUP | string |  |  |
| MARCLI | string |  |  |
| CODGRE | int64 |  |  |
| CEPCLI | int64 |  |  |
| ENDCLI | string |  |  |
| CIDCLI | string |  |  |
| SIGUFS | string |  |  |
| FONCLI | string |  |  |
| DATCAD | dateTime |  |  |
| SITCLI | string |  |  |
| CODREP | int64 |  |  |
| NOMREP | string |  |  |
| CODPAI | string |  |  |
| NOMPAI | string |  |  |
| CODRAM | string |  |  |
| USU_CODRAM | string |  |  |
| USU_DESRAM | string |  |  |
| SITFINANCEIRO | string |  |  |
| SITINADIMPLENCIA | string |  |  |
| CODCPG | string |  |  |
| ABRCPG | string |  |  |
| VLRLIM | double |  |  |
| EXIAGE | string |  |  |
| DEP_PADRAO | string |  |  |
| CODREVE | string |  |  |
| NOMRVE | string |  |  |
| ABRRVE | string |  |  |
| XREGSENHA | string |  |  |
| CODTPR | string |  |  |

## TB_FAMRMC

**Dimensão** · Oracle (ERP) via query nativa, modo Import

Família de produto (ramo/família mercadológica) usada para agrupar produtos.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| CODRMC | int64 |  |  |
| DESRMC | string |  |  |
| XDESRMC | string |  |  |

## TB_MOTIVO

**Dimensão** · Oracle (ERP) via query nativa, modo Import

Marca/motivo (`USU_CODMAR`) das notas do fato.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| USU_CODMAR | string |  |  |
| USU_NOMMAR | string |  |  |
| USU_SITMAR | string |  |  |

## TB_PRODUTO

**Dimensão** · Oracle (ERP) via query nativa, modo Import

Cadastro de produtos (descrição, família, marca, sabor, volume, classificação de BI).

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| CODPRO | string |  |  |
| DESPRO | string |  |  |
| CPLPRO | string |  |  |
| DESNFV | string |  |  |
| CODFAM | string |  |  |
| UNIMED | string |  |  |
| CODAGE | string |  |  |
| SITPRO | string |  |  |
| CODMAR | string |  |  |
| PESBRU | double |  |  |
| PESLIQ | double |  |  |
| CODAGC | string |  |  |
| USU_CLAMAN | string |  |  |
| CODREF | string |  |  |
| QTDPCTUN | double |  |  |
| QTDPCT | string |  |  |
| VOLPRO_UN | string |  |  |
| Select | string |  |  |
| CODCLC | string |  |  |
| VOLPRO | double |  |  |
| SABOR | string |  |  |
| USU_CODFAMDAY | int64 |  |  |
| USU_DESFAMDAY | string |  |  |
| PRODUTO_ZERO | string |  |  |
| QTD_PALETE | double |  |  |
| XDESPRO | string |  |  |
| CLASSIFICACAO_BI | string |  |  |

## TB_REDE

**Dimensão** · Oracle (ERP) via query nativa, modo Import

Rede/grupo de clientes (`CODGRE`).

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| CODGRE | int64 |  |  |
| NOMGRE | string |  |  |
| SIGGRE | string |  |  |
| XNOMGRE | string |  |  |

## TB_REP

**Dimensão** · Oracle (ERP) via query nativa, modo Import

Representantes comerciais, com tipo, cidade, UF, supervisor e situação.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| CODREP | int64 |  |  |
| NOMREP | string |  |  |
| APEREP | string |  |  |
| TIPREP | string |  |  |
| CIDREP | string |  |  |
| SIGUFS | string |  |  |
| FONREP | string |  |  |
| SITREP | string |  |  |
| REPSUP | int64 |  |  |
| NOMSUP | string |  |  |
| REP_CONCAT | int64 |  |  |

## CALENDARIO DAX

**Calendário** · Calculada (DAX)

Tabela de datas criada em DAX. Traz ano, mês, trimestre, semestre, dias úteis, flag de mês atual e colunas de ordenação. Liga-se ao fato pela coluna `DATA_NF` e à planilha pela coluna `Mes_Ano`.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| Date | calculada |  |  |
| Ano | calculada |  |  |
| AnoNumber | calculada |  |  |
| Mês | calculada |  |  |
| Nome do Mês | calculada |  |  |
| Mês Abrev | calculada |  |  |
| trimestre | calculada |  |  |
| semestre | calculada |  |  |
| Dia | calculada |  |  |
| Dia/Mes | calculada |  |  |
| DiasSemana | calculada |  |  |
| Mes Atual | calculada |  |  |
| Ano-Mes | calculada |  |  |
| OrderBy | calculada |  |  |
| Numerosemana | calculada |  |  |
| DiasUteis | calculada |  |  |

## UF/REGIÃO

**Dimensão** · Dataflow do Power BI Service

Mapeia UF para região/grupo e gestão. Traz também as metas de orçamento (`ORC_VER_2025`, `ORC_VER_2026`, `ORC_BON_2026`). Liga-se a `Senhas/Gerente` por `ID` (muitos-para-muitos).

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| UF | string |  |  |
| Região/Grupo | string |  |  |
| ID | string |  |  |
| GESTÃO | string |  |  |
| ORC_VER_2025 | double |  |  |
| ORC_VER_2026 | double |  |  |
| ORC_BON_2026 | double |  |  |

## Senhas/Gerente

**Dimensão** · Dataflow do Power BI Service

Cadastro de usuários do relatório, com cargo, região (`ID`) e flag de faturamento. Usado pela medida `.Check Senha` e pelo filtro de usuário.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| Usuario | string |  |  |
| Senha | int64 |  |  |
| ID | string |  |  |
| Cargo | string |  |  |
| Key | int64 |  |  |
| Flag_Fat | string |  |  |

## DRE_LV1

**Estrutura da DRE** · Tabela embutida no M (dados fixos)

Linhas de primeiro nível da DRE (`ID`, `DESC`, `SUB`). `SUB` = `A` (analítica) ou `ST` (subtotal). Não se relaciona com o fato.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| ID | string |  |  |
| DESC | string |  |  |
| SUB | string |  |  |
| Índice | int64 |  |  |

## DRE_LV2

**Estrutura da DRE** · Tabela embutida no M (dados fixos)

Linhas de detalhe da DRE, cada uma ligada a uma linha de `DRE_LV1` por `ID_LV1`. O `ID` define qual cálculo a medida de DRE aplica.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| ID | int64 |  |  |
| ID_LV1 | string |  |  |
| DESC | string |  |  |
| Índice | int64 |  |  |

## xSenha

**Auxiliar** · Calculada (DAX)

Série numérica usada como campo de digitação da senha. A coluna é oculta. A medida `.Check Senha` compara o valor digitado com a senha do usuário escolhido.

| Coluna | Tipo | Coluna de origem | Observação |
|---|---|---|---|
| xSenha | calculada | Value | oculta |

## _Medidas

**Medidas** · Sem dados

Tabela sem colunas que guarda as medidas da família Oracle (`TB_FatosDRE`).

Tabela sem colunas. Guarda 40 medida(s); ver [02-medidas.md](02-medidas.md).

## _Medidas2

**Medidas** · Sem dados

Tabela sem colunas que guarda as medidas da família da planilha (`DRE_Geral - Plan`). Os nomes usam os prefixos `01.DRE` / `02.DRE` ou `02. N -`.

Tabela sem colunas. Guarda 37 medida(s); ver [02-medidas.md](02-medidas.md).
