# Dependências entre medidas

Medidas que nenhuma outra usa (o que o relatório consome) e tudo de que dependem. Cada medida-base mostra a coluna que lê.

## Árvore por medida final

### 02. Volume Bonificado/Verba ( PCTs )

```
02. Volume Bonificado/Verba ( PCTs )
└─ 01. Total Volume ( PCTs )
```

### 03. 03 - Margem Bruta ( % )

```
03. 03 - Margem Bruta ( % )
├─ 03. 04 - Margem Bruta ( R$ )
│  ├─ 03. 01 - Receita Bruta ( R$ )
│  │  └─ 02. Receita Operacional Bruta ( R$ )
│  │     └─ 01. Total Valor Bruto ( R$ )
│  ├─ 03. 02 - Devolucao
│  │  └─ 02. Devolucao e Cancelamentos ( R$ )
│  │     └─ 01. Total Valor Bruto ( R$ )
│  ├─ 01. Total Valor Impostos ( R$ )
│  ├─ 01. Total CPV ItemVenda ( R$ )
│  └─ 01. Total CPV Fixo ( R$ )
└─ 03. 01 - Receita Bruta ( R$ )
   └─ 02. Receita Operacional Bruta ( R$ )
      └─ 01. Total Valor Bruto ( R$ )
```

### 03. 06 - Bonificacoes ( R$ )

```
03. 06 - Bonificacoes ( R$ )
└─ 01. Total CPV Bonificacao
```

### 03. 09 - Margem Contribuicao ( % )

```
03. 09 - Margem Contribuicao ( % )
├─ 03. 08 - Margem Contribuicao ( R$ )
│  ├─ 03. 04 - Margem Bruta ( R$ )
│  │  ├─ 03. 01 - Receita Bruta ( R$ )
│  │  │  └─ 02. Receita Operacional Bruta ( R$ )
│  │  │     └─ 01. Total Valor Bruto ( R$ )
│  │  ├─ 03. 02 - Devolucao
│  │  │  └─ 02. Devolucao e Cancelamentos ( R$ )
│  │  │     └─ 01. Total Valor Bruto ( R$ )
│  │  ├─ 01. Total Valor Impostos ( R$ )
│  │  ├─ 01. Total CPV ItemVenda ( R$ )
│  │  └─ 01. Total CPV Fixo ( R$ )
│  ├─ 03. 05 - Despesas Variaveis ( R$ )
│  │  ├─ 01. Total Comissao ( R$ )
│  │  ├─ 01. Total Fretes ( R$ )
│  │  └─ 01. Total CPV Bonificacao
│  └─ 03. 07 - Verbas ( R$ )
│     ├─ 01. Total Verbas Fixas ( R$ )
│     └─ 01. Total Verbas Variaveis
└─ 02. Receita Operacional Bruta ( R$ )
   └─ 01. Total Valor Bruto ( R$ )
```

### HTML_Filtros_Ativos

(sem dependências de outras medidas) · lê `Senhas/Gerente[Usuario], CALENDARIO DAX[Ano], CALENDARIO DAX[Ano-Mes], TB_REDE[XNOMGRE], TB_CLIENTE[XNOMCLI], TB_REP[NOMREP], UF/REGIÃO[Região/Grupo], UF/REGIÃO[UF]`

### 00. DRE A.H ( % )

