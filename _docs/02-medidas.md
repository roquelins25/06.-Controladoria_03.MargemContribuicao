# Medidas (79)

Agrupadas por tabela e pasta de exibição. Cada medida traz pasta, formato, explicação em PT-BR e o DAX completo.

| Tabela | Medidas |
|---|---|
| _Medidas | 40 |
| _Medidas2 | 37 |

## Pontos de atenção

Observações factuais sobre o DAX, sem alterar nada. Conferir antes de mudar as medidas.

- **1. Subtotal de impostos (família Oracle, `00. DRE`):** a variável `_imp` vale `-( Imposto de Vendas + Impostos Devolucoes )`. Mas a linha analítica de impostos devolvidos (ID 50001) soma `+ Impostos Devolucoes`. Os dois não batem quando há impostos devolvidos. A versão da planilha (`00.DRE DRE`) usa `- Imposto + Impostos Devolucoes`, que é consistente com a analítica.
- **2. Filtro de CFOP sem efeito em `02.DRE Fretes Tereceiros ( R$ )`:** o filtro `NOT TB_FatosDRE[CFOP] IN (...)` é aplicado a uma tabela que não se relaciona com `DRE_Geral - Plan`. Por isso, não restringe o valor do frete da planilha.
- **3. Mistura de famílias:** `02. 8 - MRG CTRB` (planilha) usa `03. 07 - Verbas ( R$ )`, que vem de `TB_FatosDRE` (Oracle). Assim, a margem de contribuição da planilha depende de uma fonte que não é a própria planilha.

## Tabela `_Medidas`

### Pasta: 01. Totais

#### 01. Total Valor Bruto ( R$ )

Tabela: `_Medidas` · Pasta: 01. Totais · Formato: `-`

Soma de `VALOR_VENDA` em `TB_FatosDRE`, sem filtro de operação. É a base de quase todas as medidas de receita.

Colunas lidas: `TB_FatosDRE[VALOR_VENDA]`

```dax
SUM( TB_FatosDRE[VALOR_VENDA] )
```

#### 01. Total Valor Impostos ( R$ )

Tabela: `_Medidas` · Pasta: 01. Totais · Formato: `-`

Soma de `TOTAL_IMPOSTOS` em `TB_FatosDRE`.

Colunas lidas: `TB_FatosDRE[TOTAL_IMPOSTOS]`

```dax
SUM( TB_FatosDRE[TOTAL_IMPOSTOS] )
```

#### 01. Total Volume ( PCTs )

Tabela: `_Medidas` · Pasta: 01. Totais · Formato: `-`

Soma de `QTD_ITEM` (quantidade em pacotes) em `TB_FatosDRE`.

Colunas lidas: `TB_FatosDRE[QTD_ITEM]`

```dax
SUM( TB_FatosDRE[QTD_ITEM] )
```

#### 01. Total CPV ItemVenda ( R$ )

Tabela: `_Medidas` · Pasta: 01. Totais · Formato: `-`

Soma de `CPV_ITEM_VENDA` em `TB_FatosDRE`.

Colunas lidas: `TB_FatosDRE[CPV_ITEM_VENDA]`

```dax
SUM( TB_FatosDRE[CPV_ITEM_VENDA] )
```

#### 01. Total CPV Fixo ( R$ )

Tabela: `_Medidas` · Pasta: 01. Totais · Formato: `-`

Soma de `CPV_PROC_FIXO` em `TB_FatosDRE`.

Colunas lidas: `TB_FatosDRE[CPV_PROC_FIXO]`

```dax
SUM( TB_FatosDRE[CPV_PROC_FIXO] )
```

#### 01. Total Comissao ( R$ )

Tabela: `_Medidas` · Pasta: 01. Totais · Formato: `-`

Soma de `VALOR_COMISSAO` em `TB_FatosDRE`.

Colunas lidas: `TB_FatosDRE[VALOR_COMISSAO]`

```dax
SUM( TB_FatosDRE[VALOR_COMISSAO] )
```

#### 01. Total Fretes ( R$ )

Tabela: `_Medidas` · Pasta: 01. Totais · Formato: `-`

Soma de `FRETE` em `TB_FatosDRE`.

Colunas lidas: `TB_FatosDRE[FRETE]`

```dax
SUM( TB_FatosDRE[FRETE] )
```

#### 01. Total Verbas Fixas ( R$ )

Tabela: `_Medidas` · Pasta: 01. Totais · Formato: `-`

Soma de `VERBA_FIXA` em `TB_FatosDRE`.

Colunas lidas: `TB_FatosDRE[VERBA_FIXA]`

```dax
SUM( TB_FatosDRE[VERBA_FIXA] )
```

#### 01. Total Verbas Variaveis

Tabela: `_Medidas` · Pasta: 01. Totais · Formato: `-`

Soma de `VERBA_VARIAVEL` em `TB_FatosDRE`.

Colunas lidas: `TB_FatosDRE[VERBA_VARIAVEL]`

```dax
SUM( TB_FatosDRE[VERBA_VARIAVEL] )
```

#### 01. Total CPV Bonificacao

Tabela: `_Medidas` · Pasta: 01. Totais · Formato: `-`

Soma de `VALOR_CPV_BONIF` em `TB_FatosDRE`.

Colunas lidas: `TB_FatosDRE[VALOR_CPV_BONIF]`

```dax
SUM( TB_FatosDRE[VALOR_CPV_BONIF] )
```

### Pasta: 02. Auxiliares Horizontal\01.Receita OP

#### 02. Receita Operacional Bruta ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\01.Receita OP · Formato: `#,0`

