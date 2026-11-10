# Dependências entre medidas

## Árvore por medida final

Medidas que ninguém usa (o que o relatório consome) e tudo de que dependem.

```
.Check Senha
└─ Valor xSenha
```

```
00. DRE A.H ( % )
├─ 00. DRE
│  ├─ 02. Bonificacao
│  │  └─ 01. Total CPV Bonificacao
│  ├─ 02. Comissoes ( R$ )
│  │  └─ 01. Total Comissao ( R$ )
│  ├─ 02. Credito CPV - Devolucao Pro ACA
│  │  └─ 01. Total CPV ItemVenda ( R$ )
│  ├─ 02. Devolucao e Cancelamentos ( R$ )
│  │  └─ 01. Total Valor Bruto ( R$ )
│  ├─ 02. Fretes Tereceiros ( R$ )
│  │  └─ 01. Total Fretes ( R$ )
│  ├─ 02. Gastos Gerais Fabricacao-GGF FIXO ( R$ )
│  │  └─ 01. Total CPV Fixo ( R$ )
│  ├─ 02. Imposto de Vendas ( R$ )
│  │  └─ 01. Total Valor Impostos ( R$ )
│  ├─ 02. Impostos Devolucoes ( R$ )
│  │  └─ 01. Total Valor Impostos ( R$ )
│  ├─ 02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ )
│  │  └─ 01. Total CPV ItemVenda ( R$ )
│  ├─ 02. PV Bruto / Pacotes ( R$ )
│  │  ├─ 02. Receita Operacional Bruta ( R$ )
│  │  │  └─ 01. Total Valor Bruto ( R$ )
│  │  └─ 02. Volume Total ( PCTs )
│  │     └─ 01. Total Volume ( PCTs )
│  ├─ 02. Receita Operacional Bruta ( R$ )
│  │  └─ 01. Total Valor Bruto ( R$ )
│  ├─ 02. Verbas Fixas ( R$ )
│  │  └─ 01. Total Verbas Fixas ( R$ )
│  ├─ 02. Verbas Variaveis ( R$ )
│  │  └─ 01. Total Verbas Variaveis
│  └─ 02. Volume Total ( PCTs )
│     └─ 01. Total Volume ( PCTs )
└─ 02. Receita Operacional Bruta ( R$ )
   └─ 01. Total Valor Bruto ( R$ )
```

```
02. Volume Bonificado/Verba ( PCTs )
└─ 01. Total Volume ( PCTs )
```

```
03. 03 - Margem Bruta ( % )
├─ 03. 01 - Receita Bruta ( R$ )
│  └─ 02. Receita Operacional Bruta ( R$ )
│     └─ 01. Total Valor Bruto ( R$ )
└─ 03. 04 - Margem Bruta ( R$ )
   ├─ 01. Total CPV Fixo ( R$ )
   ├─ 01. Total CPV ItemVenda ( R$ )
   ├─ 01. Total Valor Impostos ( R$ )
   ├─ 03. 01 - Receita Bruta ( R$ )
   │  └─ 02. Receita Operacional Bruta ( R$ )
   │     └─ 01. Total Valor Bruto ( R$ )
   └─ 03. 02 - Devolucao
      └─ 02. Devolucao e Cancelamentos ( R$ )
         └─ 01. Total Valor Bruto ( R$ )
```

```
03. 06 - Bonificacoes ( R$ )
└─ 01. Total CPV Bonificacao
```