```
00. DRE A.H ( % )
├─ 02. Receita Operacional Bruta ( R$ )
│  └─ 01. Total Valor Bruto ( R$ )
└─ 00. DRE
   ├─ 02. Receita Operacional Bruta ( R$ )
   │  └─ 01. Total Valor Bruto ( R$ )
   ├─ 02. Devolucao e Cancelamentos ( R$ )
   │  └─ 01. Total Valor Bruto ( R$ )
   ├─ 02. Imposto de Vendas ( R$ )
   │  └─ 01. Total Valor Impostos ( R$ )
   ├─ 02. Impostos Devolucoes ( R$ )
   │  └─ 01. Total Valor Impostos ( R$ )
   ├─ 02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )
   │  └─ 01. Total CPV ItemVenda ( R$ )
   ├─ 02. Gastos Gerais Fabricacao-GGF FIXO ( R$ )
   │  └─ 01. Total CPV Fixo ( R$ )
   ├─ 02. Credito CPV - Devolucao Pro ACA
   │  └─ 01. Total CPV ItemVenda ( R$ )
   ├─ 02. Fretes Tereceiros ( R$ )
   │  └─ 01. Total Fretes ( R$ )
   ├─ 02. Verbas Fixas ( R$ )
   │  └─ 01. Total Verbas Fixas ( R$ )
   ├─ 02. Verbas Variaveis ( R$ )
   │  └─ 01. Total Verbas Variaveis
   ├─ 02. Bonificacao
   │  └─ 01. Total CPV Bonificacao
   ├─ 02. Comissoes ( R$ )
   │  └─ 01. Total Comissao ( R$ )
   ├─ 02. PV Bruto / Pacotes ( R$ )
   │  ├─ 02. Receita Operacional Bruta ( R$ )
   │  │  └─ 01. Total Valor Bruto ( R$ )
   │  └─ 02. Volume Total ( PCTs )
   │     └─ 01. Total Volume ( PCTs )
   └─ 02. Volume Total ( PCTs )
      └─ 01. Total Volume ( PCTs )
```

### 00. DRE ( texto )

```
00. DRE ( texto )
└─ 00. DRE
   ├─ 02. Receita Operacional Bruta ( R$ )
   │  └─ 01. Total Valor Bruto ( R$ )
   ├─ 02. Devolucao e Cancelamentos ( R$ )
   │  └─ 01. Total Valor Bruto ( R$ )
   ├─ 02. Imposto de Vendas ( R$ )
   │  └─ 01. Total Valor Impostos ( R$ )
   ├─ 02. Impostos Devolucoes ( R$ )
   │  └─ 01. Total Valor Impostos ( R$ )
   ├─ 02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )
   │  └─ 01. Total CPV ItemVenda ( R$ )
   ├─ 02. Gastos Gerais Fabricacao-GGF FIXO ( R$ )
   │  └─ 01. Total CPV Fixo ( R$ )
   ├─ 02. Credito CPV - Devolucao Pro ACA
   │  └─ 01. Total CPV ItemVenda ( R$ )
   ├─ 02. Fretes Tereceiros ( R$ )
   │  └─ 01. Total Fretes ( R$ )
   ├─ 02. Verbas Fixas ( R$ )
   │  └─ 01. Total Verbas Fixas ( R$ )
   ├─ 02. Verbas Variaveis ( R$ )
   │  └─ 01. Total Verbas Variaveis
   ├─ 02. Bonificacao
   │  └─ 01. Total CPV Bonificacao
   ├─ 02. Comissoes ( R$ )
   │  └─ 01. Total Comissao ( R$ )
   ├─ 02. PV Bruto / Pacotes ( R$ )
   │  ├─ 02. Receita Operacional Bruta ( R$ )
   │  │  └─ 01. Total Valor Bruto ( R$ )
   │  └─ 02. Volume Total ( PCTs )
   │     └─ 01. Total Volume ( PCTs )
   └─ 02. Volume Total ( PCTs )
      └─ 01. Total Volume ( PCTs )
```

### 00. DRE ( cor )