Valor bruto de Saídas, excluindo CFOPs de devolução de compra, venda de sucata e similares (5201, 5102, 6201, 5556, 6556). Medida-mãe da receita.

Usa: `01. Total Valor Bruto ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`, `TB_FatosDRE[CFOP]`

```dax
// CFOPs RETIRADOS
// 5201	Devolução de compra para industrialização	
// 5102	Venda de sucatas
// 6201	Devolução de compra p/ industrialização (interestadual)
// 5556	Devolução de compra de material de uso/consumo c/ controle
// 6556	Devolução de compra de material de uso/consumo (c/ e s/ controle)
CALCULATE(
    [01. Total Valor Bruto ( R$ )],
    TB_FatosDRE[OPERACAO] = "Saidas",
    NOT TB_FatosDRE[CFOP] IN ({"5201","5102","6201","5556","6556"})
)
```

### Pasta: 02. Auxiliares Horizontal\02. Devolucoes

#### 02. Devolucao e Cancelamentos ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\02. Devolucoes · Formato: `#,0`

Valor bruto das linhas de Entradas (devoluções de venda e cancelamentos).

Usa: `01. Total Valor Bruto ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

```dax
CALCULATE(
    [01. Total Valor Bruto ( R$ )],
    TB_FatosDRE[OPERACAO] = "Entradas" )
```

### Pasta: 02. Auxiliares Horizontal\03. Impostos

#### 02. Imposto de Vendas ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\03. Impostos · Formato: `#,0`

Total de impostos das Saídas (`TOTAL_IMPOSTOS`).

Usa: `01. Total Valor Impostos ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

```dax
CALCULATE(
    [01. Total Valor Impostos ( R$ )],
    TB_FatosDRE[OPERACAO] = "Saidas" ) 
```

#### 02. Impostos Devolucoes ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\03. Impostos · Formato: `#,0`

Total de impostos das Entradas (impostos devolvidos).

Usa: `01. Total Valor Impostos ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

```dax
CALCULATE(
    [01. Total Valor Impostos ( R$ )],
    TB_FatosDRE[OPERACAO] = "Entradas" )
```

### Pasta: 02. Auxiliares Horizontal\00.Volume

#### 02. Volume Total ( PCTs )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\00.Volume · Formato: `#,0`

Volume das Saídas, excluindo CFOPs 5910, 6910, 7949 e BLANK (bonificação/verba).

Usa: `01. Total Volume ( PCTs )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`, `TB_FatosDRE[CFOP]`

```dax
CALCULATE(
    [01. Total Volume ( PCTs )],
    TB_FatosDRE[OPERACAO] = "Saidas",
    NOT TB_FatosDRE[CFOP] IN({"5910", "6910", "7949",BLANK()})
)
```

#### 02. Volume Bonificado/Verba ( PCTs )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\00.Volume · Formato: `#,0`

Volume das Saídas com CFOPs 5910, 6910, 7949 e BLANK (bonificação e verba).

Usa: `01. Total Volume ( PCTs )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`, `TB_FatosDRE[CFOP]`

```dax
CALCULATE(
    [01. Total Volume ( PCTs )],
    TB_FatosDRE[OPERACAO] = "Saidas",
    TB_FatosDRE[CFOP] IN({"5910", "6910", "7949", BLANK() })
)
```

#### 02. PV Bruto / Pacotes ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\00.Volume · Formato: `-`

Preço de venda bruto por pacote: Receita Operacional Bruta dividida pelo Volume Total.

Usa: `02. Receita Operacional Bruta ( R$ )`, `02. Volume Total ( PCTs )`

```dax
// calcula o Preço Bruto por Pacotes
DIVIDE(
    [02. Receita Operacional Bruta ( R$ )], [02. Volume Total ( PCTs )], 0 )
```

### Pasta: 02. Auxiliares Horizontal\04. CPV

#### 02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\04. CPV · Formato: `#,0`

CPV de item de venda das Saídas (custo variável de matéria-prima e embalagens).

Usa: `01. Total CPV ItemVenda ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

```dax
CALCULATE(
    [01. Total CPV ItemVenda ( R$ )],
    TB_FatosDRE[OPERACAO] = "Saidas"
)
```

#### 02. Gastos Gerais Fabricacao-GGF FIXO ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\04. CPV · Formato: `#,0`

CPV fixo das Saídas (gastos gerais de fabricação).

Usa: `01. Total CPV Fixo ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

```dax
CALCULATE(
    [01. Total CPV Fixo ( R$ )],
    TB_FatosDRE[OPERACAO] = "Saidas"
)
```

#### 02. Credito CPV - Devolucao Pro ACA

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\04. CPV · Formato: `#,0`

CPV de item das Entradas (crédito de CPV pela devolução).

Usa: `01. Total CPV ItemVenda ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

```dax
CALCULATE(
    [01. Total CPV ItemVenda ( R$ )],
    TB_FatosDRE[OPERACAO] = "Entradas"
)
```

### Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis

#### 02. Comissoes ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis · Formato: `#,0`

Comissões de Saídas menos comissões de Entradas (líquido).

Usa: `01. Total Comissao ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

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

#### 02. Fretes Tereceiros ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis · Formato: `#,0`

Fretes das Saídas, excluindo os CFOPs de devolução e similares (5201, 5102, 6201, 5556, 6556).

Usa: `01. Total Fretes ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`, `TB_FatosDRE[CFOP]`

```dax
CALCULATE(
    [01. Total Fretes ( R$ )],
    TB_FatosDRE[OPERACAO] = "Saidas",
    NOT TB_FatosDRE[CFOP] IN ({"5201","5102","6201","5556","6556"})
)
```

