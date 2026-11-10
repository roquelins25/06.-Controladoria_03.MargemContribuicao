# Medidas (39)

Agrupadas por pasta de exibição. Todas as medidas ficam na tabela `_Medidas`, exceto `Valor xSenha` e `.Check Senha` (em `xSenha`).

## (sem pasta)

### 00. DRE

Medida da DRE horizontal. Linha analítica ("A"): soma os IDs de DRE_LV2 visíveis por um SWITCH (volume, receita, devoluções, impostos, CPV, frete, verbas, bonificação, comissões). Linha subtotal ("ST"): soma acumulada das linhas A (exceto volume) até o índice atual, o que gera Receita s/ Devolução, Receita Líquida, Margem Bruta e Margem de Contribuição. Deduções entram com sinal negativo. Na LV2, o ID 1 mostra PV por pacote quando a linha de detalhe está no escopo.

```dax
VAR _tipo = SELECTEDVALUE( DRE_LV1[SUB] )
                VAR _cur  = VALUE( SELECTEDVALUE( DRE_LV1[ID] ) )

                -- valor assinado de cada linha analítica (A) do LV1
                VAR _rob  =  [02. Receita Operacional Bruta ( R$ )]
                VAR _dev  = -[02. Devolucao e Cancelamentos ( R$ )]
                VAR _imp  = -( [02. Imposto de Vendas ( R$ )] + [02. Impostos Devolucoes ( R$ )] )
                VAR _cpv  = -( [02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )]
                             + [02. Gastos Gerais Fabricacao-GGF FIXO ( R$ )]
                             - [02. Credito CPV - Devolucao Pro ACA] )
                VAR _desp = -( [02. Fretes Tereceiros ( R$ )] + [02. Verbas Fixas ( R$ )]
                             + [02. Verbas Variaveis ( R$ )] + [02. Bonificacao] + [02. Comissoes ( R$ )] )

                VAR _analitico =
                    SUMX(
                        VALUES( DRE_LV2[ID] ),
                        SWITCH(
                            DRE_LV2[ID],
                            1,    IF( ISINSCOPE( DRE_LV2[DESC] ), [02. PV Bruto / Pacotes ( R$ )], [02. Volume Total ( PCTs )] ) ,
                            20001, _rob,
                            30001, _dev,
                            40001, -[02. Imposto de Vendas ( R$ )],
                            50001,  [02. Impostos Devolucoes ( R$ )],
                            60001, -[02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )],
                            70001, -[02. Gastos Gerais Fabricacao-GGF FIXO ( R$ )],
                            80001,  [02. Credito CPV - Devolucao Pro ACA],
                            90001, -[02. Comissoes ( R$ )],
                            10001, -[02. Fretes Tereceiros ( R$ )],
                            11001, -[02. Verbas Fixas ( R$ )],
                            12001, -[02. Verbas Variaveis ( R$ )],
                            13001, -[02. Bonificacao]
                        )
                    )

                -- ST = soma acumulada das linhas A (exceto volume) até o índice atual
                VAR _acumulado =
                    SUMX(
                        FILTER(
                            ALL( DRE_LV1 ),
                            VALUE( DRE_LV1[ID] ) <= _cur
                                && DRE_LV1[SUB] = "A"
                                && DRE_LV1[ID] <> "10001"
                        ),
                        SWITCH(
                            DRE_LV1[ID],
                            "10002", _rob,
                            "10003", _dev,
                            "10005", _imp,
                            "10007", _cpv,
                            "10009", _desp
                        )
                    )

                RETURN
                IF(
                    ISINSCOPE( DRE_LV2[DESC] ) && _tipo = "ST", BLANK(),
                    SWITCH( _tipo, "A", _analitico, "ST", _acumulado )
                )
```

Usa: `02. Bonificacao`, `02. Comissoes ( R$ )`, `02. Credito CPV - Devolucao Pro ACA`, `02. Devolucao e Cancelamentos ( R$ )`, `02. Fretes Tereceiros ( R$ )`, `02. Gastos Gerais Fabricacao-GGF FIXO ( R$ )`, `02. Imposto de Vendas ( R$ )`, `02. Impostos Devolucoes ( R$ )`, `02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )`, `02. PV Bruto / Pacotes ( R$ )`, `02. Receita Operacional Bruta ( R$ )`, `02. Verbas Fixas ( R$ )`, `02. Verbas Variaveis ( R$ )`, `02. Volume Total ( PCTs )`