```
03. 09 - Margem Contribuicao ( % )
├─ 02. Receita Operacional Bruta ( R$ )
│  └─ 01. Total Valor Bruto ( R$ )
└─ 03. 08 - Margem Contribuicao ( R$ )
   ├─ 03. 04 - Margem Bruta ( R$ )
   │  ├─ 01. Total CPV Fixo ( R$ )
   │  ├─ 01. Total CPV ItemVenda ( R$ )
   │  ├─ 01. Total Valor Impostos ( R$ )
   │  ├─ 03. 01 - Receita Bruta ( R$ )
   │  │  └─ 02. Receita Operacional Bruta ( R$ )
   │  │     └─ 01. Total Valor Bruto ( R$ )
   │  └─ 03. 02 - Devolucao
   │     └─ 02. Devolucao e Cancelamentos ( R$ )
   │        └─ 01. Total Valor Bruto ( R$ )
   ├─ 03. 05 - Despesas Variaveis ( R$ )
   │  ├─ 01. Total CPV Bonificacao
   │  ├─ 01. Total Comissao ( R$ )
   │  └─ 01. Total Fretes ( R$ )
   └─ 03. 07 - Verbas ( R$ )
      ├─ 01. Total Verbas Fixas ( R$ )
      └─ 01. Total Verbas Variaveis
```

```
HTML_Filtros_Ativos

```

## Quem usa quem