```
00. DRE ( cor )
└─ 00. DRE
   ├─ 02. Receita Operacional Bruta ( R$ )
   │  └─ 01. Total Valor Bruto ( R$ )
   ├─ 02. Devolucao e Cancelamentos ( R$ )
   │  └─ 01. Total Valor Bruto ( R$ )
   ├─ 02. Imposto de Vendas ( R$ )
   │  └─ 01. Total Valor Impostos ( R$ )
   ├─ 02. Impostos Devolucoes ( R$ )
   │  └─ 01. Total Valor Impostos ( R$ )
   ├─ 02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )
   │  └─ 01. Total CPV ItemVenda ( R$ )
   ├─ 02. Gastos Gerais Fabricacao-GGF FIXO ( R$ )
   │  └─ 01. Total CPV Fixo ( R$ )
   ├─ 02. Credito CPV - Devolucao Pro ACA
   │  └─ 01. Total CPV ItemVenda ( R$ )
   ├─ 02. Fretes Tereceiros ( R$ )
   │  └─ 01. Total Fretes ( R$ )
   ├─ 02. Verbas Fixas ( R$ )
   │  └─ 01. Total Verbas Fixas ( R$ )
   ├─ 02. Verbas Variaveis ( R$ )
   │  └─ 01. Total Verbas Variaveis
   ├─ 02. Bonificacao
   │  └─ 01. Total CPV Bonificacao
   ├─ 02. Comissoes ( R$ )
   │  └─ 01. Total Comissao ( R$ )
   ├─ 02. PV Bruto / Pacotes ( R$ )
   │  ├─ 02. Receita Operacional Bruta ( R$ )
   │  │  └─ 01. Total Valor Bruto ( R$ )
   │  └─ 02. Volume Total ( PCTs )
   │     └─ 01. Total Volume ( PCTs )
   └─ 02. Volume Total ( PCTs )
      └─ 01. Total Volume ( PCTs )
```

### 00.BLANK

(sem dependências de outras medidas)

### 02. 4 - % MRG BRT

```
02. 4 - % MRG BRT
├─ 02. 3 - MRG BRT
│  ├─ 02. 1 - REC BRT
│  │  └─ 01.DRE Total Valor Bruto ( R$ )
│  ├─ 02. 2 - DVL
│  │  └─ 01.DRE Total Valor Bruto ( R$ )
│  ├─ 01.DRE Total Impostos ( R$ )
│  ├─ 01.DRE Total CPV ItemVenda
│  └─ 01. Total CPV Fixo ( R$ )
└─ 02. 1 - REC BRT
   └─ 01.DRE Total Valor Bruto ( R$ )
```

### 02. 6 - BNF

```
02. 6 - BNF
└─ 01.DRE CPV Bonificacao
```

### 02. 7 - VERBAS

```
02. 7 - VERBAS
├─ 01.DRE Total Verbas Fixas
└─ 01.DRE Verbas Variaveis
```

### 02. 9 - % MRG x RB

```
02. 9 - % MRG x RB
├─ 02. 8 - MRG CTRB
│  ├─ 02. 3 - MRG BRT
│  │  ├─ 02. 1 - REC BRT
│  │  │  └─ 01.DRE Total Valor Bruto ( R$ )
│  │  ├─ 02. 2 - DVL
│  │  │  └─ 01.DRE Total Valor Bruto ( R$ )
│  │  ├─ 01.DRE Total Impostos ( R$ )
│  │  ├─ 01.DRE Total CPV ItemVenda
│  │  └─ 01. Total CPV Fixo ( R$ )
│  ├─ 02. 5 - OTRS DESP VAR
│  │  ├─ 01.DRE Total Comissao
│  │  ├─ 01.DRE Total Fretes
│  │  └─ 01.DRE CPV Bonificacao
│  └─ 03. 07 - Verbas ( R$ )
│     ├─ 01. Total Verbas Fixas ( R$ )
│     └─ 01. Total Verbas Variaveis
└─ 02. 1 - REC BRT
   └─ 01.DRE Total Valor Bruto ( R$ )
```

### 02.DRE Volume Bonificado/Verba ( PCTs )

```
02.DRE Volume Bonificado/Verba ( PCTs )
└─ 01.DRE Total Volume ( PCTs )
```

### 00.DRE DRE ( cor )