### HTML_Filtros_Ativos

Gera um bloco HTML/CSS (para o visual HTML Content) com os filtros ativos: usuário, ano, mês, grupo, cliente, representante, região e UF, em chips, com contador e limite de 5 valores por filtro.

```dax
VAR FiltroACC = IF( ISFILTERED('Senhas/Gerente'[Usuario]),
                    CONCATENATEX( TOPN(5, VALUES('Senhas/Gerente'[Usuario]), 'Senhas/Gerente'[Usuario], ASC),
                        "<span class='chip'>" & 'Senhas/Gerente'[Usuario] & "</span>", "", 'Senhas/Gerente'[Usuario], ASC )
                    & IF( COUNTROWS(VALUES('Senhas/Gerente'[Usuario])) > 5,
                        "<span class='more'>+" & (COUNTROWS(VALUES('Senhas/Gerente'[Usuario])) - 5) & "</span>", "" ) )

                VAR Filtro1 = IF( ISFILTERED('CALENDARIO DAX'[Ano]),
                    CONCATENATEX( TOPN(5, VALUES('CALENDARIO DAX'[Ano]), 'CALENDARIO DAX'[Ano], ASC),
                        "<span class='chip'>" & 'CALENDARIO DAX'[Ano] & "</span>", "", 'CALENDARIO DAX'[Ano], ASC )
                    & IF( COUNTROWS(VALUES('CALENDARIO DAX'[Ano])) > 5,
                        "<span class='more'>+" & (COUNTROWS(VALUES('CALENDARIO DAX'[Ano])) - 5) & "</span>", "" ) )
                VAR Filtro2 = IF( ISFILTERED('CALENDARIO DAX'[Ano-Mes]),
                    CONCATENATEX( TOPN(5, VALUES('CALENDARIO DAX'[Ano-Mes]), 'CALENDARIO DAX'[Ano-Mes], ASC),
                        "<span class='chip'>" & 'CALENDARIO DAX'[Ano-Mes] & "</span>", "", 'CALENDARIO DAX'[Ano-Mes], ASC )
                    & IF( COUNTROWS(VALUES('CALENDARIO DAX'[Ano-Mes])) > 5,
                        "<span class='more'>+" & (COUNTROWS(VALUES('CALENDARIO DAX'[Ano-Mes])) - 5) & "</span>", "" ) )
                VAR Filtro3 = IF( ISFILTERED(TB_REDE[XNOMGRE]),
                    CONCATENATEX( TOPN(5, VALUES(TB_REDE[XNOMGRE]), TB_REDE[XNOMGRE], ASC),
                        "<span class='chip'>" & TB_REDE[XNOMGRE] & "</span>", "", TB_REDE[XNOMGRE], ASC )
                    & IF( COUNTROWS(VALUES(TB_REDE[XNOMGRE])) > 5,
                        "<span class='more'>+" & (COUNTROWS(VALUES(TB_REDE[XNOMGRE])) - 5) & "</span>", "" ) )
                VAR Filtro4 = IF( ISFILTERED(TB_CLIENTE[XNOMCLI]),
                    CONCATENATEX( TOPN(5, VALUES(TB_CLIENTE[XNOMCLI]), TB_CLIENTE[XNOMCLI], ASC),
                        "<span class='chip'>" & TB_CLIENTE[XNOMCLI] & "</span>", "", TB_CLIENTE[XNOMCLI], ASC )
                    & IF( COUNTROWS(VALUES(TB_CLIENTE[XNOMCLI])) > 5,
                        "<span class='more'>+" & (COUNTROWS(VALUES(TB_CLIENTE[XNOMCLI])) - 5) & "</span>", "" ) )
                VAR Filtro5 = IF( ISFILTERED(TB_REP[NOMREP]),
                    CONCATENATEX( TOPN(5, VALUES(TB_REP[NOMREP]), TB_REP[NOMREP], ASC),
                        "<span class='chip'>" & TB_REP[NOMREP] & "</span>", "", TB_REP[NOMREP], ASC )
                    & IF( COUNTROWS(VALUES(TB_REP[NOMREP])) > 5,
                        "<span class='more'>+" & (COUNTROWS(VALUES(TB_REP[NOMREP])) - 5) & "</span>", "" ) )
                VAR Filtro6 = IF( ISFILTERED('UF/REGIÃO'[Região/Grupo]),
                    CONCATENATEX( TOPN(5, VALUES('UF/REGIÃO'[Região/Grupo]), 'UF/REGIÃO'[Região/Grupo], ASC),
                        "<span class='chip'>" & 'UF/REGIÃO'[Região/Grupo] & "</span>", "", 'UF/REGIÃO'[Região/Grupo], ASC )
                    & IF( COUNTROWS(VALUES('UF/REGIÃO'[Região/Grupo])) > 5,
                        "<span class='more'>+" & (COUNTROWS(VALUES('UF/REGIÃO'[Região/Grupo])) - 5) & "</span>", "" ) )
                VAR Filtro7 = IF( ISFILTERED('UF/REGIÃO'[UF]),
                    CONCATENATEX( TOPN(5, VALUES('UF/REGIÃO'[UF]), 'UF/REGIÃO'[UF], ASC),
                        "<span class='chip'>" & 'UF/REGIÃO'[UF] & "</span>", "", 'UF/REGIÃO'[UF], ASC )
                    & IF( COUNTROWS(VALUES('UF/REGIÃO'[UF])) > 5,
                        "<span class='more'>+" & (COUNTROWS(VALUES('UF/REGIÃO'[UF])) - 5) & "</span>", "" ) )

                VAR TotalFiltros =
                    (NOT ISBLANK(FiltroACC)) +
                    (NOT ISBLANK(Filtro1)) + (NOT ISBLANK(Filtro2)) + (NOT ISBLANK(Filtro3)) +
                    (NOT ISBLANK(Filtro4)) + (NOT ISBLANK(Filtro5)) + (NOT ISBLANK(Filtro6)) +
                    (NOT ISBLANK(Filtro7))

                VAR CSS =
                "<style>
                 .bar{font-family:'Segoe UI',Arial,sans-serif;display:flex;align-items:center;gap:12px;box-sizing:border-box;
                   width:100%;min-height:36px;padding:6px 12px;background:#FFFBEA;border:1px solid #FBE08A;
                   border-left:5px solid #FEC200;border-radius:8px}
                 .head{display:flex;align-items:center;gap:6px;flex:0 0 auto;font-size:11px;font-weight:800;
                   letter-spacing:.06em;text-transform:uppercase;color:#6B5000}
                 .badge{background:#FEC200;color:#1F1F1F;border-radius:999px;min-width:20px;height:20px;padding:0 6px;
                   display:inline-flex;align-items:center;justify-content:center;font-size:11px;font-weight:800;box-sizing:border-box}
                 .list{display:flex;flex-wrap:wrap;align-items:center;gap:6px 14px;flex:1 1 auto}
                 .grp{display:flex;align-items:center;gap:5px}
                 .lbl{font-size:11px;font-weight:700;color:#B38600}
                 .chips{display:flex;flex-wrap:wrap;gap:4px}
                 .chip{background:#fff;color:#4A3800;border:1px solid #FBE08A;border-radius:999px;padding:2px 9px;font-size:11px;
                   max-width:180px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
                 .more{background:#FEC200;color:#1F1F1F;border-radius:999px;padding:2px 8px;font-size:11px;font-weight:800}
                 .none{font-size:11px;color:#8A7A3A;font-style:italic}
                </style>"

                VAR Grupos =
                    IF( NOT ISBLANK(FiltroACC), "<div class='grp'><span class='lbl'>Usuario:</span><div class='chips'>" & FiltroACC & "</div></div>", "" ) &
                    IF( NOT ISBLANK(Filtro1), "<div class='grp'><span class='lbl'>Ano:</span><div class='chips'>" & Filtro1 & "</div></div>", "" ) &
                    IF( NOT ISBLANK(Filtro2), "<div class='grp'><span class='lbl'>Mês:</span><div class='chips'>" & Filtro2 & "</div></div>", "" ) &
                    IF( NOT ISBLANK(Filtro3), "<div class='grp'><span class='lbl'>Grupo:</span><div class='chips'>" & Filtro3 & "</div></div>", "" ) &
                    IF( NOT ISBLANK(Filtro4), "<div class='grp'><span class='lbl'>Cliente:</span><div class='chips'>" & Filtro4 & "</div></div>", "" ) &
                    IF( NOT ISBLANK(Filtro5), "<div class='grp'><span class='lbl'>Representante:</span><div class='chips'>" & Filtro5 & "</div></div>", "" ) &
                    IF( NOT ISBLANK(Filtro6), "<div class='grp'><span class='lbl'>Região:</span><div class='chips'>" & Filtro6 & "</div></div>", "" ) &
                    IF( NOT ISBLANK(Filtro7), "<div class='grp'><span class='lbl'>UF:</span><div class='chips'>" & Filtro7 & "</div></div>", "" )

                RETURN
                CSS &
                "<div class='bar'>
                    <div class='head'>&#9660; Filtros aplicados <span class='badge'>" & TotalFiltros & "</span></div>
                    <div class='list'>" &
                        IF( TotalFiltros = 0, "<span class='none'>Nenhum filtro aplicado</span>", Grupos ) &
                    "</div>
                </div>"
```