#### 02. Verbas Fixas ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis · Formato: `#,0`

Verba fixa das Saídas.

Usa: `01. Total Verbas Fixas ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

```dax
CALCULATE(
    [01. Total Verbas Fixas ( R$ )],
    TB_FatosDRE[OPERACAO] = "Saidas"
)
```

#### 02. Verbas Variaveis ( R$ )

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis · Formato: `#,0`

Verba variável das Saídas.

Usa: `01. Total Verbas Variaveis`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

```dax
CALCULATE(
    [01. Total Verbas Variaveis],
    TB_FatosDRE[OPERACAO] = "Saidas"
)
```

#### 02. Bonificacao

Tabela: `_Medidas` · Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis · Formato: `#,0`

CPV de bonificação das Saídas (excluindo os CFOPs de devolução e similares) menos o das Entradas.

Usa: `01. Total CPV Bonificacao`

Colunas lidas: `TB_FatosDRE[OPERACAO]`, `TB_FatosDRE[CFOP]`

```dax
CALCULATE(
    [01. Total CPV Bonificacao],
    TB_FatosDRE[OPERACAO] = "Saidas",
    NOT TB_FatosDRE[CFOP] IN ({"5201","5102","6201","5556","6556"})
)
```

### Pasta: 03. Auxiliares Vertical

#### 03. 04 - Margem Bruta ( R$ )

Tabela: `_Medidas` · Pasta: 03. Auxiliares Vertical · Formato: `-`

Margem bruta em R$: Receita Bruta − Devolução − Impostos líquidos − CPV (variável e fixo). Versão vertical, usada na análise vertical.

Usa: `03. 01 - Receita Bruta ( R$ )`, `03. 02 - Devolucao`, `01. Total Valor Impostos ( R$ )`, `01. Total CPV ItemVenda ( R$ )`, `01. Total CPV Fixo ( R$ )`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

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

#### 03. 01 - Receita Bruta ( R$ )

Tabela: `_Medidas` · Pasta: 03. Auxiliares Vertical · Formato: `-`

Repassa a Receita Operacional Bruta.

Usa: `02. Receita Operacional Bruta ( R$ )`

```dax
[02. Receita Operacional Bruta ( R$ )]
```

#### 03. 02 - Devolucao

Tabela: `_Medidas` · Pasta: 03. Auxiliares Vertical · Formato: `-`

Repassa Devolução e Cancelamentos.

Usa: `02. Devolucao e Cancelamentos ( R$ )`

```dax
[02. Devolucao e Cancelamentos ( R$ )]
```

#### 03. 03 - Margem Bruta ( % )

Tabela: `_Medidas` · Pasta: 03. Auxiliares Vertical · Formato: `0.0%;-0.0%;0.0%`

Margem Bruta em R$ dividida pela Receita Bruta (formato %).

Usa: `03. 04 - Margem Bruta ( R$ )`, `03. 01 - Receita Bruta ( R$ )`

```dax
// Calculado percentual encima da Receita Bruta
DIVIDE( [03. 04 - Margem Bruta ( R$ )], [03. 01 - Receita Bruta ( R$ )] , 0 )
```

#### 03. 05 - Despesas Variaveis ( R$ )

Tabela: `_Medidas` · Pasta: 03. Auxiliares Vertical · Formato: `-`

Comissões líquidas + fretes das Saídas + bonificações líquidas.

Usa: `01. Total Comissao ( R$ )`, `01. Total Fretes ( R$ )`, `01. Total CPV Bonificacao`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

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

#### 03. 06 - Bonificacoes ( R$ )

Tabela: `_Medidas` · Pasta: 03. Auxiliares Vertical · Formato: `-`

Bonificações líquidas (Saídas − Entradas).

Usa: `01. Total CPV Bonificacao`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

```dax
VAR _BONIFICACOES =
    VAR vBONISAIDAS = CALCULATE( [01. Total CPV Bonificacao], TB_FatosDRE[OPERACAO] = "Saidas" )
    VAR vBONIENTRAD = CALCULATE( [01. Total CPV Bonificacao], TB_FatosDRE[OPERACAO] = "Entradas" )
    RETURN
    vBONISAIDAS - vBONIENTRAD
RETURN
_BONIFICACOES
```

#### 03. 07 - Verbas ( R$ )

Tabela: `_Medidas` · Pasta: 03. Auxiliares Vertical · Formato: `-`

Verbas fixas + verbas variáveis das Saídas.

Usa: `01. Total Verbas Fixas ( R$ )`, `01. Total Verbas Variaveis`

Colunas lidas: `TB_FatosDRE[OPERACAO]`

```dax
VAR _FIXAS = 
    CALCULATE( [01. Total Verbas Fixas ( R$ )], TB_FatosDRE[OPERACAO] = "Saidas" )
VAR _VARIAVEIS = 
    CALCULATE( [01. Total Verbas Variaveis], TB_FatosDRE[OPERACAO] = "Saidas" )
RETURN
_FIXAS + _VARIAVEIS
```

#### 03. 08 - Margem Contribuicao ( R$ )

Tabela: `_Medidas` · Pasta: 03. Auxiliares Vertical · Formato: `-`

Margem de contribuição em R$: Margem Bruta − Despesas Variáveis − Verbas.

Usa: `03. 04 - Margem Bruta ( R$ )`, `03. 05 - Despesas Variaveis ( R$ )`, `03. 07 - Verbas ( R$ )`

