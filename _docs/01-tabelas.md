# Tabelas

| Tabela | Tipo | Colunas | Origem |
|---|---|---|---|
| [TB_FatosDRE](#tb-fatosdre) | Fato | 33 | Oracle (ERP) via query nativa |
| [TB_CLIENTE](#tb-cliente) | Dimensão | 34 | Oracle (ERP) via query nativa |
| [TB_FAMRMC](#tb-famrmc) | Dimensão | 3 | Oracle (ERP) via query nativa |
| [TB_MOTIVO](#tb-motivo) | Dimensão | 3 | Oracle (ERP) via query nativa |
| [TB_PRODUTO](#tb-produto) | Dimensão | 27 | Oracle (ERP) via query nativa |
| [TB_REDE](#tb-rede) | Dimensão | 4 | Oracle (ERP) via query nativa |
| [TB_REP](#tb-rep) | Dimensão | 11 | Oracle (ERP) via query nativa |
| [CALENDARIO DAX](#calendario-dax) | Calendário | 16 | Calculada (DAX) |
| [_Medidas](#medidas) | Medidas | 0 | Sem dados |
| [UF/REGIÃO](#uf-regi-o) | Dimensão | 7 | Dataflow do Power BI Service |
| [Senhas/Gerente](#senhas-gerente) | Dimensão | 6 | Dataflow do Power BI Service |
| [DRE_LV1](#dre-lv1) | Estrutura da DRE | 4 | Tabela embutida no M (dados fixos) |
| [DRE_LV2](#dre-lv2) | Estrutura da DRE | 4 | Tabela embutida no M (dados fixos) |
| [xSenha](#xsenha) | Auxiliar | 1 | Calculada (DAX) |

## TB_FatosDRE

**Fato** · Oracle (ERP) via query nativa

Fato único do modelo. Junta três blocos do SQL (Saídas, Verbas e Entradas) em um UNION ALL, e a coluna OPERACAO diz de qual bloco a linha veio. Grão: 1 linha por item de nota fiscal (por produto/NF), mais as linhas de movimento de verba. Carrega valores de venda, CPV, impostos, comissão, frete, verbas e bonificação.

| Coluna | Tipo | Observação |
|---|---|---|
| OPERACAO | string |  |
| FILIAL | int64 |  |
| MES_ANO | string |  |
| DATA_NF | dateTime |  |
| TIPO_NOTA | string |  |
| CODIGO_REPRES_NF | double |  |
| NOTA_FISCAL | double |  |
| COD_CLIENTE | int64 |  |
| CFOP | string |  |
| DESCRICAO_CFOP | string |  |
| CODPRODUTO | string |  |
| QTD_ITEM | double |  |
| CPV_ITEM_VENDA | double |  |
| VALOR_VENDA | double |  |
| VALOR_CPV_BONIF | double |  |
| VALOR_DESCONTO | double |  |
| VALOR_OUTRAS | double |  |
| VALOR_IPI | double |  |
| VALOR_ICMS | double |  |
| VALOR_FCP | double |  |
| VALOR_ST | double |  |
| VALOR_PIS_VENDA | double |  |
| VALOR_COFINS | double |  |
| TOTAL_IMPOSTOS | double |  |
| VALOR_COMISSAO | double |  |
| CPV_PROC_FIXO | double |  |
| DESP_OPER_FIXA | double |  |
| CRED_IMPOSTOS | double |  |
| VERBA_FIXA | double |  |
| VERBA_VARIAVEL | double |  |
| FRETE | double |  |
| PLACA | string |  |
| USU_CODMAR | string |  |

SELECT completo em `05-selects.md`.

## TB_CLIENTE

**Dimensão** · Oracle (ERP) via query nativa

Cadastro de clientes com filial 1, representante, rede/grupo, canal, situação financeira e inadimplência. Grão: 1 linha por cliente. XREGSENHA define a região usada no controle de acesso por senha (SP vira SPI/SPA/SPC conforme a regional).

| Coluna | Tipo | Observação |
|---|---|---|
| CODCLI | int64 |  |
| NOMCLI | string |  |
| XNOMCLI | string |  |
| CGCCPF | int64 |  |
| APECLI | string |  |
| USU_PERDUP | string |  |
| MARCLI | string |  |
| CODGRE | int64 |  |
| CEPCLI | int64 |  |
| ENDCLI | string |  |
| CIDCLI | string |  |
| SIGUFS | string |  |
| FONCLI | string |  |
| DATCAD | dateTime |  |
| SITCLI | string |  |
| CODREP | int64 |  |
| NOMREP | string |  |
| CODPAI | string |  |
| NOMPAI | string |  |
| CODRAM | string |  |
| USU_CODRAM | string |  |
| USU_DESRAM | string |  |
| SITFINANCEIRO | string |  |
| SITINADIMPLENCIA | string |  |
| CODCPG | string |  |
| ABRCPG | string |  |
| VLRLIM | double |  |
| EXIAGE | string |  |
| DEP_PADRAO | string |  |
| CODREVE | string |  |
| NOMRVE | string |  |
| ABRRVE | string |  |
| XREGSENHA | string |  |
| CODTPR | string |  |

SELECT completo em `05-selects.md`.

## TB_FAMRMC

**Dimensão** · Oracle (ERP) via query nativa

Famílias de produto usadas na DRE (código + descrição). Grão: 1 linha por família.

| Coluna | Tipo | Observação |
|---|---|---|
| CODRMC | int64 |  |
| DESRMC | string |  |
| XDESRMC | string |  |

SELECT completo em `05-selects.md`.

## TB_MOTIVO

**Dimensão** · Oracle (ERP) via query nativa

Motivos/marcas cadastrados na tabela USU_TE440MOT, ligados ao fato por USU_CODMAR.

| Coluna | Tipo | Observação |
|---|---|---|
| USU_CODMAR | string |  |
| USU_NOMMAR | string |  |
| USU_SITMAR | string |  |

SELECT completo em `05-selects.md`.

## TB_PRODUTO

**Dimensão** · Oracle (ERP) via query nativa

Cadastro de produtos com a coluna CLASSIFICACAO_BI, que agrupa energéticos por volume e demais produtos por família (Vodka, Cerveja, Gin, Whisky...). Grão: 1 linha por produto.

| Coluna | Tipo | Observação |
|---|---|---|
| CODPRO | string |  |
| DESPRO | string |  |
| CPLPRO | string |  |
| DESNFV | string |  |
| CODFAM | string |  |
| UNIMED | string |  |
| CODAGE | string |  |
| SITPRO | string |  |
| CODMAR | string |  |
| PESBRU | double |  |
| PESLIQ | double |  |
| CODAGC | string |  |
| USU_CLAMAN | string |  |
| CODREF | string |  |
| QTDPCTUN | double |  |
| QTDPCT | string |  |
| VOLPRO_UN | string |  |
| Select | string |  |
| CODCLC | string |  |
| VOLPRO | double |  |
| SABOR | string |  |
| USU_CODFAMDAY | int64 |  |
| USU_DESFAMDAY | string |  |
| PRODUTO_ZERO | string |  |
| QTD_PALETE | double |  |
| XDESPRO | string |  |
| CLASSIFICACAO_BI | string |  |

SELECT completo em `05-selects.md`.

## TB_REDE

**Dimensão** · Oracle (ERP) via query nativa

Redes/grupos de clientes. Grão: 1 linha por grupo (CODGRE).

| Coluna | Tipo | Observação |
|---|---|---|
| CODGRE | int64 |  |
| NOMGRE | string |  |
| SIGGRE | string |  |
| XNOMGRE | string |  |

SELECT completo em `05-selects.md`.

## TB_REP

**Dimensão** · Oracle (ERP) via query nativa

Representantes comerciais e seus supervisores. Grão: 1 linha por representante.

| Coluna | Tipo | Observação |
|---|---|---|
| CODREP | int64 |  |
| NOMREP | string |  |
| APEREP | string |  |
| TIPREP | string |  |
| CIDREP | string |  |
| SIGUFS | string |  |
| FONREP | string |  |
| SITREP | string |  |
| REPSUP | int64 |  |
| NOMSUP | string |  |
| REP_CONCAT | int64 |  |

SELECT completo em `05-selects.md`.

## CALENDARIO DAX

**Calendário** · Calculada (DAX)

Tabela de datas criada em DAX, indo do menor ao maior ano de DATA_NF. Traz ano, mês, trimestre, semestre, dias úteis, flag de mês atual e coluna de ordenação (OrderBy).

Colunas: Date, Ano, AnoNumber, Mês, Nome do Mês, Mês Abrev, trimestre, semestre, Dia, Dia/Mes, DiasSemana, Mes Atual, Ano-Mes, OrderBy, Numerosemana, DiasUteis.

## _Medidas

**Medidas** · Sem dados

Tabela vazia que concentra as medidas do modelo.


## UF/REGIÃO

**Dimensão** · Dataflow do Power BI Service

Mapa UF → região/grupo/gestão, com orçamento de volume 2025/2026 e bonificação 2026. Liga o cliente (XREGSENHA) à região e ao gerente.

| Coluna | Tipo | Observação |
|---|---|---|
| UF | string |  |
| Região/Grupo | string |  |
| ID | string |  |
| GESTÃO | string |  |
| ORC_VER_2025 | double |  |
| ORC_VER_2026 | double |  |
| ORC_BON_2026 | double |  |

M: lê a entidade do dataflow no workspace do Power BI Service (sem query SQL).

## Senhas/Gerente

**Dimensão** · Dataflow do Power BI Service

Usuários, cargos e senhas numéricas usados no controle de acesso do relatório.

| Coluna | Tipo | Observação |
|---|---|---|
| Usuario | string |  |
| Senha | int64 |  |
| ID | string |  |
| Cargo | string |  |
| Key | int64 |  |
| Flag_Fat | string |  |

M: lê a entidade do dataflow no workspace do Power BI Service (sem query SQL).

## DRE_LV1

**Estrutura da DRE** · Tabela embutida no M (dados fixos)

Linhas do primeiro nível da DRE. SUB = "A" é linha analítica (soma filhos) e "ST" é subtotal acumulado. Índice mantém a ordem de exibição.

| Coluna | Tipo | Observação |
|---|---|---|
| ID | string |  |
| DESC | string | ordena por Índice |
| SUB | string |  |
| Índice | int64 |  |

## DRE_LV2

**Estrutura da DRE** · Tabela embutida no M (dados fixos)

Linhas de detalhe da DRE. Cada ID é um item (ex.: 20001 Receita Operacional) ligado ao LV1 por ID_LV1. A medida 00. DRE usa esses IDs no SWITCH.

| Coluna | Tipo | Observação |
|---|---|---|
| ID | int64 |  |
| ID_LV1 | string |  |
| DESC | string | ordena por Índice |
| Índice | int64 |  |

## xSenha

**Auxiliar** · Calculada (DAX)

Série de 0 a 9999 usada como "teclado" para o usuário digitar a senha no relatório.

| Coluna | Tipo | Observação |
|---|---|---|
| xSenha | ? |  |
| Coluna | Tipo |
|---|---|
| xSenha | int64 (0 a 9999) |

Medidas nesta tabela: Valor xSenha, .Check Senha (ver `02-medidas.md`).
