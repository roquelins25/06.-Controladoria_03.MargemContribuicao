# MRG CONTR ANALISE HORIZONTAL

## Origem dos dados e responsabilidade

A fonte de dados deste Power BI é uma planilha Excel elaborada e mantida pela Controladoria. O autor da planilha tem plena ciência dos dados disponibilizados. Este Power BI apenas reflete o resultado dessa planilha: não cria, corrige nem valida os números de origem. Dúvidas sobre o conteúdo dos dados (valores, regras de cálculo da base, classificações) devem ser tratadas com a Controladoria.

Relatório de Power BI da Controladoria: DRE gerencial em análise horizontal e vertical, com Margem Bruta e Margem de Contribuição por cliente, representante, produto e região.

## Visão rápida

| Item | Detalhe |
|---|---|
| Projeto | `MRG CONTR ANALISE HORIZONTAL.pbip` |
| Fonte principal | Oracle ERP (modo Import) |
| Outras fontes | Dataflow do Power BI Service (UF/REGIÃO, Senhas/Gerente); planilha Excel da Controladoria (`DRE_Geral - Plan`) |
| Tabelas | 14 (1 fato + dimensões, calendário e tabelas de apoio da DRE) |
| Medidas | 39 |
| Relacionamentos | 10 |
| Cultura | pt-BR |

## Documentação

Toda a documentação técnica está na pasta [`_docs`](_docs/).

| Arquivo | O que tem |
|---|---|
| [00-overview.md](_docs/00-overview.md) | Visão geral, números, fontes de dados e como o modelo funciona |
| [01-tabelas.md](_docs/01-tabelas.md) | As 14 tabelas, com tipo, origem e colunas |
| [02-medidas.md](_docs/02-medidas.md) | As 39 medidas, com DAX e explicação |
| [03-relacionamentos.md](_docs/03-relacionamentos.md) | Relacionamentos e diagrama do modelo |
| [04-dependencias.md](_docs/04-dependencias.md) | Árvore de dependências entre medidas |
| [05-selects.md](_docs/05-selects.md) | SELECT de cada tabela e SQLs fora do modelo |

## Estrutura do projeto

```
06-Controladoria_03.Margem-Contribuicao/
├── MRG CONTR ANALISE HORIZONTAL.pbip
├── MRG CONTR ANALISE HORIZONTAL.SemanticModel/
├── MRG CONTR ANALISE HORIZONTAL.Report/
├── dados/
└── _docs/
```

## Pontos de atenção

- **Período dos dados:** o SQL da `TB_FatosDRE` no modelo busca 01/01/2026 a 31/12/2026. O arquivo `TB_DRE.SQL` na pasta `00.SQLs\TB_FATOS` está com setembro/2026.
- **Período da planilha Excel:** a aba `DRE_Geral - Jan26 a Set26` cobre janeiro a setembro de 2026. A `TB_FatosDRE` (Oracle) cobre o ano inteiro, então os dois não são comparáveis período a período sem filtro.
- **Manutenção da planilha Excel:** a `DRE_Geral - Plan` lê o arquivo `DRE CLIENTE_10112026.xlsx` em `\\dados\Financeiro\Controladoria\BD_BI\DRE CLIENTE CONSOLIDADO`. Quando a Controladoria gerar uma nova versão, troque o nome do arquivo no Power Query dessa tabela (Transformar dados > Editor Avançado).
- **Acesso:** o relatório usa usuário e senha (`Senhas/Gerente`, `xSenha` e a medida `.Check Senha`).
- **DRE horizontal:** a medida `00. DRE` calcula cada linha pelo ID de `DRE_LV2`. Ao incluir uma linha nova na DRE, ajuste o `SWITCH` da medida.
- **SQLs de origem:** ficam em `G:\Meu Drive\BalyEnergy\00.SQLs` (`TB_DIMENSAO` e `TB_FATOS`). `TB_TNS` e `TB_PEDIDOS` existem lá, mas não estão no modelo.

## Como atualizar a documentação

1. Abra o projeto no Power BI Desktop.
2. Peça ao Claude para rodar `/pbi-doc` com o modelo conectado.
3. Substitua o conteúdo da pasta `_docs`.