```dax
VAR _MargemBruta = [03. 04 - Margem Bruta ( R$ )]
VAR _DespVariavel = [03. 05 - Despesas Variaveis ( R$ )]
VAR _Verbas = [03. 07 - Verbas ( R$ )]

RETURN
_MargemBruta - _DespVariavel - _Verbas
```

#### 03. 09 - Margem Contribuicao ( % )

Tabela: `_Medidas` · Pasta: 03. Auxiliares Vertical · Formato: `0.0%;-0.0%;0.0%`

Margem de contribuição em R$ dividida pela Receita Operacional Bruta.

Usa: `03. 08 - Margem Contribuicao ( R$ )`, `02. Receita Operacional Bruta ( R$ )`

```dax
// calculado encima da receita bruta 
DIVIDE( [03. 08 - Margem Contribuicao ( R$ )], [02. Receita Operacional Bruta ( R$ )], 0 )
```

### Pasta: (sem pasta)

#### 00. DRE

Tabela: `_Medidas` · Formato: `-`

Medida da DRE horizontal (família Oracle). Linha analítica (`A`): soma os IDs de `DRE_LV2` visíveis por um SWITCH (volume, receita, devoluções, impostos, CPV, frete, verbas, bonificação, comissões). Linha subtotal (`ST`): soma acumulada das linhas `A` (exceto volume) até o índice atual, o que gera Receita s/ Devolução, Receita Líquida, Margem Bruta e Margem de Contribuição. Deduções entram com sinal negativo. Na LV2, o ID 1 mostra PV por pacote quando a linha de detalhe está no escopo. Ver ponto de atenção 1.

Usa: `02. Receita Operacional Bruta ( R$ )`, `02. Devolucao e Cancelamentos ( R$ )`, `02. Imposto de Vendas ( R$ )`, `02. Impostos Devolucoes ( R$ )`, `02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )`, `02. Gastos Gerais Fabricacao-GGF FIXO ( R$ )`, `02. Credito CPV - Devolucao Pro ACA`, `02. Fretes Tereceiros ( R$ )`, `02. Verbas Fixas ( R$ )`, `02. Verbas Variaveis ( R$ )`, `02. Bonificacao`, `02. Comissoes ( R$ )`, `02. PV Bruto / Pacotes ( R$ )`, `02. Volume Total ( PCTs )`

Colunas lidas: `DRE_LV1[SUB]`, `DRE_LV1[ID]`, `DRE_LV2[ID]`, `DRE_LV2[DESC]`

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

#### HTML_Filtros_Ativos

Tabela: `_Medidas` · Formato: `-`

Monta em HTML a faixa de filtros aplicados no topo do relatório: usuário, ano, mês, grupo, cliente, representante, região e UF. Mostra até 5 valores por filtro e um contador com o total de filtros ativos.

Colunas lidas: `Senhas/Gerente[Usuario]`, `CALENDARIO DAX[Ano]`, `CALENDARIO DAX[Ano-Mes]`, `TB_REDE[XNOMGRE]`, `TB_CLIENTE[XNOMCLI]`, `TB_REP[NOMREP]`, `UF/REGIÃO[Região/Grupo]`, `UF/REGIÃO[UF]`

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

#### 00. DRE A.H ( % )

Tabela: `_Medidas` · Formato: `0.0%;-0.0%;0.0%`

Análise horizontal em percentual: a linha da DRE (`00. DRE`) dividida pela Receita Operacional Bruta.

Usa: `02. Receita Operacional Bruta ( R$ )`, `00. DRE`

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

#### 00. DRE ( texto )

Tabela: `_Medidas` · Formato: `-`

Texto formatado da DRE horizontal: PV por pacote em R$, volume sem casas decimais, demais linhas com 1 casa pela função `fxFORMAT`.

Usa: `00. DRE`

Colunas lidas: `DRE_LV1[ID]`, `DRE_LV2[ID]`, `DRE_LV2[DESC]`

```dax
VAR _v   = [00. DRE]
VAR _lv1 = SELECTEDVALUE( DRE_LV1[ID] )
VAR _lv2 = SELECTEDVALUE( DRE_LV2[ID] )
RETURN
SWITCH(
    TRUE(),
    ISBLANK( _v ), BLANK(),
    ISINSCOPE( DRE_LV2[DESC] ) && _lv2 = 1, FORMAT( _v, "R$ #,0.00" ),   -- PV por pacote
    _lv1 = "10001", FORMAT( _v, "#,0" ),                                  -- volume
    fxFORMAT( _v, 9 )                                             -- demais linhas: 1 casa
)
```

#### 00. DRE ( cor )

Tabela: `_Medidas` · Formato: `-`

Cor do texto da DRE horizontal: vermelho escuro quando negativo, preto caso contrário.

Usa: `00. DRE`

```dax
VAR _v = [00. DRE]
RETURN
IF( _v < 0, "#930202", "#000000" )
```

#### 00.BLANK

Tabela: `_Medidas` · Formato: `0`

Retorna BLANK(). Usada como valor vazio em visuais e formatações.

```dax
BLANK()
```

## Tabela `_Medidas2`

### Pasta: 01. Totais

#### 01.DRE Total Valor Bruto ( R$ )

Tabela: `_Medidas2` · Pasta: 01. Totais · Formato: `-`

Soma de `Valor_Venda` em `DRE_Geral - Plan`.

Colunas lidas: `DRE_Geral - Plan[Valor_Venda]`

```dax
SUM( 'DRE_Geral - Plan'[Valor_Venda] )
```

#### 01.DRE Total Impostos ( R$ )

Tabela: `_Medidas2` · Pasta: 01. Totais · Formato: `-`

Soma de `Total_Impostos` em `DRE_Geral - Plan`.