| Medida | É usada por | Colunas do fato/dimensões que lê |
|---|---|---|
| .Check Senha | (nenhuma, consumida direto no visual) | Senhas/Gerente[Senha], Senhas/Gerente[Usuario] |
| 00. DRE | 00. DRE A.H ( % ) | DRE_LV1[ID], DRE_LV1[SUB], DRE_LV2[DESC], DRE_LV2[ID] |
| 00. DRE A.H ( % ) | (nenhuma, consumida direto no visual) |  |
| 01. Total CPV Bonificacao | 02. Bonificacao, 03. 05 - Despesas Variaveis ( R$ ), 03. 06 - Bonificacoes ( R$ ) | TB_FatosDRE[VALOR_CPV_BONIF] |
| 01. Total CPV Fixo ( R$ ) | 02. Gastos Gerais Fabricacao-GGF FIXO ( R$ ), 03. 04 - Margem Bruta ( R$ ) | TB_FatosDRE[CPV_PROC_FIXO] |
| 01. Total CPV ItemVenda ( R$ ) | 02. Credito CPV - Devolucao Pro ACA, 02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ ), 03. 04 - Margem Bruta ( R$ ) | TB_FatosDRE[CPV_ITEM_VENDA] |
| 01. Total Comissao ( R$ ) | 02. Comissoes ( R$ ), 03. 05 - Despesas Variaveis ( R$ ) | TB_FatosDRE[VALOR_COMISSAO] |
| 01. Total Fretes ( R$ ) | 02. Fretes Tereceiros ( R$ ), 03. 05 - Despesas Variaveis ( R$ ) | TB_FatosDRE[FRETE] |
| 01. Total Valor Bruto ( R$ ) | 02. Devolucao e Cancelamentos ( R$ ), 02. Receita Operacional Bruta ( R$ ) | TB_FatosDRE[VALOR_VENDA] |
| 01. Total Valor Impostos ( R$ ) | 02. Imposto de Vendas ( R$ ), 02. Impostos Devolucoes ( R$ ), 03. 04 - Margem Bruta ( R$ ) | TB_FatosDRE[TOTAL_IMPOSTOS] |
| 01. Total Verbas Fixas ( R$ ) | 02. Verbas Fixas ( R$ ), 03. 07 - Verbas ( R$ ) | TB_FatosDRE[VERBA_FIXA] |
| 01. Total Verbas Variaveis | 02. Verbas Variaveis ( R$ ), 03. 07 - Verbas ( R$ ) | TB_FatosDRE[VERBA_VARIAVEL] |
| 01. Total Volume ( PCTs ) | 02. Volume Bonificado/Verba ( PCTs ), 02. Volume Total ( PCTs ) | TB_FatosDRE[QTD_ITEM] |
| 02. Bonificacao | 00. DRE | TB_FatosDRE[CFOP], TB_FatosDRE[OPERACAO] |
| 02. Comissoes ( R$ ) | 00. DRE | TB_FatosDRE[OPERACAO] |
| 02. Credito CPV - Devolucao Pro ACA | 00. DRE | TB_FatosDRE[OPERACAO] |
| 02. Devolucao e Cancelamentos ( R$ ) | 00. DRE, 03. 02 - Devolucao | TB_FatosDRE[OPERACAO] |
| 02. Fretes Tereceiros ( R$ ) | 00. DRE | TB_FatosDRE[CFOP], TB_FatosDRE[OPERACAO] |
| 02. Gastos Gerais Fabricacao-GGF FIXO ( R$ ) | 00. DRE | TB_FatosDRE[OPERACAO] |
| 02. Imposto de Vendas ( R$ ) | 00. DRE | TB_FatosDRE[OPERACAO] |
| 02. Impostos Devolucoes ( R$ ) | 00. DRE | TB_FatosDRE[OPERACAO] |
| 02. Materia Prima + Embalagens ( CPV Variaveis ) ( R$ ) | 00. DRE | TB_FatosDRE[OPERACAO] |
| 02. PV Bruto / Pacotes ( R$ ) | 00. DRE |  |
| 02. Receita Operacional Bruta ( R$ ) | 00. DRE, 00. DRE A.H ( % ), 02. PV Bruto / Pacotes ( R$ ), 03. 01 - Receita Bruta ( R$ ), 03. 09 - Margem Contribuicao ( % ) | TB_FatosDRE[CFOP], TB_FatosDRE[OPERACAO] |
| 02. Verbas Fixas ( R$ ) | 00. DRE | TB_FatosDRE[OPERACAO] |
| 02. Verbas Variaveis ( R$ ) | 00. DRE | TB_FatosDRE[OPERACAO] |
| 02. Volume Bonificado/Verba ( PCTs ) | (nenhuma, consumida direto no visual) | TB_FatosDRE[CFOP], TB_FatosDRE[OPERACAO] |
| 02. Volume Total ( PCTs ) | 00. DRE, 02. PV Bruto / Pacotes ( R$ ) | TB_FatosDRE[CFOP], TB_FatosDRE[OPERACAO] |
| 03. 01 - Receita Bruta ( R$ ) | 03. 03 - Margem Bruta ( % ), 03. 04 - Margem Bruta ( R$ ) |  |
| 03. 02 - Devolucao | 03. 04 - Margem Bruta ( R$ ) |  |
| 03. 03 - Margem Bruta ( % ) | (nenhuma, consumida direto no visual) |  |
| 03. 04 - Margem Bruta ( R$ ) | 03. 03 - Margem Bruta ( % ), 03. 08 - Margem Contribuicao ( R$ ) | TB_FatosDRE[OPERACAO] |
| 03. 05 - Despesas Variaveis ( R$ ) | 03. 08 - Margem Contribuicao ( R$ ) | TB_FatosDRE[OPERACAO] |
| 03. 06 - Bonificacoes ( R$ ) | (nenhuma, consumida direto no visual) | TB_FatosDRE[OPERACAO] |
| 03. 07 - Verbas ( R$ ) | 03. 08 - Margem Contribuicao ( R$ ) | TB_FatosDRE[OPERACAO] |
| 03. 08 - Margem Contribuicao ( R$ ) | 03. 09 - Margem Contribuicao ( % ) |  |
| 03. 09 - Margem Contribuicao ( % ) | (nenhuma, consumida direto no visual) |  |
| HTML_Filtros_Ativos | (nenhuma, consumida direto no visual) | CALENDARIO DAX[Ano-Mes], CALENDARIO DAX[Ano], Senhas/Gerente[Usuario], TB_CLIENTE[XNOMCLI], TB_REDE[XNOMGRE], TB_REP[NOMREP], UF/REGIÃO[Região/Grupo], UF/REGIÃO[UF] |
| Valor xSenha | .Check Senha | xSenha[xSenha] |