### 00. DRE A.H ( % )

Análise horizontal: 00. DRE ÷ Receita Operacional Bruta, mostrando cada linha como % da receita.

Formato: `0.0%;-0.0%;0.0%`

```dax
VAR _Receita = CALCULATE( [02. Receita Operacional Bruta ( R$ )] )

                VAR _TOTAL = 
                DIVIDE(
                    _Medidas[00. DRE],
                    _Receita ,
                    0 )

                RETURN
                _TOTAL
```

Usa: `00. DRE`, `02. Receita Operacional Bruta ( R$ )`

### Valor xSenha

Lê o número escolhido na tabela xSenha (senha digitada pelo usuário).

Formato: `0`

```dax
SELECTEDVALUE('xSenha'[xSenha])
```

### .Check Senha

Compara a senha digitada com a senha do usuário selecionado em Senhas/Gerente. Devolve "SELECIONE UM USUÁRIO", "SENHA INCORRETA" ou "HORIZONTAL" (acesso liberado).

```dax
VAR _Usuario = TRIM( SELECTEDVALUE( 'Senhas/Gerente'[Usuario] ) )
                VAR _SenhaDigitada = [Valor xSenha]
                VAR _SenhaCorreta = 
                    CALCULATE(
                        MAX( 'Senhas/Gerente'[Senha] ),
                        'Senhas/Gerente'[Usuario] = _Usuario
                    )
                RETURN
                SWITCH(
                    TRUE(),
                    ISBLANK( _Usuario ), "SELECIONE UM USUÁRIO",
                    _SenhaDigitada <> _SenhaCorreta, "SENHA INCORRETA",
                    "HORIZONTAL"
                )
```