Colunas lidas: `DRE_Geral - Plan[Total_Impostos]`

```dax
SUM( 'DRE_Geral - Plan'[Total_Impostos] )
```

#### 01.DRE Total CPV ItemVenda

Tabela: `_Medidas2` · Pasta: 01. Totais · Formato: `-`

Soma de `CPV_Item_Venda` em `DRE_Geral - Plan`.

Colunas lidas: `DRE_Geral - Plan[CPV_Item_Venda]`

```dax
SUM( 'DRE_Geral - Plan'[CPV_Item_Venda] )
```

#### 01.DRE Total CPV Fixo

Tabela: `_Medidas2` · Pasta: 01. Totais · Formato: `-`

Soma de `CPV_Proc_Fixo` em `DRE_Geral - Plan`.

Colunas lidas: `DRE_Geral - Plan[CPV_Proc_Fixo]`

```dax
SUM('DRE_Geral - Plan'[CPV_Proc_Fixo])
```

#### 01.DRE Total Comissao

Tabela: `_Medidas2` · Pasta: 01. Totais · Formato: `-`

Soma de `Valor_Comissao` em `DRE_Geral - Plan`.

Colunas lidas: `DRE_Geral - Plan[Valor_Comissao]`

```dax
SUM( 'DRE_Geral - Plan'[Valor_Comissao] )
```

#### 01.DRE CPV Bonificacao

Tabela: `_Medidas2` · Pasta: 01. Totais · Formato: `-`

Soma de `Valor_CPV_Bonif` em `DRE_Geral - Plan`.

Colunas lidas: `DRE_Geral - Plan[Valor_CPV_Bonif]`

```dax
SUM( 'DRE_Geral - Plan'[Valor_CPV_Bonif] )
```

#### 01.DRE Total Fretes

Tabela: `_Medidas2` · Pasta: 01. Totais · Formato: `-`

Soma de `Frete` em `DRE_Geral - Plan`.

Colunas lidas: `DRE_Geral - Plan[Frete]`

```dax
SUM( 'DRE_Geral - Plan'[Frete] )
```

#### 01.DRE Total Verbas Fixas

Tabela: `_Medidas2` · Pasta: 01. Totais · Formato: `-`

Soma de `Verba_Fixa` em `DRE_Geral - Plan`.

Colunas lidas: `DRE_Geral - Plan[Verba_Fixa]`

```dax
SUM( 'DRE_Geral - Plan'[Verba_Fixa] )
```

#### 01.DRE Verbas Variaveis

Tabela: `_Medidas2` · Pasta: 01. Totais · Formato: `-`

Soma de `Verba_Variavel` em `DRE_Geral - Plan`.

Colunas lidas: `DRE_Geral - Plan[Verba_Variavel]`

```dax
SUM( 'DRE_Geral - Plan'[Verba_Variavel] )
```

#### 01.DRE Total Volume ( PCTs )

Tabela: `_Medidas2` · Pasta: 01. Totais · Formato: `-`

Soma de `Qtd_Item` em `DRE_Geral - Plan`.

Colunas lidas: `DRE_Geral - Plan[Qtd_Item]`

```dax
SUM( 'DRE_Geral - Plan'[Qtd_Item] )
```

### Pasta: 02. Auxiliares Vertical

#### 02. 1 - REC BRT

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Vertical · Formato: `-`

Receita Bruta da planilha: valor bruto de Saídas.

Usa: `01.DRE Total Valor Bruto ( R$ )`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
// Receita Bruta
CALCULATE(
    [01.DRE Total Valor Bruto ( R$ )],
    'DRE_Geral - Plan'[Operacao] = "Saidas" )
```

#### 02. 2 - DVL

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Vertical · Formato: `-`

Devolução da planilha: valor bruto de Entradas.

Usa: `01.DRE Total Valor Bruto ( R$ )`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
// Devolucao
CALCULATE(
    [01.DRE Total Valor Bruto ( R$ )],
    'DRE_Geral - Plan'[Operacao] = "Entradas" )
```

#### 02. 3 - MRG BRT

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Vertical · Formato: `-`

Margem Bruta da planilha em R$: Receita − Devolução − Impostos líquidos − CPV (variável e fixo).

Usa: `02. 1 - REC BRT`, `02. 2 - DVL`, `01.DRE Total Impostos ( R$ )`, `01.DRE Total CPV ItemVenda`, `01. Total CPV Fixo ( R$ )`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
// Margem Bruta
VAR _RECEITA =  [02. 1 - REC BRT]

VAR _DEVOLUCAO =  [02. 2 - DVL]

VAR _IMPOSTO = 
    VAR vIMPSAIDAS = CALCULATE( [01.DRE Total Impostos ( R$ )],  'DRE_Geral - Plan'[Operacao] = "Saidas" )
    VAR vIMPENTRAD = CALCULATE( [01.DRE Total Impostos ( R$ )], 'DRE_Geral - Plan'[Operacao]  = "Entradas" )
    RETURN
    vIMPSAIDAS - vIMPENTRAD

VAR _CPV = 
    VAR vCPVSAIDAS = CALCULATE( [01.DRE Total CPV ItemVenda], 'DRE_Geral - Plan'[Operacao]  = "Saidas" )
    VAR vCPVENTRAD = CALCULATE( [01.DRE Total CPV ItemVenda], 'DRE_Geral - Plan'[Operacao]  = "Entradas" )
    VAR vCPVFIXOSA = CALCULATE( [01. Total CPV Fixo ( R$ )], 'DRE_Geral - Plan'[Operacao]  = "Saidas" )
    RETURN
    vCPVSAIDAS -  vCPVENTRAD + vCPVFIXOSA 