```
00.DRE DRE ( cor )
└─ 00.DRE DRE
   ├─ 02.DRE Receita Operacional Bruta ( R$ )
   │  └─ 01.DRE Total Valor Bruto ( R$ )
   ├─ 02.DRE Devolucao e Cancelamentos ( R$ )
   │  └─ 01.DRE Total Valor Bruto ( R$ )
   ├─ 02.DRE Imposto de Vendas ( R$ )
   │  └─ 01.DRE Total Impostos ( R$ )
   ├─ 02.DRE Impostos Devolucoes ( R$ )
   │  └─ 01.DRE Total Impostos ( R$ )
   ├─ 02.DRE Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )
   │  └─ 01.DRE Total CPV ItemVenda
   ├─ 02.DRE Gastos Gerais Fabricacao-GGF FIXO ( R$ )
   │  └─ 01.DRE Total CPV Fixo
   ├─ 02.DRE Credito CPV - Devolucao Pro ACA
   │  └─ 01.DRE Total CPV ItemVenda
   ├─ 02.DRE Fretes Tereceiros ( R$ )
   │  └─ 01.DRE Total Fretes
   ├─ 02.DRE Verbas Fixas ( R$ )
   │  └─ 01.DRE Total Verbas Fixas
   ├─ 02.DRE Verbas Variaveis ( R$ )
   │  └─ 01.DRE Verbas Variaveis
   ├─ 02.DRE Bonificacao
   │  └─ 01.DRE CPV Bonificacao
   ├─ 02.DRE Comissoes ( R$ )
   │  └─ 01.DRE Total Comissao
   ├─ 02.DRE PV Bruto / Pacotes ( R$ )
   │  ├─ 01.DRE Total Valor Bruto ( R$ )
   │  └─ 01.DRE Total Volume ( PCTs )
   └─ 02.DRE Volume Total ( PCTs )
      └─ 01.DRE Total Volume ( PCTs )
```

### 00.DRE DRE ( texto )

```
00.DRE DRE ( texto )
└─ 00.DRE DRE
   ├─ 02.DRE Receita Operacional Bruta ( R$ )
   │  └─ 01.DRE Total Valor Bruto ( R$ )
   ├─ 02.DRE Devolucao e Cancelamentos ( R$ )
   │  └─ 01.DRE Total Valor Bruto ( R$ )
   ├─ 02.DRE Imposto de Vendas ( R$ )
   │  └─ 01.DRE Total Impostos ( R$ )
   ├─ 02.DRE Impostos Devolucoes ( R$ )
   │  └─ 01.DRE Total Impostos ( R$ )
   ├─ 02.DRE Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )
   │  └─ 01.DRE Total CPV ItemVenda
   ├─ 02.DRE Gastos Gerais Fabricacao-GGF FIXO ( R$ )
   │  └─ 01.DRE Total CPV Fixo
   ├─ 02.DRE Credito CPV - Devolucao Pro ACA
   │  └─ 01.DRE Total CPV ItemVenda
   ├─ 02.DRE Fretes Tereceiros ( R$ )
   │  └─ 01.DRE Total Fretes
   ├─ 02.DRE Verbas Fixas ( R$ )
   │  └─ 01.DRE Total Verbas Fixas
   ├─ 02.DRE Verbas Variaveis ( R$ )
   │  └─ 01.DRE Verbas Variaveis
   ├─ 02.DRE Bonificacao
   │  └─ 01.DRE CPV Bonificacao
   ├─ 02.DRE Comissoes ( R$ )
   │  └─ 01.DRE Total Comissao
   ├─ 02.DRE PV Bruto / Pacotes ( R$ )
   │  ├─ 01.DRE Total Valor Bruto ( R$ )
   │  └─ 01.DRE Total Volume ( PCTs )
   └─ 02.DRE Volume Total ( PCTs )
      └─ 01.DRE Total Volume ( PCTs )
```

### .Check Senha

```
.Check Senha
└─ Valor xSenha
```

## Quem usa quem