Usa: `Valor xSenha`

## 01. Totais

### 01. Total Valor Bruto ( R$ )

Soma VALOR_VENDA do fato. É a medida-mãe da receita, devolução e todas as auxiliares da DRE a usam.

```dax
SUM( TB_FatosDRE[VALOR_VENDA] )
```

### 01. Total Valor Impostos ( R$ )

Soma TOTAL_IMPOSTOS do fato, sem filtro de operação.

```dax
SUM( TB_FatosDRE[TOTAL_IMPOSTOS] )
```

### 01. Total Volume ( PCTs )

Soma QTD_ITEM (pacotes) do fato.

```dax
SUM( TB_FatosDRE[QTD_ITEM] )
```

### 01. Total CPV ItemVenda ( R$ )

Soma CPV_ITEM_VENDA, o custo variável (matéria-prima + embalagem) por item.

```dax
SUM( TB_FatosDRE[CPV_ITEM_VENDA] )
```

### 01. Total CPV Fixo ( R$ )

Soma CPV_PROC_FIXO, o custo fixo de fabricação rateado por item.

```dax
SUM( TB_FatosDRE[CPV_PROC_FIXO] )
```

### 01. Total Comissao ( R$ )

Soma VALOR_COMISSAO.

```dax
SUM( TB_FatosDRE[VALOR_COMISSAO] )
```

### 01. Total Fretes ( R$ )

Soma FRETE.

```dax
SUM( TB_FatosDRE[FRETE] )
```

### 01. Total Verbas Fixas ( R$ )

Soma VERBA_FIXA.