VAR vTOTAL = _RECEITA - _DEVOLUCAO - _IMPOSTO  - _CPV 

RETURN
vTOTAL
```

#### 02. 4 - % MRG BRT

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Vertical · Formato: `0.00%;-0.00%;0.00%`

Margem Bruta da planilha dividida pela Receita Bruta (formato %, 2 casas).

Usa: `02. 3 - MRG BRT`, `02. 1 - REC BRT`

```dax
// percentual Margem Bruta vs Receita Bruta
DIVIDE( [02. 3 - MRG BRT], [02. 1 - REC BRT], 0 )
```

#### 02. 5 - OTRS DESP VAR

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Vertical · Formato: `-`

Outras despesas variáveis da planilha: comissões líquidas + fretes das Saídas + bonificações líquidas.

Usa: `01.DRE Total Comissao`, `01.DRE Total Fretes`, `01.DRE CPV Bonificacao`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
// Outras Despesas Variaveis
VAR _COMISSOES = 
    VAR vCOMISAIDAS = CALCULATE( [01.DRE Total Comissao], 'DRE_Geral - Plan'[Operacao] = "Saidas" ) 
    VAR vCOMIENTRAD = CALCULATE( [01.DRE Total Comissao], 'DRE_Geral - Plan'[Operacao] = "Entradas" )
    RETURN
    vCOMISAIDAS - vCOMIENTRAD

VAR _FRETES = CALCULATE( [01.DRE Total Fretes], 'DRE_Geral - Plan'[Operacao] = "Saidas" )

VAR _BONIFICACOES =
    VAR vBONISAIDAS = CALCULATE( [01.DRE CPV Bonificacao], 'DRE_Geral - Plan'[Operacao] = "Saidas" )
    VAR vBONIENTRAD = CALCULATE( [01.DRE CPV Bonificacao], 'DRE_Geral - Plan'[Operacao] = "Entradas" )
    RETURN
    vBONISAIDAS - vBONIENTRAD

VAR vTOTAL = _COMISSOES + _FRETES + _BONIFICACOES

RETURN
vTOTAL
```

#### 02. 6 - BNF

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Vertical · Formato: `-`

Bonificações líquidas da planilha (Saídas − Entradas).

Usa: `01.DRE CPV Bonificacao`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
// Bonificações
VAR _BONIFICACOES =
    VAR vBONISAIDAS = CALCULATE( [01.DRE CPV Bonificacao], 'DRE_Geral - Plan'[Operacao] = "Saidas" )
    VAR vBONIENTRAD = CALCULATE( [01.DRE CPV Bonificacao], 'DRE_Geral - Plan'[Operacao] = "Entradas" )
    RETURN
    vBONISAIDAS - vBONIENTRAD
RETURN
_BONIFICACOES
```

#### 02. 7 - VERBAS

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Vertical · Formato: `-`

Verbas fixas + variáveis das Saídas da planilha.

Usa: `01.DRE Total Verbas Fixas`, `01.DRE Verbas Variaveis`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
// calcula verbas fixas e variaveis 
VAR _FIXAS = 
    CALCULATE( [01.DRE Total Verbas Fixas], 'DRE_Geral - Plan'[Operacao] = "Saidas" )
VAR _VARIAVEIS = 
    CALCULATE( [01.DRE Verbas Variaveis], 'DRE_Geral - Plan'[Operacao] = "Saidas" )
RETURN
_FIXAS + _VARIAVEIS
```

#### 02. 8 - MRG CTRB

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Vertical · Formato: `-`

Margem de contribuição da planilha em R$: Margem Bruta − Outras Despesas Variáveis − Verbas. A parcela de verbas usa `03. 07 - Verbas ( R$ )`, que é da família Oracle. Ver ponto de atenção 3.

Usa: `02. 3 - MRG BRT`, `02. 5 - OTRS DESP VAR`, `03. 07 - Verbas ( R$ )`

```dax
// Margem de Contribuicao
VAR _MargemBruta = [02. 3 - MRG BRT]
VAR _DespVariavel = [02. 5 - OTRS DESP VAR]
VAR _Verbas = [03. 07 - Verbas ( R$ )]

RETURN
_MargemBruta - _DespVariavel - _Verbas
```

#### 02. 9 - % MRG x RB

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Vertical · Formato: `0.00%;-0.00%;0.00%`

Margem de contribuição da planilha dividida pela Receita Bruta (formato %, 2 casas).

Usa: `02. 8 - MRG CTRB`, `02. 1 - REC BRT`

```dax
// calculado encima da receita bruta 
DIVIDE( [02. 8 - MRG CTRB], [02. 1 - REC BRT], 0 )
```

### Pasta: 02. Auxiliares Horizontal\00.Volume

#### 02.DRE PV Bruto / Pacotes ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\00.Volume · Formato: `-`

Preço bruto por pacote da planilha: valor bruto dividido pelo volume total.

Usa: `01.DRE Total Valor Bruto ( R$ )`, `01.DRE Total Volume ( PCTs )`

```dax
// calcula o Preço Bruto por Pacotes
DIVIDE(
    [01.DRE Total Valor Bruto ( R$ )], [01.DRE Total Volume ( PCTs )], 0 )
```

#### 02.DRE Volume Bonificado/Verba ( PCTs )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\00.Volume · Formato: `#,0`

Volume das Saídas da planilha (sem filtro de CFOP).

