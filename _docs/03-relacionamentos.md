# Relacionamentos

O modelo tem **14 relacionamentos** de usuário. Ficam de fora 3 relacionamentos de data automática do Power BI.

| # | De (muitos) | Para (um) | Cardinalidade | Direção | Ativo |
|---|---|---|---|---|---|
| 1 | TB_FatosDRE[USU_CODMAR] | TB_MOTIVO[USU_CODMAR] | Muitos para um | Única | Sim |
| 2 | TB_FatosDRE[DATA_NF] | CALENDARIO DAX[Date] | Muitos para um | Única | Sim |
| 3 | TB_FatosDRE[CODPRODUTO] | TB_PRODUTO[CODPRO] | Muitos para um | Única | Sim |
| 4 | TB_FatosDRE[CODIGO_REPRES_NF] | TB_REP[CODREP] | Muitos para um | Única | Sim |
| 5 | TB_FatosDRE[COD_CLIENTE] | TB_CLIENTE[CODCLI] | Muitos para um | Única | Sim |
| 6 | TB_PRODUTO[USU_CODFAMDAY] | TB_FAMRMC[CODRMC] | Muitos para um | Única | Sim |
| 7 | TB_CLIENTE[CODGRE] | TB_REDE[CODGRE] | Muitos para um | Única | Sim |
| 8 | TB_CLIENTE[XREGSENHA] | UF/REGIÃO[UF] | Muitos para um | Única | Sim |
| 9 | UF/REGIÃO[ID] | Senhas/Gerente[ID] | Muitos para muitos | Única | Sim |
| 10 | DRE_LV2[ID_LV1] | DRE_LV1[ID] | Muitos para um | Única | Sim |
| 11 | DRE_Geral - Plan[Cod_Cliente] | TB_CLIENTE[CODCLI] | Muitos para um | Única | Sim |
| 12 | DRE_Geral - Plan[CodProduto] | TB_PRODUTO[CODPRO] | Muitos para um | Única | Sim |
| 13 | DRE_Geral - Plan[Codigo_repres_NF] | TB_REP[CODREP] | Muitos para um | Única | Sim |
| 14 | DRE_Geral - Plan[Mes_Ano] | CALENDARIO DAX[Date] | Muitos para um | Única | Sim |

## Diagrama

```
                          CALENDARIO DAX ------------ DRE_Geral - Plan (Mes_Ano)
                               |
                            DATA_NF
 TB_MOTIVO ---USU_CODMAR--- TB_FatosDRE ---CODPRODUTO--- TB_PRODUTO ---USU_CODFAMDAY--- TB_FAMRMC
                            |      |
                 COD_CLIENTE      CODIGO_REPRES_NF
                       |                  |
                  TB_CLIENTE           TB_REP
                   |      |
              CODGRE      XREGSENHA
                 |            |
             TB_REDE      UF/REGIÃO ---ID (N:N)--- Senhas/Gerente

 DRE_Geral - Plan ---Cod_Cliente---> TB_CLIENTE   (mesma dimensão do fato Oracle)
 DRE_Geral - Plan ---CodProduto----> TB_PRODUTO
 DRE_Geral - Plan ---Codigo_repres_NF---> TB_REP

 DRE_LV2 ---ID_LV1--- DRE_LV1      (sem ligação com os fatos; usadas pelas medidas de DRE)
 xSenha                            (sem relacionamento; usada por .Check Senha)
```

## Observações

- Todos os relacionamentos filtram em uma única direção (dimensão para fato), exceto onde a tabela indica.
- `TB_CLIENTE`, `TB_PRODUTO` e `TB_REP` são compartilhados pelos dois fatos (`TB_FatosDRE` e `DRE_Geral - Plan`).
- `UF/REGIÃO` ↔ `Senhas/Gerente` é muitos-para-muitos pela coluna `ID`.
- `DRE_LV1`/`DRE_LV2` ficam fora dos fatos de propósito: as medidas de DRE calculam cada linha pelo ID, sem depender de filtro.
- `DRE_Geral - Plan` e `TB_FatosDRE` não têm relacionamento entre si. Por isso, uma medida que filtra um fato com a coluna do outro não tem efeito (ver ponto de atenção 2 em `02-medidas.md`).