```dax
SUM( TB_FatosDRE[VERBA_FIXA] )
```

### 01. Total Verbas Variaveis

Soma VERBA_VARIAVEL.

```dax
SUM( TB_FatosDRE[VERBA_VARIAVEL] )
```

### 01. Total CPV Bonificacao

Soma VALOR_CPV_BONIF, o custo das bonificações.

```dax
SUM( TB_FatosDRE[VALOR_CPV_BONIF] )
```

## 02. Auxiliares Horizontal\00.Volume

### 02. Volume Total ( PCTs )

Volume das Saídas, excluindo CFOPs de bonificação/verba (5910, 6910, 7949) e CFOP em branco.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total Volume ( PCTs )],
                    TB_FatosDRE[OPERACAO] = "Saidas",
                    NOT TB_FatosDRE[CFOP] IN({"5910", "6910", "7949",BLANK()})
                )
```

Usa: `01. Total Volume ( PCTs )`

### 02. Volume Bonificado/Verba ( PCTs )

Volume das Saídas somente dos CFOPs de bonificação/verba (5910, 6910, 7949) ou em branco. É o espelho da medida de volume total.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total Volume ( PCTs )],
                    TB_FatosDRE[OPERACAO] = "Saidas",
                    TB_FatosDRE[CFOP] IN({"5910", "6910", "7949", BLANK() })
                )
```

Usa: `01. Total Volume ( PCTs )`

### 02. PV Bruto / Pacotes ( R$ )

Preço de venda bruto por pacote: Receita Operacional Bruta ÷ Volume Total. Devolve 0 se o volume for zero.

```dax
// calcula o Preço Bruto por Pacotes
                DIVIDE(
                    [02. Receita Operacional Bruta ( R$ )], [02. Volume Total ( PCTs )], 0 )
```

Usa: `02. Receita Operacional Bruta ( R$ )`, `02. Volume Total ( PCTs )`

## 02. Auxiliares Horizontal\01.Receita OP

### 02. Receita Operacional Bruta ( R$ )

Valor bruto só das Saídas, excluindo CFOPs que não são venda de produto (devolução de compra, sucata, material de consumo). Os CFOPs retirados estão comentados no próprio DAX.

Formato: `#,0`

```dax
// CFOPs RETIRADOS
                // 5201    Devolução de compra para industrialização    
                // 5102    Venda de sucatas
                // 6201    Devolução de compra p/ industrialização (interestadual)
                // 5556    Devolução de compra de material de uso/consumo c/ controle
                // 6556    Devolução de compra de material de uso/consumo (c/ e s/ controle)
                CALCULATE(
                    [01. Total Valor Bruto ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Saidas",
                    NOT TB_FatosDRE[CFOP] IN ({"5201","5102","6201","5556","6556"})
                )
```

Usa: `01. Total Valor Bruto ( R$ )`

## 02. Auxiliares Horizontal\02. Devolucoes

### 02. Devolucao e Cancelamentos ( R$ )

Valor bruto das linhas com OPERACAO = "Entradas", ou seja, as devoluções de venda.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total Valor Bruto ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Entradas" )
```

Usa: `01. Total Valor Bruto ( R$ )`

## 02. Auxiliares Horizontal\03. Impostos

### 02. Imposto de Vendas ( R$ )

Impostos das Saídas.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total Valor Impostos ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Saidas" )
```

Usa: `01. Total Valor Impostos ( R$ )`

### 02. Impostos Devolucoes ( R$ )

Impostos das Entradas (devoluções), usados para abater do imposto total.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total Valor Impostos ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Entradas" )
```

Usa: `01. Total Valor Impostos ( R$ )`

## 02. Auxiliares Horizontal\04. CPV

### 02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )

CPV variável apenas das Saídas.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total CPV ItemVenda ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Saidas"
                )
```

Usa: `01. Total CPV ItemVenda ( R$ )`

### 02. Gastos Gerais Fabricacao-GGF FIXO ( R$ )

CPV fixo (GGF) apenas das Saídas.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total CPV Fixo ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Saidas"
                )
```

Usa: `01. Total CPV Fixo ( R$ )`

### 02. Credito CPV - Devolucao Pro ACA

CPV das Entradas, que volta como crédito quando o produto acabado é devolvido.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total CPV ItemVenda ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Entradas"
                )
```