Usa: `01.DRE Total Volume ( PCTs )`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
CALCULATE(
    [01.DRE Total Volume ( PCTs )],
    'DRE_Geral - Plan'[Operacao] = "Saidas"
)
```

#### 02.DRE Volume Total ( PCTs )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\00.Volume · Formato: `#,0`

Volume das Saídas da planilha, excluindo CFOPs 5910, 6910, 7949 e BLANK.

Usa: `01.DRE Total Volume ( PCTs )`

Colunas lidas: `DRE_Geral - Plan[Operacao]`, `DRE_Geral - Plan[CFOP]`

```dax
CALCULATE(
    [01.DRE Total Volume ( PCTs )],
    'DRE_Geral - Plan'[Operacao] = "Saidas",
    NOT 'DRE_Geral - Plan'[CFOP] IN({"5910", "6910", "7949",BLANK()})
)
```

### Pasta: 02. Auxiliares Horizontal\01.Receita OP

#### 02.DRE Receita Operacional Bruta ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\01.Receita OP · Formato: `-`

Valor bruto de Saídas da planilha.

Usa: `01.DRE Total Valor Bruto ( R$ )`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
CALCULATE(
    [01.DRE Total Valor Bruto ( R$ )],
    'DRE_Geral - Plan'[Operacao] = "Saidas"
)
```

### Pasta: 02. Auxiliares Horizontal\02. Devolucoes

#### 02.DRE Devolucao e Cancelamentos ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\02. Devolucoes · Formato: `-`

Valor bruto de Entradas da planilha.

Usa: `01.DRE Total Valor Bruto ( R$ )`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
CALCULATE(
    [01.DRE Total Valor Bruto ( R$ )],
    'DRE_Geral - Plan'[Operacao] = "Entradas" )
```

### Pasta: 02. Auxiliares Horizontal\03. Impostos

#### 02.DRE Imposto de Vendas ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\03. Impostos · Formato: `-`

Impostos das Saídas da planilha.

Usa: `01.DRE Total Impostos ( R$ )`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
CALCULATE(
    [01.DRE Total Impostos ( R$ )],
    'DRE_Geral - Plan'[Operacao] = "Saidas" ) 
```

#### 02.DRE Impostos Devolucoes ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\03. Impostos · Formato: `-`

Impostos das Entradas da planilha.

Usa: `01.DRE Total Impostos ( R$ )`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
CALCULATE(
    [01.DRE Total Impostos ( R$ )],
    'DRE_Geral - Plan'[Operacao] = "Entradas" )
```

### Pasta: 02. Auxiliares Horizontal\04. CPV

#### 02.DRE Credito CPV - Devolucao Pro ACA

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\04. CPV · Formato: `#,0`

CPV de item das Entradas da planilha.

Usa: `01.DRE Total CPV ItemVenda`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
CALCULATE(
    [01.DRE Total CPV ItemVenda],
    'DRE_Geral - Plan'[Operacao] = "Entradas"
)
```

#### 02.DRE Gastos Gerais Fabricacao-GGF FIXO ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\04. CPV · Formato: `#,0`

CPV fixo das Saídas da planilha.

Usa: `01.DRE Total CPV Fixo`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
CALCULATE(
    [01.DRE Total CPV Fixo],
    'DRE_Geral - Plan'[Operacao] = "Saidas"
)
```

#### 02.DRE Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\04. CPV · Formato: `#,0`

CPV de item das Saídas da planilha.

Usa: `01.DRE Total CPV ItemVenda`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
CALCULATE(
    [01.DRE Total CPV ItemVenda],
    'DRE_Geral - Plan'[Operacao] = "Saidas"
)
```

### Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis

#### 02.DRE Bonificacao

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis · Formato: `#,0`

Bonificação líquida da planilha (Saídas − Entradas).

Usa: `01.DRE CPV Bonificacao`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
VAR _SAIDAS = 
CALCULATE(
    [01.DRE CPV Bonificacao],
    'DRE_Geral - Plan'[Operacao] = "Saidas"
)
VAR _ENTRADAS = 
CALCULATE(
    [01.DRE CPV Bonificacao],
    'DRE_Geral - Plan'[Operacao] = "Entradas"
)
RETURN
_SAIDAS - _ENTRADAS
```

#### 02.DRE Comissoes ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis · Formato: `#,0`

Comissões líquidas da planilha (Saídas − Entradas).

Usa: `01.DRE Total Comissao`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
VAR _Saidas = 
CALCULATE(
    [01.DRE Total Comissao],
    'DRE_Geral - Plan'[Operacao] = "Saidas" )

VAR _Entradas = 
CALCULATE(
    [01.DRE Total Comissao],
    'DRE_Geral - Plan'[Operacao] = "Entradas" )

RETURN
_Saidas - _Entradas
```

#### 02.DRE Fretes Tereceiros ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis · Formato: `#,0`

Fretes das Saídas da planilha. O filtro de CFOP aplica-se a `TB_FatosDRE`, que não se relaciona com `DRE_Geral - Plan`, então não restringe este cálculo. Ver ponto de atenção 2.

Usa: `01.DRE Total Fretes`

Colunas lidas: `DRE_Geral - Plan[Operacao]`, `TB_FatosDRE[CFOP]`

```dax
CALCULATE(
    [01.DRE Total Fretes],
    'DRE_Geral - Plan'[Operacao] = "Saidas",
    NOT TB_FatosDRE[CFOP] IN ({"5201","5102","6201","5556","6556"})
)
```

#### 02.DRE Verbas Fixas ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis · Formato: `#,0`

Verba fixa das Saídas da planilha.