| Medida | É usada por | Colunas que lê diretamente |
|---|---|---|
| 01. Total Valor Bruto ( R$ ) | 02. Devolucao e Cancelamentos ( R$ ), 02. Receita Operacional Bruta ( R$ ) | `TB_FatosDRE[VALOR_VENDA]` |
| 02. Receita Operacional Bruta ( R$ ) | 00. DRE, 00. DRE A.H ( % ), 02. PV Bruto / Pacotes ( R$ ), 03. 01 - Receita Bruta ( R$ ), 03. 09 - Margem Contribuicao ( % ) | `TB_FatosDRE[OPERACAO]`, `TB_FatosDRE[CFOP]` |
| 02. Devolucao e Cancelamentos ( R$ ) | 00. DRE, 03. 02 - Devolucao | `TB_FatosDRE[OPERACAO]` |
| 02. Imposto de Vendas ( R$ ) | 00. DRE | `TB_FatosDRE[OPERACAO]` |
| 01. Total Valor Impostos ( R$ ) | 02. Imposto de Vendas ( R$ ), 02. Impostos Devolucoes ( R$ ), 03. 04 - Margem Bruta ( R$ ) | `TB_FatosDRE[TOTAL_IMPOSTOS]` |
| 02. Impostos Devolucoes ( R$ ) | 00. DRE | `TB_FatosDRE[OPERACAO]` |
| 01. Total Volume ( PCTs ) | 02. Volume Bonificado/Verba ( PCTs ), 02. Volume Total ( PCTs ) | `TB_FatosDRE[QTD_ITEM]` |
| 02. Volume Total ( PCTs ) | 00. DRE, 02. PV Bruto / Pacotes ( R$ ) | `TB_FatosDRE[OPERACAO]`, `TB_FatosDRE[CFOP]` |
| 02. Volume Bonificado/Verba ( PCTs ) | (nenhuma, consumida direto no visual) | `TB_FatosDRE[OPERACAO]`, `TB_FatosDRE[CFOP]` |
| 02. PV Bruto / Pacotes ( R$ ) | 00. DRE |  |
| 02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ ) | 00. DRE | `TB_FatosDRE[OPERACAO]` |
| 01. Total CPV ItemVenda ( R$ ) | 02. Credito CPV - Devolucao Pro ACA, 02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ ), 03. 04 - Margem Bruta ( R$ ) | `TB_FatosDRE[CPV_ITEM_VENDA]` |
| 01. Total CPV Fixo ( R$ ) | 02. 3 - MRG BRT, 02. Gastos Gerais Fabricacao-GGF FIXO ( R$ ), 03. 04 - Margem Bruta ( R$ ) | `TB_FatosDRE[CPV_PROC_FIXO]` |
| 02. Gastos Gerais Fabricacao-GGF FIXO ( R$ ) | 00. DRE | `TB_FatosDRE[OPERACAO]` |
| 02. Credito CPV - Devolucao Pro ACA | 00. DRE | `TB_FatosDRE[OPERACAO]` |
| 01. Total Comissao ( R$ ) | 02. Comissoes ( R$ ), 03. 05 - Despesas Variaveis ( R$ ) | `TB_FatosDRE[VALOR_COMISSAO]` |
| 02. Comissoes ( R$ ) | 00. DRE | `TB_FatosDRE[OPERACAO]` |
| 01. Total Fretes ( R$ ) | 02. Fretes Tereceiros ( R$ ), 03. 05 - Despesas Variaveis ( R$ ) | `TB_FatosDRE[FRETE]` |
| 02. Fretes Tereceiros ( R$ ) | 00. DRE | `TB_FatosDRE[OPERACAO]`, `TB_FatosDRE[CFOP]` |
| 01. Total Verbas Fixas ( R$ ) | 02. Verbas Fixas ( R$ ), 03. 07 - Verbas ( R$ ) | `TB_FatosDRE[VERBA_FIXA]` |
| 01. Total Verbas Variaveis | 02. Verbas Variaveis ( R$ ), 03. 07 - Verbas ( R$ ) | `TB_FatosDRE[VERBA_VARIAVEL]` |
| 02. Verbas Fixas ( R$ ) | 00. DRE | `TB_FatosDRE[OPERACAO]` |
| 02. Verbas Variaveis ( R$ ) | 00. DRE | `TB_FatosDRE[OPERACAO]` |
| 01. Total CPV Bonificacao | 02. Bonificacao, 03. 05 - Despesas Variaveis ( R$ ), 03. 06 - Bonificacoes ( R$ ) | `TB_FatosDRE[VALOR_CPV_BONIF]` |
| 02. Bonificacao | 00. DRE | `TB_FatosDRE[OPERACAO]`, `TB_FatosDRE[CFOP]` |
| 03. 04 - Margem Bruta ( R$ ) | 03. 03 - Margem Bruta ( % ), 03. 08 - Margem Contribuicao ( R$ ) | `TB_FatosDRE[OPERACAO]` |
| 03. 01 - Receita Bruta ( R$ ) | 03. 03 - Margem Bruta ( % ), 03. 04 - Margem Bruta ( R$ ) |  |
| 03. 02 - Devolucao | 03. 04 - Margem Bruta ( R$ ) |  |
| 03. 03 - Margem Bruta ( % ) | (nenhuma, consumida direto no visual) |  |
| 03. 05 - Despesas Variaveis ( R$ ) | 03. 08 - Margem Contribuicao ( R$ ) | `TB_FatosDRE[OPERACAO]` |
| 03. 06 - Bonificacoes ( R$ ) | (nenhuma, consumida direto no visual) | `TB_FatosDRE[OPERACAO]` |
| 03. 07 - Verbas ( R$ ) | 02. 8 - MRG CTRB, 03. 08 - Margem Contribuicao ( R$ ) | `TB_FatosDRE[OPERACAO]` |
| 03. 08 - Margem Contribuicao ( R$ ) | 03. 09 - Margem Contribuicao ( % ) |  |
| 03. 09 - Margem Contribuicao ( % ) | (nenhuma, consumida direto no visual) |  |
| 00. DRE | 00. DRE ( cor ), 00. DRE ( texto ), 00. DRE A.H ( % ) | `DRE_LV1[SUB]`, `DRE_LV1[ID]`, `DRE_LV2[ID]`, `DRE_LV2[DESC]` |
| HTML_Filtros_Ativos | (nenhuma, consumida direto no visual) | `Senhas/Gerente[Usuario]`, `CALENDARIO DAX[Ano]`, `CALENDARIO DAX[Ano-Mes]`, `TB_REDE[XNOMGRE]`, `TB_CLIENTE[XNOMCLI]`, `TB_REP[NOMREP]`, `UF/REGIÃO[Região/Grupo]`, `UF/REGIÃO[UF]` |
| 00. DRE A.H ( % ) | (nenhuma, consumida direto no visual) |  |
| 00. DRE ( texto ) | (nenhuma, consumida direto no visual) | `DRE_LV1[ID]`, `DRE_LV2[ID]`, `DRE_LV2[DESC]` |
| 00. DRE ( cor ) | (nenhuma, consumida direto no visual) |  |
| 00.BLANK | (nenhuma, consumida direto no visual) |  |
| 01.DRE Total Valor Bruto ( R$ ) | 02. 1 - REC BRT, 02. 2 - DVL, 02.DRE Devolucao e Cancelamentos ( R$ ), 02.DRE PV Bruto / Pacotes ( R$ ), 02.DRE Receita Operacional Bruta ( R$ ) | `DRE_Geral - Plan[Valor_Venda]` |
| 02. 1 - REC BRT | 02. 3 - MRG BRT, 02. 4 - % MRG BRT, 02. 9 - % MRG x RB | `DRE_Geral - Plan[Operacao]` |
| 02. 2 - DVL | 02. 3 - MRG BRT | `DRE_Geral - Plan[Operacao]` |
| 02. 3 - MRG BRT | 02. 4 - % MRG BRT, 02. 8 - MRG CTRB | `DRE_Geral - Plan[Operacao]` |
| 01.DRE Total Impostos ( R$ ) | 02. 3 - MRG BRT, 02.DRE Imposto de Vendas ( R$ ), 02.DRE Impostos Devolucoes ( R$ ) | `DRE_Geral - Plan[Total_Impostos]` |
| 01.DRE Total CPV ItemVenda | 02. 3 - MRG BRT, 02.DRE Credito CPV - Devolucao Pro ACA, 02.DRE Materia Prima + Embalagens ( CPV Variaveis ) ( R$ ) | `DRE_Geral - Plan[CPV_Item_Venda]` |
| 01.DRE Total CPV Fixo | 02.DRE Gastos Gerais Fabricacao-GGF FIXO ( R$ ) | `DRE_Geral - Plan[CPV_Proc_Fixo]` |
| 02. 4 - % MRG BRT | (nenhuma, consumida direto no visual) |  |
| 02. 5 - OTRS DESP VAR | 02. 8 - MRG CTRB | `DRE_Geral - Plan[Operacao]` |
| 01.DRE Total Comissao | 02. 5 - OTRS DESP VAR, 02.DRE Comissoes ( R$ ) | `DRE_Geral - Plan[Valor_Comissao]` |
| 01.DRE CPV Bonificacao | 02. 5 - OTRS DESP VAR, 02. 6 - BNF, 02.DRE Bonificacao | `DRE_Geral - Plan[Valor_CPV_Bonif]` |
| 01.DRE Total Fretes | 02. 5 - OTRS DESP VAR, 02.DRE Fretes Tereceiros ( R$ ) | `DRE_Geral - Plan[Frete]` |
| 02. 6 - BNF | (nenhuma, consumida direto no visual) | `DRE_Geral - Plan[Operacao]` |
| 01.DRE Total Verbas Fixas | 02. 7 - VERBAS, 02.DRE Verbas Fixas ( R$ ) | `DRE_Geral - Plan[Verba_Fixa]` |
| 01.DRE Verbas Variaveis | 02. 7 - VERBAS, 02.DRE Verbas Variaveis ( R$ ) | `DRE_Geral - Plan[Verba_Variavel]` |
| 02. 7 - VERBAS | (nenhuma, consumida direto no visual) | `DRE_Geral - Plan[Operacao]` |
| 02. 8 - MRG CTRB | 02. 9 - % MRG x RB |  |
| 02. 9 - % MRG x RB | (nenhuma, consumida direto no visual) |  |
| 01.DRE Total Volume ( PCTs ) | 02.DRE PV Bruto / Pacotes ( R$ ), 02.DRE Volume Bonificado/Verba ( PCTs ), 02.DRE Volume Total ( PCTs ) | `DRE_Geral - Plan[Qtd_Item]` |
| 02.DRE PV Bruto / Pacotes ( R$ ) | 00.DRE DRE |  |
| 02.DRE Receita Operacional Bruta ( R$ ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Devolucao e Cancelamentos ( R$ ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Imposto de Vendas ( R$ ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Impostos Devolucoes ( R$ ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Credito CPV - Devolucao Pro ACA | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Gastos Gerais Fabricacao-GGF FIXO ( R$ ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Materia Prima + Embalagens ( CPV Variaveis ) ( R$ ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Bonificacao | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Comissoes ( R$ ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Fretes Tereceiros ( R$ ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]`, `TB_FatosDRE[CFOP]` |
| 02.DRE Verbas Fixas ( R$ ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Verbas Variaveis ( R$ ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Volume Bonificado/Verba ( PCTs ) | (nenhuma, consumida direto no visual) | `DRE_Geral - Plan[Operacao]` |
| 02.DRE Volume Total ( PCTs ) | 00.DRE DRE | `DRE_Geral - Plan[Operacao]`, `DRE_Geral - Plan[CFOP]` |
| 00.DRE DRE | 00.DRE DRE ( cor ), 00.DRE DRE ( texto ) | `DRE_LV1[SUB]`, `DRE_LV1[ID]`, `DRE_LV2[ID]`, `DRE_LV2[DESC]` |
| 00.DRE DRE ( cor ) | (nenhuma, consumida direto no visual) |  |
| 00.DRE DRE ( texto ) | (nenhuma, consumida direto no visual) | `DRE_LV1[ID]`, `DRE_LV2[ID]`, `DRE_LV2[DESC]` |
| Valor xSenha | .Check Senha | `xSenha[xSenha]` |
| .Check Senha | (nenhuma, consumida direto no visual) | `Senhas/Gerente[Usuario]`, `Senhas/Gerente[Senha]` |