Usa: `01. Total CPV ItemVenda ( R$ )`

## 02. Auxiliares Horizontal\05. Despesas Variaveis

### 02. Comissoes ( R$ )

Comissão das Saídas menos a comissão das Entradas (estorno em devolução).

Formato: `#,0`

```dax
VAR _Saidas = 
                CALCULATE(
                    [01. Total Comissao ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Saidas" )

                VAR _Entradas = 
                CALCULATE(
                    [01. Total Comissao ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Entradas" )

                RETURN
                _Saidas - _Entradas
```

Usa: `01. Total Comissao ( R$ )`

### 02. Fretes Tereceiros ( R$ )

Frete das Saídas, com os mesmos CFOPs excluídos da receita.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total Fretes ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Saidas",
                    NOT TB_FatosDRE[CFOP] IN ({"5201","5102","6201","5556","6556"})
                )
```

Usa: `01. Total Fretes ( R$ )`

### 02. Verbas Fixas ( R$ )

Verba fixa das Saídas.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total Verbas Fixas ( R$ )],
                    TB_FatosDRE[OPERACAO] = "Saidas"
                )
```

Usa: `01. Total Verbas Fixas ( R$ )`

### 02. Verbas Variaveis ( R$ )

Verba variável das Saídas.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total Verbas Variaveis],
                    TB_FatosDRE[OPERACAO] = "Saidas"
                )
```

Usa: `01. Total Verbas Variaveis`

### 02. Bonificacao

CPV de bonificação das Saídas, com os mesmos CFOPs excluídos da receita.

Formato: `#,0`

```dax
CALCULATE(
                    [01. Total CPV Bonificacao],
                    TB_FatosDRE[OPERACAO] = "Saidas",
                    NOT TB_FatosDRE[CFOP] IN ({"5201","5102","6201","5556","6556"})
                )
```

Usa: `01. Total CPV Bonificacao`

## 03. Auxiliares Vertical

### 03. 04 - Margem Bruta ( R$ )

Receita − Devolução − Impostos líquidos (saídas − entradas) − CPV (variável saídas − variável entradas + fixo saídas).

```dax
VAR _RECEITA =  [03. 01 - Receita Bruta ( R$ )]

                VAR _DEVOLUCAO = [03. 02 - Devolucao]

                VAR _IMPOSTO = 
                    VAR vIMPSAIDAS = CALCULATE( [01. Total Valor Impostos ( R$ )], TB_FatosDRE[OPERACAO] = "Saidas" )
                    VAR vIMPENTRAD = CALCULATE( [01. Total Valor Impostos ( R$ )], TB_FatosDRE[OPERACAO] = "Entradas" )
                    RETURN
                    vIMPSAIDAS - vIMPENTRAD

                VAR _CPV = 
                    VAR vCPVSAIDAS = CALCULATE( [01. Total CPV ItemVenda ( R$ )], TB_FatosDRE[OPERACAO] = "Saidas" )
                    VAR vCPVENTRAD = CALCULATE( [01. Total CPV ItemVenda ( R$ )], TB_FatosDRE[OPERACAO] = "Entradas" )
                    VAR vCPVFIXOSA = CALCULATE( [01. Total CPV Fixo ( R$ )], TB_FatosDRE[OPERACAO] = "Saidas" )
                    RETURN
                    vCPVSAIDAS -  vCPVENTRAD + vCPVFIXOSA 

                VAR vTOTAL = _RECEITA - _DEVOLUCAO - _IMPOSTO  - _CPV 

                RETURN
                vTOTAL
```

Usa: `01. Total CPV Fixo ( R$ )`, `01. Total CPV ItemVenda ( R$ )`, `01. Total Valor Impostos ( R$ )`, `03. 01 - Receita Bruta ( R$ )`, `03. 02 - Devolucao`

### 03. 01 - Receita Bruta ( R$ )

Atalho para a Receita Operacional Bruta, usado na visão vertical.

```dax
[02. Receita Operacional Bruta ( R$ )]
```

Usa: `02. Receita Operacional Bruta ( R$ )`

### 03. 02 - Devolucao

Atalho para Devolução e Cancelamentos.

```dax
[02. Devolucao e Cancelamentos ( R$ )]
```

Usa: `02. Devolucao e Cancelamentos ( R$ )`