Usa: `01.DRE Total Verbas Fixas`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
CALCULATE(
    [01.DRE Total Verbas Fixas],
    'DRE_Geral - Plan'[Operacao] = "Saidas"
)
```

#### 02.DRE Verbas Variaveis ( R$ )

Tabela: `_Medidas2` · Pasta: 02. Auxiliares Horizontal\05. Despesas Variaveis · Formato: `#,0`

Verba variável das Saídas da planilha.

Usa: `01.DRE Verbas Variaveis`

Colunas lidas: `DRE_Geral - Plan[Operacao]`

```dax
CALCULATE(
    [01.DRE Verbas Variaveis],
    'DRE_Geral - Plan'[Operacao] = "Saidas"
)
```

### Pasta: (sem pasta)

#### 00.DRE DRE

Tabela: `_Medidas2` · Formato: `-`

Medida da DRE da planilha. Mesma lógica de `00. DRE`, mas com as medidas da família `_Medidas2`. Linha `A` pelo SWITCH de `DRE_LV2`; linha `ST` pela soma acumulada. Ver ponto de atenção 1 (versão Oracle).

Usa: `02.DRE Receita Operacional Bruta ( R$ )`, `02.DRE Devolucao e Cancelamentos ( R$ )`, `02.DRE Imposto de Vendas ( R$ )`, `02.DRE Impostos Devolucoes ( R$ )`, `02.DRE Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )`, `02.DRE Gastos Gerais Fabricacao-GGF FIXO ( R$ )`, `02.DRE Credito CPV - Devolucao Pro ACA`, `02.DRE Fretes Tereceiros ( R$ )`, `02.DRE Verbas Fixas ( R$ )`, `02.DRE Verbas Variaveis ( R$ )`, `02.DRE Bonificacao`, `02.DRE Comissoes ( R$ )`, `02.DRE PV Bruto / Pacotes ( R$ )`, `02.DRE Volume Total ( PCTs )`

Colunas lidas: `DRE_LV1[SUB]`, `DRE_LV1[ID]`, `DRE_LV2[ID]`, `DRE_LV2[DESC]`

```dax
VAR _tipo = SELECTEDVALUE( DRE_LV1[SUB] )
VAR _cur  = VALUE( SELECTEDVALUE( DRE_LV1[ID] ) )

-- valor assinado de cada linha analítica (A) do LV1
VAR _rob  =  [02.DRE Receita Operacional Bruta ( R$ )]
VAR _dev  = -[02.DRE Devolucao e Cancelamentos ( R$ )]
VAR _imp  = - [02.DRE Imposto de Vendas ( R$ )] + [02.DRE Impostos Devolucoes ( R$ )] 
VAR _cpv  = -( [02.DRE Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )]
             + [02.DRE Gastos Gerais Fabricacao-GGF FIXO ( R$ )]
             - [02.DRE Credito CPV - Devolucao Pro ACA] )
VAR _desp = -( [02.DRE Fretes Tereceiros ( R$ )] + [02.DRE Verbas Fixas ( R$ )]
             + [02.DRE Verbas Variaveis ( R$ )] + [02.DRE Bonificacao] + [02.DRE Comissoes ( R$ )] )

VAR _analitico =
    SUMX(
        VALUES( DRE_LV2[ID] ),
        SWITCH(
            DRE_LV2[ID],
            1,    IF( ISINSCOPE( DRE_LV2[DESC] ), [02.DRE PV Bruto / Pacotes ( R$ )], [02.DRE Volume Total ( PCTs )] ) ,
            20001, _rob,
            30001, _dev,
            40001, -[02.DRE Imposto de Vendas ( R$ )],
            50001,  [02.DRE Impostos Devolucoes ( R$ )],
            60001, -[02.DRE Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )],
            70001, -[02.DRE Gastos Gerais Fabricacao-GGF FIXO ( R$ )],
            80001,  [02.DRE Credito CPV - Devolucao Pro ACA],
            90001, -[02.DRE Comissoes ( R$ )],
            10001, -[02.DRE Fretes Tereceiros ( R$ )],
            11001, -[02.DRE Verbas Fixas ( R$ )],
            12001, -[02.DRE Verbas Variaveis ( R$ )],
            13001, -[02.DRE Bonificacao]
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

#### 00.DRE DRE ( cor )

Tabela: `_Medidas2` · Formato: `-`

Cor do texto: vermelho escuro (`#930202`) quando o valor é negativo, preto (`#000000`) caso contrário.

Usa: `00.DRE DRE`

```dax
VAR _v = [00.DRE DRE]
RETURN
IF( _v < 0, "#930202", "#000000" )
```

#### 00.DRE DRE ( texto )

Tabela: `_Medidas2` · Formato: `-`

Texto formatado da DRE da planilha: PV por pacote em R$ com 2 casas, volume sem casas decimais, demais linhas com 1 casa pela função `fxFORMAT`.

Usa: `00.DRE DRE`

Colunas lidas: `DRE_LV1[ID]`, `DRE_LV2[ID]`, `DRE_LV2[DESC]`

```dax
VAR _v   = [00.DRE DRE]
VAR _lv1 = SELECTEDVALUE( DRE_LV1[ID] )
VAR _lv2 = SELECTEDVALUE( DRE_LV2[ID] )
RETURN
SWITCH(
    TRUE(),
    ISBLANK( _v ), BLANK(),
    ISINSCOPE( DRE_LV2[DESC] ) && _lv2 = 1, FORMAT( _v, "R$ #,0.00" ),   -- PV por pacote
    _lv1 = "10001", FORMAT( _v, "#,0" ),                                  -- volume
    fxFORMAT( _v, 9 )                                             -- demais linhas: 1 casa
)
```