### 03. 03 - Margem Bruta ( % )

Margem Bruta ÷ Receita Bruta.

Formato: `0.0%;-0.0%;0.0%`

```dax
// Calculado percentual encima da Receita Bruta
                DIVIDE( [03. 04 - Margem Bruta ( R$ )], [03. 01 - Receita Bruta ( R$ )] , 0 )
```

Usa: `03. 01 - Receita Bruta ( R$ )`, `03. 04 - Margem Bruta ( R$ )`

### 03. 05 - Despesas Variaveis ( R$ )

Comissões líquidas + Fretes das Saídas + Bonificações líquidas (saídas − entradas).

```dax
VAR _COMISSOES = 
                    VAR vCOMISAIDAS = CALCULATE( [01. Total Comissao ( R$ )], TB_FatosDRE[OPERACAO] = "Saidas" )
                    VAR vCOMIENTRAD = CALCULATE( [01. Total Comissao ( R$ )], TB_FatosDRE[OPERACAO] = "Entradas" )
                    RETURN
                    vCOMISAIDAS - vCOMIENTRAD

                VAR _FRETES = CALCULATE( [01. Total Fretes ( R$ )], TB_FatosDRE[OPERACAO] = "Saidas" )

                VAR _BONIFICACOES =
                    VAR vBONISAIDAS = CALCULATE( [01. Total CPV Bonificacao], TB_FatosDRE[OPERACAO] = "Saidas" )
                    VAR vBONIENTRAD = CALCULATE( [01. Total CPV Bonificacao], TB_FatosDRE[OPERACAO] = "Entradas" )
                    RETURN
                    vBONISAIDAS - vBONIENTRAD

                VAR vTOTAL = _COMISSOES + _FRETES + _BONIFICACOES

                RETURN
                vTOTAL
```

Usa: `01. Total CPV Bonificacao`, `01. Total Comissao ( R$ )`, `01. Total Fretes ( R$ )`

### 03. 06 - Bonificacoes ( R$ )

CPV de bonificação das Saídas menos o das Entradas.

```dax
VAR _BONIFICACOES =
                    VAR vBONISAIDAS = CALCULATE( [01. Total CPV Bonificacao], TB_FatosDRE[OPERACAO] = "Saidas" )
                    VAR vBONIENTRAD = CALCULATE( [01. Total CPV Bonificacao], TB_FatosDRE[OPERACAO] = "Entradas" )
                    RETURN
                    vBONISAIDAS - vBONIENTRAD
                RETURN
                _BONIFICACOES
```

Usa: `01. Total CPV Bonificacao`

### 03. 07 - Verbas ( R$ )

Verbas fixas + verbas variáveis das Saídas.

```dax
VAR _FIXAS = 
                    CALCULATE( [01. Total Verbas Fixas ( R$ )], TB_FatosDRE[OPERACAO] = "Saidas" )
                VAR _VARIAVEIS = 
                    CALCULATE( [01. Total Verbas Variaveis], TB_FatosDRE[OPERACAO] = "Saidas" )
                RETURN
                _FIXAS + _VARIAVEIS
```

Usa: `01. Total Verbas Fixas ( R$ )`, `01. Total Verbas Variaveis`

### 03. 08 - Margem Contribuicao ( R$ )

Margem Bruta − Despesas Variáveis − Verbas.

```dax
VAR _MargemBruta = [03. 04 - Margem Bruta ( R$ )]
                VAR _DespVariavel = [03. 05 - Despesas Variaveis ( R$ )]
                VAR _Verbas = [03. 07 - Verbas ( R$ )]

                RETURN
                _MargemBruta - _DespVariavel - _Verbas
```

Usa: `03. 04 - Margem Bruta ( R$ )`, `03. 05 - Despesas Variaveis ( R$ )`, `03. 07 - Verbas ( R$ )`

### 03. 09 - Margem Contribuicao ( % )

Margem de Contribuição ÷ Receita Operacional Bruta.

Formato: `0.0%;-0.0%;0.0%`

```dax
// calculado encima da receita bruta 
                DIVIDE( [03. 08 - Margem Contribuicao ( R$ )], [02. Receita Operacional Bruta ( R$ )], 0 )
```

Usa: `02. Receita Operacional Bruta ( R$ )`, `03. 08 - Margem Contribuicao ( R$ )`
