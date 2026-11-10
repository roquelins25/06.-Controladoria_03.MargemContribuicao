# SELECTs das tabelas

Fonte da verdade: o SQL embutido no modelo (o que o Power BI executa). Conexão: Oracle ERP, modo Import, `CommandTimeout` de 10 minutos.

| Tabela | Arquivo equivalente | Situação |
|---|---|---|
| TB_FatosDRE | TB_FATOS\TB_DRE.SQL | Só muda o período: modelo 01/01/2026 a 31/12/2026, arquivo 01/09/2026 a 30/09/2026 |
| TB_CLIENTE | TB_DIMENSAO\TB_CLIENTE.SQL | Idêntico |
| TB_FAMRMC | TB_DIMENSAO\TB_FAMRMC.SQL | Idêntico |
| TB_MOTIVO | TB_DIMENSAO\TB_MOTIVO.SQL | Idêntico |
| TB_PRODUTO | TB_DIMENSAO\TB_PRODUTO.SQL | Idêntico |
| TB_REDE | TB_DIMENSAO\TB_REDE.SQL | Idêntico |
| TB_REP | TB_DIMENSAO\TB_REP.SQL | Idêntico |
| DRE_Geral - Plan | Sem SQL: lê a planilha Excel por Power Query (M) | Fonte é o arquivo `DRE CLIENTE_10112026.xlsx` (ver seção abaixo) |

## TB_FatosDRE

```sql
/* =====================================================================
   DRE GERENCIAL - versão CTE (cópia refatorada de DRE.SQL)

   Estrutura:
     params              -> período (altere SÓ aqui)
     dim_produto         -> produto + Classificacao_BI (regra única)
     dim_cliente         -> cliente x filial + gerente regional (regra única)
     cpv / frete_uf      -> tabelas de custo e frete do mês
     vendas_cli          -> total vendido por cliente (denominador do rateio)
     mov_verba           -> movimentos de verba (contas a pagar 9051V/9051B)
     verba_fixa_cli      -> verba fixa por cliente (contas a receber 90364)
     verba_var_cli       -> verba variável por cliente
     clientes_com_venda  -> clientes com venda no mês (anti-join do bloco Verbas)
     saidas / verbas / entradas -> os 3 blocos do UNION ALL
     base                -> UNION ALL numérico (use SELECT * FROM base para somar)
     SELECT final        -> formatação em texto (igual ao DRE.SQL original)

   Mudanças em relação ao original:
     1. Subqueries correlacionadas de rateio (Verba_Fixa / Verba_Variavel)
        viraram CTEs agregadas por cliente (1 leitura em vez de 1 por item).
     2. Datas parametrizadas; fim do período tratado como < dt_fim + 1.
     3. CASE de Gerente_Regional e de Classificacao_BI escritos uma única vez.
        ATENÇÃO: no bloco Entradas original, volume 1 de energético caía em
        'ENERGÉTICO 250ML' e o código 10012 em 'OUTROS NÃO ALCOOLICOS'.
        Aqui vale a regra do bloco Saídas ('ENERGÉTICO 1L' / 'SAFADAO').
     4. GROUP BY do bloco Verbas corrigido (mcp.datmov).
     5. ORDER BY usa a data real (antes ordenava pelo texto DD/MM/YYYY).
     6. Período com vários meses: CPV, frete e rateio de verbas são feitos
        POR MÊS (chave TRUNC(data,'MM')). Assume tabelas e081itp/e081fce
        com datini = 1º dia do mês.
   ===================================================================== */
WITH
params AS (
    SELECT TO_DATE('20260101','YYYYMMDD') AS dt_ini,
           TO_DATE('20261231','YYYYMMDD') AS dt_fim
    FROM dual
),

/* ---------- DIMENSÕES ---------- */
dim_produto AS (
    SELECT pro.codpro,
           CASE WHEN pro.cplpro IS NULL THEN pro.desnfv ELSE pro.cplpro END AS produto,
           pro.volpro                AS volume_embalagem,
           UPPER(pro.usu_claman)     AS classificacao,
           CASE
               WHEN (pro.codfam = '17' AND pro.volpro = 2)     THEN 'ENERGÉTICO 2L'
               WHEN (pro.codfam = '17' AND pro.volpro = 0.473) THEN 'ENERGÉTICO 473ML'
               WHEN (pro.codfam = '17' AND pro.volpro = 0.250) THEN 'ENERGÉTICO 250ML'
               WHEN (pro.codfam = '17' AND pro.volpro = 1)     THEN 'ENERGÉTICO 1L'
               WHEN (pro.codfam = '17' AND pro.volpro = 0.750) THEN 'ENERGÉTICO 750ML'
               WHEN (pro.codfam = '17' AND pro.volpro = 2.5)   THEN 'ENERGÉTICO 2.5L'
               WHEN pro.usu_codfamday = '10002' THEN 'VODKA'
               WHEN pro.usu_codfamday = '10003' THEN 'CERVEJA'
               WHEN pro.usu_codfamday = '10004' THEN 'GIN'
               WHEN pro.usu_codfamday = '10005' THEN 'WHISKY'
               WHEN pro.usu_codfamday = '10006' THEN 'KIDS'
               WHEN pro.usu_codfamday = '10008' THEN 'OUTROS ALCOOLICOS'
               WHEN pro.usu_codfamday = '10009' THEN 'OUTROS NÃO ALCOOLICOS'
               WHEN pro.usu_codfamday = '10001' THEN 'OUTROS ALCOOLICOS'
               WHEN pro.usu_codfamday = '10011' THEN 'OUTROS ALCOOLICOS'
               WHEN pro.usu_codfamday = '10012' THEN 'SAFADAO'
               WHEN pro.usu_codfamday = '10013' THEN 'OUTROS ALCOOLICOS'
               WHEN pro.usu_codfamday = '10014' THEN 'OUTROS NÃO ALCOOLICOS'
               WHEN pro.usu_codfamday = '10015' THEN 'ISOTONICOS'
               ELSE 'OUTROS'
           END AS classificacao_bi
    FROM e075pro pro
),

dim_cliente AS (
    SELECT cli.codcli,
           hcl.codfil,
           hcl.codrve,
           cli.nomcli,
           cli.sigufs                AS uf,
           cli.codgre,
           NVL(gre.nomgre,'')        AS grupo_empresa,
           UPPER(can.usu_desram)     AS segmento,
           NVL(re2.nomrep,'')        AS key_account,
           NVL(re3.nomrep,'')        AS supervisor,
           cat.regiao                AS regiao,
           cat.codrep                AS codrep_cat,
           cat.nomrep                AS representante,
           cat.nomger                AS gerente_nacional,
           CASE
               WHEN (cli.codgre = 24 AND cli.sigufs = 'SP') THEN 'JEORGE'
               WHEN cli.sigufs IN ('AC','AP','AM','PA','RO','RR','CE','MA','PI') THEN 'DIEGO'
               WHEN cli.sigufs IN ('BA','SE') THEN 'CLAUDER'
               WHEN cli.sigufs IN ('AL','PB','PE','RN') THEN 'URIEL'
               WHEN cli.sigufs IN ('DF','GO','MT','MS','TO') THEN 'MELEU'
               WHEN cli.sigufs IN ('ES','MG') THEN 'LIXA'
               WHEN cli.sigufs = 'RJ' THEN 'VICTER'
               WHEN cli.sigufs = 'SC' THEN 'RAFAEL'
               WHEN cli.sigufs = 'PR' THEN 'VANDER'
               WHEN cli.sigufs = 'RS' THEN 'VITOR'
               WHEN (hcl.codrve = '70' AND cli.sigufs = 'SP') THEN 'PAMELA'
               WHEN (hcl.codrve = '71' AND cli.sigufs = 'SP') THEN 'ADRIANO'
               WHEN (hcl.codrve NOT IN ('70','71')
                     AND cli.sigufs = 'SP'
                     AND cli.codgre <> 24) THEN 'SP_NAO_IDENT'
               WHEN cli.sigufs = 'EX' THEN 'PABLO'
               ELSE 'OUTROS'
           END AS gerente_regional
    FROM e085cli cli
    INNER JOIN e085hcl hcl ON hcl.codcli = cli.codcli
    LEFT JOIN e090rep re2  ON re2.codrep = hcl.usu_codkey
    LEFT JOIN e090rep re3  ON re3.codrep = hcl.usu_codsup
    LEFT JOIN usu_tcanais can ON can.usu_codram = cli.usu_codram
    LEFT JOIN e069gre gre  ON gre.codgre = cli.codgre
    LEFT JOIN usu_view_categoria_representante cat ON cat.codcli = cli.codcli
),

/* ---------- TABELAS DE CUSTO / FRETE DO MÊS ---------- */
cpv AS (
    SELECT itp.codpro, itp.datini, itp.prebas
    FROM e081itp itp
    CROSS JOIN params p
    WHERE itp.codtpr = 'CPV'
      AND itp.datini >= TRUNC(p.dt_ini,'MM')
      AND itp.datini <= p.dt_fim
),

frete_uf AS (
    SELECT fce.sigufs, fce.datini, fce.perfre
    FROM e081fce fce
    CROSS JOIN params p
    WHERE fce.codtab = 'FRET'
      AND fce.datini >= TRUNC(p.dt_ini,'MM')
      AND fce.datini <= p.dt_fim
),

/* ---------- BASES DO RATEIO DE VERBAS (1 leitura por tabela) ---------- */
-- Denominador: total vendido por cliente no mês (mesmos filtros do bloco Saídas)
vendas_cli AS (
    SELECT nfv2.codcli,
           TRUNC(nfv2.datemi,'MM') AS mes,
           SUM(ipv2.vlrfin) AS total_vendas
    FROM e140nfv nfv2
    INNER JOIN e140ipv ipv2
        ON nfv2.codemp = ipv2.codemp
       AND nfv2.codfil = ipv2.codfil
       AND nfv2.codsnf = ipv2.codsnf
       AND nfv2.numnfv = ipv2.numnfv
    CROSS JOIN params p
    WHERE nfv2.datemi >= p.dt_ini
      AND nfv2.datemi <  p.dt_fim + 1
      AND nfv2.sitnfv = 2
      AND ipv2.qtdfat > 0
      AND ipv2.tnspro NOT IN ('6907','5151','6151','5907','6905')
      AND (ipv2.tnspro LIKE '5%' OR ipv2.tnspro LIKE '6%' OR ipv2.tnspro LIKE '7%')
    GROUP BY nfv2.codcli, TRUNC(nfv2.datemi,'MM')
),

-- Movimentos de verba variável (contas a pagar), nível título/movimento
mov_verba AS (
    SELECT mcp.codfil,
           mcp.codtpt,
           mcp.codfor,
           mcp.numtit,
           mcp.datmov,
           mcp.codtns,
           mcp.vlrmov,
           cp.vlrori
    FROM e501mcp mcp
    INNER JOIN e501tcp cp
        ON cp.codemp = mcp.codemp
       AND cp.codfil = mcp.codfil
       AND cp.codtpt = mcp.codtpt
       AND cp.codfor = mcp.codfor
       AND cp.numtit = mcp.numtit
    CROSS JOIN params p
    WHERE mcp.codtns IN ('9051V','9051B')
      AND cp.sittit <> 'CA'
      AND mcp.datmov >= p.dt_ini
      AND mcp.datmov <  p.dt_fim + 1
),

verba_var_cli AS (
    SELECT codfor AS codcli,
           TRUNC(datmov,'MM') AS mes,
           SUM(vlrmov) AS vlr_verba_var
    FROM mov_verba
    GROUP BY codfor, TRUNC(datmov,'MM')
),

-- Verba fixa (contas a receber, transação 90364)
verba_fixa_cli AS (
    SELECT tcr.codcli,
           TRUNC(mcr.datmov,'MM') AS mes,
           SUM(mcr.vlrmov) AS vlr_verba_fixa
    FROM e301tcr tcr
    INNER JOIN e301mcr mcr
        ON mcr.codemp = tcr.codemp
       AND mcr.codfil = tcr.codfil
       AND mcr.codtpt = tcr.codtpt
       AND mcr.numtit = tcr.numtit
    CROSS JOIN params p
    WHERE mcr.codtns = '90364'
      AND mcr.datmov >= p.dt_ini
      AND mcr.datmov <  p.dt_fim + 1
    GROUP BY tcr.codcli, TRUNC(mcr.datmov,'MM')
),

-- Clientes com venda no mês (chave cliente + mês): quem NÃO está aqui recebe a verba no bloco Verbas
clientes_com_venda AS (
    SELECT DISTINCT nfv.codcli, TRUNC(nfv.datemi,'MM') AS mes
    FROM e140nfv nfv
    CROSS JOIN params p
    WHERE nfv.datemi >= p.dt_ini
      AND nfv.datemi <  p.dt_fim + 1
      AND nfv.vlrfin > 0
      AND nfv.tipnfs IN (1,10)
      AND nfv.sitnfv = 2
),

/* =====================================================================
   BLOCO 0 - taxas_ratio de CPV fixos
   esse calculo fixo é enviado pela controladoria e rateado entre o valor.
   Regra especifica ainda nao repassada.
   % emitido foi calculado com base no valor de CPV do mes de referencia pela Planilha de DRE Oficial.
   ===================================================================== */
taxas_rateio AS (
    SELECT TO_DATE('20260101','YYYYMMDD') mes, 'Saidas' op, 0.050161 cpv, 0.140649 desp, 0.120936 cred FROM dual UNION ALL
    SELECT TO_DATE('20260201','YYYYMMDD'), 'Saidas', 0.047767, 0.103806, 0.126601 FROM dual UNION ALL
    SELECT TO_DATE('20260301','YYYYMMDD'), 'Saidas', 0.053243, 0.126719, 0.150237 FROM dual UNION ALL
    SELECT TO_DATE('20260401','YYYYMMDD'), 'Saidas', 0.053094, 0.162000, 0.153479 FROM dual UNION ALL
    SELECT TO_DATE('20260501','YYYYMMDD'), 'Saidas', 0.051199, 0.127485, 0.126017 FROM dual UNION ALL
    SELECT TO_DATE('20260601','YYYYMMDD'), 'Saidas', 0.057910, 0.165580, 0.128637 FROM dual UNION ALL
    SELECT TO_DATE('20260701','YYYYMMDD'), 'Saidas', 0.052808, 0.146727, 0.101621 FROM dual UNION ALL
    SELECT TO_DATE('20260801','YYYYMMDD'), 'Saidas', 0.053311, 0.156930, 0.105914 FROM dual UNION ALL
    SELECT TO_DATE('20260901','YYYYMMDD'), 'Saidas', 0.048199, 0.114399, 0.119493 FROM dual UNION ALL

    SELECT TO_DATE('20260101','YYYYMMDD'), 'Entradas', 0.046243, 0.154128, 0.185632 FROM dual UNION ALL
    SELECT TO_DATE('20260201','YYYYMMDD'), 'Entradas', 0.044322, 0.114495, 0.195593 FROM dual UNION ALL
    SELECT TO_DATE('20260301','YYYYMMDD'), 'Entradas', 0.049404, 0.139767, 0.232109 FROM dual UNION ALL
    SELECT TO_DATE('20260401','YYYYMMDD'), 'Entradas', 0.053094, 0.162000, 0.153479 FROM dual UNION ALL
    SELECT TO_DATE('20260501','YYYYMMDD'), 'Entradas', 0.051199, 0.127485, 0.126017 FROM dual UNION ALL
    SELECT TO_DATE('20260601','YYYYMMDD'), 'Entradas', 0.057910, 0.165580, 0.128637 FROM dual UNION ALL
    SELECT TO_DATE('20260701','YYYYMMDD'), 'Entradas', 0.049000, 0.161836, 0.157000 FROM dual UNION ALL
    SELECT TO_DATE('20260801','YYYYMMDD'), 'Entradas', 0.049467, 0.173089, 0.163632 FROM dual UNION ALL
    SELECT TO_DATE('20260901','YYYYMMDD'), 'Entradas', 0.044723, 0.126179, 0.184612 FROM dual
),

/* =====================================================================
   BLOCO 1 - SAÍDAS (vendas e bonificações) + verba rateada por item
   ===================================================================== */
saidas AS (
    SELECT
        'Saidas'                      AS Operacao,
        nfv.codfil                    AS Filial,
        nfv.datemi                    AS Data_NF,
        CASE
            WHEN nfv.tipnfs = 1  THEN 'Saida_Individual'
            WHEN nfv.tipnfs = 10 THEN 'Saida_Carga'
            ELSE 'Outras_Saidas'
        END                           AS Tipo_Nota,
        d.regiao                      AS Regiao,
        d.uf                          AS UF,
        nfv.codrep                    AS Codigo_repres_NF,
        d.representante               AS Representante,
        d.supervisor                  AS Supervisor,
        d.key_account                 AS Key_Account,
        d.gerente_regional            AS Gerente_Regional,
        d.gerente_nacional            AS Gerente_Nacional,
        nfv.numnfv                    AS Nota_Fiscal,
        nfv.codcli                    AS Cod_Cliente,
        d.nomcli                      AS Razao_Social,
        d.codgre                      AS Cod_Grupo,
        d.grupo_empresa               AS Grupo_Empresa,
        ipv.noppro                    AS CFOP,
        tns.destns                    AS Descricao_CFOP,
        ipv.codpro                    AS CodProduto,
        pr.produto                    AS Produto,
        pr.volume_embalagem           AS Volume_Embalagem,
        pr.classificacao              AS Classificacao,
        pr.classificacao_bi           AS Classificacao_BI,
        d.segmento                    AS Segmento,
        ipv.qtdfat                    AS Qtd_Item,
        CASE WHEN ipv.vlrfin > 0
             THEN ipv.qtdfat * NVL(cpv.prebas,0)
             ELSE 0 END               AS CPV_Item_Venda,
        ipv.vlrfin                    AS Valor_Venda,
        CASE WHEN nfv.codcpg = 'BO'
             THEN ipv.qtdfat * NVL(cpv.prebas,0)
             ELSE 0 END               AS Valor_CPV_Bonif,
        ipv.vlrdsc + ipv.vlrdzf       AS Valor_Desconto,
        ipv.vlrout                    AS Valor_Outras,
        ipv.vlripi + ipv.vlripd       AS Valor_IPI,
        ipv.vlricm                    AS Valor_ICMS,
        pvd.vlrfcp                    AS Valor_FCP,
        ipv.vlrics + pvd.vstfcp       AS Valor_ST,
        ipv.vlrpif                    AS Valor_PIS_Venda,
        ipv.vlrcff                    AS valor_COFINS,
        ipv.vlripi + ipv.vlripd + ipv.vlricm + pvd.vlrfcp
          + ipv.vlrics + pvd.vstfcp + ipv.vlrpif + ipv.vlrcff AS Total_Impostos,
        CASE WHEN ipv.vlrfin > 0 THEN ipv.vlrcom ELSE 0 END   AS Valor_Comissao,
        -- ------ OLHAR ESSE TRECHO: percentuais fixos (rateio) ------
        ipv.vlrfin * tx.cpv  AS CPV_Proc_Fixo,
        ipv.vlrfin * tx.desp AS DESP_Oper_Fixa,
        ipv.vlrfin * tx.cred AS CRED_Impostos,
        -- -----------------------------------------------------------
        -- Verba = (item / total vendido do cliente) x verba do cliente no mês
        CASE WHEN ipv.vlrfin > 0
             THEN NVL(ipv.vlrfin / NULLIF(vc.total_vendas,0)
                      * NVL(vf.vlr_verba_fixa,0), 0)
             ELSE 0 END               AS Verba_Fixa,
        CASE WHEN ipv.vlrfin > 0
             THEN NVL(ipv.vlrfin / NULLIF(vc.total_vendas,0)
                      * vv.vlr_verba_var, 0)
             ELSE 0 END               AS Verba_Variavel,
        CASE WHEN ipv.vlrliq > 0
             THEN NVL(ipv.vlrliq * fu.perfre / 100, 0)
             ELSE 0 END               AS Frete,
        CASE WHEN nfv.plavei IN ('AAA-1234','RET-0000') THEN 'FOB' ELSE 'CIF' END AS Placa,
        ''                            AS Motivo_Devolucao,
        ''                                 AS usu_codmar
    FROM e140nfv nfv
    CROSS JOIN params p
    INNER JOIN e140ipv ipv
        ON nfv.codemp = ipv.codemp
       AND nfv.codfil = ipv.codfil
       AND nfv.codsnf = ipv.codsnf
       AND nfv.numnfv = ipv.numnfv
    INNER JOIN e140pvd pvd
        ON pvd.codemp = ipv.codemp
       AND pvd.codfil = ipv.codfil
       AND pvd.codsnf = ipv.codsnf
       AND pvd.numnfv = ipv.numnfv
       AND pvd.seqipv = ipv.seqipv
    INNER JOIN dim_produto pr ON pr.codpro = ipv.codpro
    INNER JOIN e001tns tns    ON tns.codtns = ipv.tnspro
    INNER JOIN e090rep rep    ON rep.codrep = nfv.codrep
    INNER JOIN dim_cliente d  ON d.codcli = nfv.codcli AND d.codfil = nfv.codfil
    LEFT JOIN cpv             ON cpv.codpro = ipv.codpro
                             AND cpv.datini = TRUNC(nfv.datemi,'MM')
    LEFT JOIN frete_uf fu     ON fu.sigufs = d.uf
                             AND fu.datini = TRUNC(nfv.datemi,'MM')
    LEFT JOIN vendas_cli vc       ON vc.codcli = nfv.codcli
                                 AND vc.mes    = TRUNC(nfv.datemi,'MM')
    LEFT JOIN verba_fixa_cli vf   ON vf.codcli = nfv.codcli
                                 AND vf.mes    = TRUNC(nfv.datemi,'MM')
    LEFT JOIN verba_var_cli vv    ON vv.codcli = nfv.codcli
                                 AND vv.mes    = TRUNC(nfv.datemi,'MM')

    LEFT JOIN taxas_rateio tx ON tx.op  = 'Saidas'
                             AND tx.mes = TRUNC(nfv.datemi,'MM')

    WHERE nfv.datemi >= p.dt_ini
      AND nfv.datemi <  p.dt_fim + 1
      AND nfv.sitnfv = 2
      AND ipv.qtdfat > 0
      AND (ipv.tnspro LIKE '5%' OR ipv.tnspro LIKE '6%' OR ipv.tnspro LIKE '7%')
      AND (nfv.vlrfin > 0 OR nfv.codcpg = 'BO')
      AND nfv.codcli NOT IN (11143)
      AND ipv.tnspro NOT IN ('6907','5151','6151','5907','6905')
),

/* =====================================================================
   BLOCO 2 - VERBAS de clientes SEM venda no mês
   ===================================================================== */
verbas AS (
    SELECT
        'Saidas'                      AS Operacao,
        mv.codfil                     AS Filial,
        mv.datmov                     AS Data_NF,
        'Verbas'                      AS Tipo_Nota,
        d.regiao                      AS Regiao,
        d.uf                          AS UF,
        d.codrep_cat                  AS Codigo_repres_NF,
        d.representante               AS Representante,
        d.supervisor                  AS Supervisor,
        d.key_account                 AS Key_Account,
        d.gerente_regional            AS Gerente_Regional,
        d.gerente_nacional            AS Gerente_Nacional,
        0                             AS Nota_Fiscal,
        mv.codfor                     AS Cod_Cliente,
        d.nomcli                      AS Razao_Social,
        d.codgre                      AS Cod_Grupo,
        d.grupo_empresa               AS Grupo_Empresa,
        ''                            AS CFOP,
        mv.codtns                     AS Descricao_CFOP,
        ''                            AS CodProduto,
        ''                            AS Produto,
        0                             AS Volume_Embalagem,
        ''                            AS Classificacao,
        CASE WHEN ver.usu_tipemb IS NULL THEN mv.numtit ELSE ver.usu_tipemb END AS Classificacao_BI,
        d.segmento                    AS Segmento,
        ver.usu_qtdemb                AS Qtd_Item,
        0 AS CPV_Item_Venda,
        0 AS Valor_Venda,
        0 AS Valor_CPV_Bonif,
        0 AS Valor_Desconto,
        0 AS Valor_Outras,
        0 AS Valor_IPI,
        0 AS Valor_ICMS,
        0 AS Valor_FCP,
        0 AS Valor_ST,
        0 AS Valor_PIS_Venda,
        0 AS valor_COFINS,
        0 AS Total_Impostos,
        0 AS Valor_Comissao,
        0 AS CPV_Proc_Fixo,
        0 AS DESP_Oper_Fixa,
        0 AS CRED_Impostos,
        0 AS Verba_Fixa,
        CASE WHEN MAX(ver.usu_vlrtot) IS NULL
             THEN SUM(mv.vlrori)
             ELSE SUM(ver.usu_vlrtot) END AS Verba_Variavel,
        0                             AS Frete,
        'CIF'                         AS Placa,
        ''                            AS Motivo_Devolucao,
        ''                                 AS usu_codmar
    FROM mov_verba mv
    INNER JOIN dim_cliente d
        ON d.codcli = mv.codfor
       AND d.codfil = mv.codfil
    LEFT JOIN usu_tverba ver
        ON ver.usu_codfil = mv.codfil
       AND ver.usu_codtpt = mv.codtpt
       AND ver.usu_numtit = mv.numtit
       AND ver.usu_codcli = mv.codfor
    WHERE NOT EXISTS (SELECT 1 FROM clientes_com_venda cv
                      WHERE cv.codcli = mv.codfor
                        AND cv.mes    = TRUNC(mv.datmov,'MM'))
    GROUP BY
        mv.codfil, mv.datmov, mv.codfor, mv.codtns, mv.numtit,
        d.regiao, d.uf, d.codrep_cat, d.representante, d.supervisor, d.key_account,
        d.gerente_regional, d.gerente_nacional, d.nomcli, d.codgre, d.grupo_empresa,
        d.segmento, d.codrve,
        ver.usu_tipemb, ver.usu_qtdemb
),

/* =====================================================================
   BLOCO 3 - ENTRADAS (devoluções) - valores positivos; subtrair no BI
   ===================================================================== */
entradas AS (
    SELECT
        'Entradas'                    AS Operacao,
        nfc.codfil                    AS Filial,
        nfc.datent                    AS Data_NF,
        CASE
            WHEN nfc.tipnfe = 2 THEN 'NF_Cliente'
            WHEN nfc.tipnfe = 3 THEN 'NF_Grassi'
            ELSE 'Devol_Bonificacao'
        END                           AS Tipo_Nota,
        d.regiao                      AS Regiao,
        d.uf                          AS UF,
        d.codrep_cat                  AS Codigo_repres_NF,
        d.representante               AS Representante,
        d.supervisor                  AS Supervisor,
        d.key_account                 AS Key_Account,
        d.gerente_regional            AS Gerente_Regional,
        d.gerente_nacional            AS Gerente_Nacional,
        nfc.numnfc                    AS Nota_Fiscal,
        nfc.codfor                    AS Cod_Cliente,
        d.nomcli                      AS Razao_Social,
        d.codgre                      AS Cod_Grupo,
        d.grupo_empresa               AS Grupo_Empresa,
        ipc.noppro                    AS CFOP,
        tns.destns                    AS Descricao_CFOP,
        ipc.codpro                    AS CodProduto,
        pr.produto                    AS Produto,
        pr.volume_embalagem           AS Volume_Embalagem,
        pr.classificacao              AS Classificacao,
        pr.classificacao_bi           AS Classificacao_BI,
        d.segmento                    AS Segmento,
        ipc.qtdest                    AS Qtd_Item,
        CASE WHEN ipc.vlrfin > 0
             THEN ipc.qtdest * NVL(cpv.prebas,0)
             ELSE 0 END               AS CPV_Item_Venda,
        ipc.vlrfin                    AS Valor_Venda,
        CASE WHEN nfc.noppro IN ('1949','2949')
             THEN ipc.qtdest * NVL(cpv.prebas,0)
             ELSE 0 END               AS Valor_CPV_Bonif,
        ipc.vlrdsc                    AS Valor_Desconto,
        ipc.vlrout                    AS Valor_Outras,
        ipc.vecipi + ipc.vlripd       AS Valor_IPI,
        ipc.vecicm                    AS Valor_ICMS,
        pvd.vlrfcp                    AS Valor_FCP,
        ipc.vlrics + pvd.vstfcp       AS Valor_ST,
        ipc.vlrpif                    AS Valor_PIS_Venda,
        ipc.vlrcff                    AS valor_COFINS,
        ipc.vecipi + ipc.vlripd + ipc.vecicm + pvd.vlrfcp
          + ipc.vlrics + pvd.vstfcp + ipc.vlrpif + ipc.vlrcff AS Total_Impostos,
        CASE WHEN ipc.numnfv > 0
             THEN (ip2.vlrcom / NULLIF(ip2.qtdfat,0)) * ipc.qtdest
             ELSE 0 END               AS Valor_Comissao,
        ipc.vlrfin * tx.cpv  AS CPV_Proc_Fixo,
        ipc.vlrfin * tx.desp AS DESP_Oper_Fixa,
        ipc.vlrfin * tx.cred AS CRED_Impostos,
        0                             AS Verba_Fixa,
        0                             AS Verba_Variavel,
        CASE WHEN ipc.vlrliq > 0
             THEN NVL(ipc.vlrliq * fu.perfre / 100, 0)
             ELSE 0 END               AS Frete,
        'CIF'                         AS Placa,
        mot.usu_nommar                AS Motivo_Devolucao,
        nfc.usu_codmar                 AS usu_codmar
    FROM e440nfc nfc
    CROSS JOIN params p
    INNER JOIN e440ipc ipc
        ON nfc.codemp = ipc.codemp
       AND nfc.codfil = ipc.codfil
       AND nfc.codsnf = ipc.codsnf
       AND nfc.numnfc = ipc.numnfc
       AND nfc.codfor = ipc.codfor
    INNER JOIN e440pcd pvd
        ON pvd.codemp = ipc.codemp
       AND pvd.codfil = ipc.codfil
       AND pvd.codsnf = ipc.codsnf
       AND pvd.numnfc = ipc.numnfc
       AND pvd.seqipc = ipc.seqipc
       AND pvd.codfor = ipc.codfor
    INNER JOIN dim_produto pr ON pr.codpro = ipc.codpro
    INNER JOIN e001tns tns    ON tns.codtns = ipc.tnspro
    INNER JOIN dim_cliente d  ON d.codcli = nfc.codfor AND d.codfil = nfc.codfil
    LEFT JOIN e140ipv ip2
        ON ip2.codemp = ipc.empnfv
       AND ip2.codfil = ipc.filnfv
       AND ip2.codsnf = ipc.snfnfv
       AND ip2.numnfv = ipc.numnfv
       AND ip2.seqipv = ipc.seqipv
    LEFT JOIN cpv             ON cpv.codpro = ipc.codpro
                             AND cpv.datini = TRUNC(nfc.datent,'MM')
    LEFT JOIN frete_uf fu     ON fu.sigufs = d.uf
                             AND fu.datini = TRUNC(nfc.datent,'MM')
    LEFT JOIN usu_te440mot mot ON mot.usu_codmar = nfc.usu_codmar
    LEFT JOIN taxas_rateio tx ON tx.op  = 'Entradas'
                             AND tx.mes = TRUNC(nfc.datent,'MM')
    WHERE nfc.datent >= p.dt_ini
      AND nfc.datent <  p.dt_fim + 1
      AND nfc.sitnfc = 2
      AND nfc.tipnfe IN (2,3,7)
      AND ipc.qtdest > 0
      AND (ipc.tnspro LIKE '1%' OR ipc.tnspro LIKE '2%' OR ipc.tnspro LIKE '3%')
      AND ipc.noppro NOT IN ('1551','2551','2921','1921')
      AND ipc.codfor NOT IN (11143)
),

/* ---------- UNION ALL numérico (mesma ordem de colunas nos 3 blocos) ---------- */
base AS (
    SELECT * FROM saidas
    UNION ALL
    SELECT * FROM verbas
    UNION ALL
    SELECT * FROM entradas
),

/* =====================================================================
   CONSOLIDADO - valores numéricos (use para somar/analisar)
   ===================================================================== */
tb_consolidado AS (
SELECT
       b.Operacao,
       b.Filial,
       TO_CHAR(b.Data_NF, 'MM/YYYY')    AS Mes_Ano,
       b.Data_NF AS Data_NF,
       b.Tipo_Nota,
       --b.Regiao,
       --b.UF,
       b.Codigo_repres_NF,
       --b.Representante,
       --b.Supervisor,
       --b.Key_Account,
       --b.Gerente_Regional,
       --b.Gerente_Nacional,
       b.Nota_Fiscal,
       b.Cod_Cliente,
       --b.Razao_Social,
       --b.Cod_Grupo,
       --b.Grupo_Empresa,
       b.CFOP,
       b.Descricao_CFOP,
       b.CodProduto,
       --b.Produto,
       --b.Volume_Embalagem,
       --b.Classificacao,
       --b.Classificacao_BI,
       --b.Segmento,
       b.Qtd_Item,
       b.CPV_Item_Venda,
       b.Valor_Venda,
       b.Valor_CPV_Bonif,
       b.Valor_Desconto,
       b.Valor_Outras,
       b.Valor_IPI,
       b.Valor_ICMS,
       b.Valor_FCP,
       b.Valor_ST,
       b.Valor_PIS_Venda,
       b.valor_COFINS,
       b.Total_Impostos,
       b.Valor_Comissao,
       b.CPV_Proc_Fixo,
       b.DESP_Oper_Fixa,
       b.CRED_Impostos,
       b.Verba_Fixa,
       b.Verba_Variavel,
       b.Frete,
       b.Placa,
       b.usu_codmar
FROM base b
)

/* Venda por mês: Entradas (devoluções) vêm positivas, então são separadas */
SELECT
       *
FROM tb_consolidado
```

## TB_CLIENTE

```sql
select 
       e085cli.codcli,
       e085cli.nomcli,
       e085cli.codcli || ' - ' || UPPER( e085cli.nomcli ) as xNOMCLI,
       e085cli.cgccpf,
       e085cli.apecli,
       replace(cast((e085hcl.usu_perdup/100) AS VARCHAR2(4000)),'.',',') usu_perdup,
       e085cli.marcli,
       e085cli.codgre,
       e085cli.cepcli,
       e085cli.endcli,
       e085cli.cidcli,
       e085cli.sigufs,
       e085cli.foncli,
       e085cli.datcad,
       e085cli.sitcli,
       e085hcl.codrep,
       e090rep.nomrep,
       e085cli.codpai,
       e006pai.nompai,
       e085cli.codram,
       e085cli.usu_codram,
       upper(usu_tcanais.usu_desram) as usu_desram,
       case when SitFinanceira.codcli is null then NULL
             else 'PENDÊNCIA FINANCEIRA'
       end SitFinanceiro,
       case when SitInadimplencia.codcli is null then 'NAO'
             else 'SIM'
       end SitInadimplencia,
       e085hcl.codcpg,
       e028cpg.abrcpg,
       e085hcl.vlrlim,
       e085hcl.exiage,
       case when exists(select 1 from e085ccl where e085ccl.codcli = e085cli.codcli and e085cli.sigufs='SC') then 'TORNADO-SC'
            when (e085cli.sigufs='PR') then 'TORNADO-PR'
            when (e085cli.sigufs='RS') then 'CLICK-RS'
            else 'PADRAO' end as Dep_Padrao,
        e085hcl.codrve codreve,
        e017rve.nomrve nomrve,
        e017rve.abrrve abrrve,
        CASE
            WHEN e017rve.abrrve IN ('SPI','SPA','SPC') THEN e017rve.abrrve
            ELSE e085cli.sigufs
        END xREGSenha,

        e085hcl.codtpr codtpr
from e085cli
  join e085hcl  on e085cli.codcli = e085hcl.codcli and e085hcl.codfil = 1
  LEFT OUTER JOIN e017rve ON e017rve.codrve = e085hcl.codrve AND e017rve.codemp = e085hcl.codemp
  left outer join e006pai  on e085cli.codpai = e006pai.codpai
  left outer join usu_tcanais  on e085cli.usu_codram = usu_tcanais.usu_codram
  left outer join e090rep  on e085hcl.codrep =  e090rep.codrep
  left outer JOIN e081tab   on e085hcl.codemp = e081tab.codemp and e085hcl.codtpr = e081tab.codtpr
  left outer JOIN e028cpg   on e085hcl.codemp = e028cpg.codemp and e085hcl.codcpg = e028cpg.codcpg
  left outer join(select codcli
         from e301tcr
        where sittit = 'AB'
          and codtpt = '01'
          and vctpro < (SYSDATE - 7)
        group by codcli) SitFinanceira
                on SitFinanceira.codcli = e085cli.codcli
  left outer join (select tcr.codcli
                     from e301tcr tcr
                     join e039por por on por.codpor=tcr.codpor
                    where por.codpor IN ('ADV','ANGE','CART','CNTH','EXCE','GBNT','PERD','PLCB','REN')
                      AND TCR.VLRABE>0
                 group by tcr.codcli) SitInadimplencia
                       on SitInadimplencia.codcli = e085cli.codcli
```

## TB_FAMRMC

```sql
SELECT
USU_CODFAMDAY AS CODRMC,
USU_DESFAMDAY AS DESRMC,
USU_CODFAMDAY || ' - ' || USU_DESFAMDAY AS xDESRMC
FROM USU_TFAMDAY 
```

## TB_MOTIVO

```sql
SELECT
*
FROM usu_te440mot
```

## TB_PRODUTO

```sql
SELECT 
PRO.*,
PRO.CODPRO || ' - ' || UPPER( PRO.CPLPRO ) AS xDESPRO,
           CASE
               WHEN (pro.codfam = '17' AND pro.volpro = 2)     THEN 'ENERGÉTICO 2L'
               WHEN (pro.codfam = '17' AND pro.volpro = 0.473) THEN 'ENERGÉTICO 473ML'
               WHEN (pro.codfam = '17' AND pro.volpro = 0.250) THEN 'ENERGÉTICO 250ML'
               WHEN (pro.codfam = '17' AND pro.volpro = 1)     THEN 'ENERGÉTICO 1L'
               WHEN (pro.codfam = '17' AND pro.volpro = 0.750) THEN 'ENERGÉTICO 750ML'
               WHEN (pro.codfam = '17' AND pro.volpro = 2.5)   THEN 'ENERGÉTICO 2.5L'
               WHEN pro.usu_codfamday = '10002' THEN 'VODKA'
               WHEN pro.usu_codfamday = '10003' THEN 'CERVEJA'
               WHEN pro.usu_codfamday = '10004' THEN 'GIN'
               WHEN pro.usu_codfamday = '10005' THEN 'WHISKY'
               WHEN pro.usu_codfamday = '10006' THEN 'KIDS'
               WHEN pro.usu_codfamday = '10008' THEN 'OUTROS ALCOOLICOS'
               WHEN pro.usu_codfamday = '10009' THEN 'OUTROS NÃO ALCOOLICOS'
               WHEN pro.usu_codfamday = '10001' THEN 'OUTROS ALCOOLICOS'
               WHEN pro.usu_codfamday = '10011' THEN 'OUTROS ALCOOLICOS'
               WHEN pro.usu_codfamday = '10012' THEN 'SAFADAO'
               WHEN pro.usu_codfamday = '10013' THEN 'OUTROS ALCOOLICOS'
               WHEN pro.usu_codfamday = '10014' THEN 'OUTROS NÃO ALCOOLICOS'
               WHEN pro.usu_codfamday = '10015' THEN 'ISOTONICOS'
               ELSE 'OUTROS'
           END AS classificacao_bi
FROM I260220090255_ERPP.USU_V075PRO2 PRO
```

## TB_REDE

```sql
SELECT
CODGRE,
NOMGRE,
SIGGRE,
CODGRE || ' - ' || UPPER( NOMGRE ) AS xNOMGRE
FROM E069GRE
```

## TB_REP

```sql
SELECT
*
FROM I260220090255_ERPP.USU_V090REP
```

## DRE_Geral - Plan

Não é SQL. É uma consulta M (Power Query) que lê a aba `DRE_Geral - Jan26 a Set26` da planilha da Controladoria, remove linhas vazias, promove o cabeçalho, mantém as colunas usadas e tipa cada uma. Modo Import.

```m
let
  Origem = Excel.Workbook(File.Contents("\\dados\Financeiro\Controladoria\BD_BI\DRE CLIENTE CONSOLIDADO\DRE CLIENTE_10112026.xlsx"), null, true),
  #"Navegação 1" = Origem{[Item = "DRE_Geral - Jan26 a Set26", Kind = "Sheet"]}[Data],
  #"Linhas filtradas" = Table.SelectRows(#"Navegação 1", each [Column1] <> null and [Column1] <> ""),
  #"Cabeçalhos promovidos" = Table.PromoteHeaders(#"Linhas filtradas", [PromoteAllScalars = true]),
  #"As outras colunas foram removidas" = Table.SelectColumns(#"Cabeçalhos promovidos", {"Operacao", "Filial", "Mes_Ano", "Data_NF", "Tipo_Nota", "Codigo_repres_NF", "Nota_Fiscal", "Cod_Cliente", "CFOP", "CodProduto", ...}),
  #"Tipo de coluna alterado 1" = Table.TransformColumnTypes(#"As outras colunas foram removidas", {{"Operacao", type text}, {"Tipo_Nota", type text}, {"Motivo_Devolucao", type text}, {"Frete", type number}, ...})
in
  #"Tipo de coluna alterado 1"
```

Trechos longos (listas de colunas e tipos) foram abreviados com `...`. A consulta completa está no modelo, na partição `DRE_Geral - Plan`.

Para trocar a versão da planilha, altere o caminho em `Origem = Excel.Workbook(...)` (Transformar dados > Editor Avançado). Ver também o ponto de atenção sobre o período no [README](../README.md).

## Tabelas sem SELECT

- **UF/REGIÃO** e **Senhas/Gerente**: dataflow do Power BI Service (entidades de mesmo nome).
- **DRE_LV1** e **DRE_LV2**: dados fixos embutidos no M.
- **CALENDARIO DAX** e **xSenha**: calculadas em DAX.
- **DRE_Geral - Plan**: planilha Excel, ver seção acima.

## SQLs fora do modelo

Existem nas pastas, mas não têm tabela no modelo atual.

### TB_TNS

```sql
SELECT
comnop,
codtns,
destns,
dettns,
comnop || ' - ' || UPPER(destns) AS xDestns
FROM e001tns
where sittns = 'A'
```

### TB_PEDIDOS

```sql
SELECT
    PED.CODFIL                                          AS FILIAL,
    CASE 
	    WHEN PED.SITPED = 5 THEN 'Cancelado' 
	    ELSE PED.SITPED 
	END AS SITUACAO,
    TO_CHAR(PED.DATGER, 'DD/MM/YYYY')                   AS DATA_GERACAO,
    CASE 
	    WHEN PED.DATPRV <= (PED.DATEMI + 1) THEN NULL
        ELSE TO_CHAR(PED.DATPRV, 'DD/MM/YYYY') 
    END    												AS DATA_PREVISAO,
    CLI.SIGUFS                                          AS UF,
    PED.CODREP                                          AS COD_REPRESENTANTE,
    REP.APEREP                                          AS REPRESENTANTE,
    CAT.REGIAO                                          AS REGIAO,
    CAT.NOMSUP                                          AS SUPERVISOR,
    CAT.NOMKEY                                          AS KEY_ACCOUNT,
    CAT.NOMGRE                                          AS GERENTE_REGIONAL,
    CAT.NOMGER                                          AS GERENTE_NACIONAL,
    NVL(CAN.USU_DESRAM, '')                             AS CANAL_SEGMENTO,
    CPG.DESCPG                                          AS CONDICAO_PGTO,
    PED.NUMPED                                          AS NUM_PEDIDO,
    CLI.CODCLI                                          AS COD_CLIENTE,
    CLI.NOMCLI                                          AS RAZAO_SOCIAL,
    CLI.CODGRE                                          AS COD_GRUPO,
    NVL(GRE.NOMGRE, '')                                 AS GRUPO,
    IPD.CODPRO                                          AS COD_PRODUTO,
    PRO.CPLPRO                                          AS PRODUTO,
    IPD.QTDPED                                          AS QTD_ITEM_PCT,
    CASE WHEN PED.SITPED = 9 THEN IPD.VLRBRU
         WHEN PED.SITPED = 1 THEN IPD.VLRLIQ
         ELSE IPD.USU_VLRLIQ END                        AS VALOR_PEDIDO,
    IPD.QTDPED * DER.PESBRU                             AS PESO_ITEM_PEDIDO
FROM E120PED PED
JOIN E120IPD IPD
  ON IPD.CODEMP = PED.CODEMP
 AND IPD.CODFIL = PED.CODFIL
 AND IPD.NUMPED = PED.NUMPED
JOIN E075PRO PRO
  ON PRO.CODPRO = IPD.CODPRO
JOIN E075DER DER
  ON DER.CODPRO = IPD.CODPRO
 AND DER.CODDER = IPD.CODDER
JOIN E085CLI CLI
  ON CLI.CODCLI = PED.CODCLI
JOIN E028CPG CPG
  ON CPG.CODCPG = PED.CODCPG
LEFT JOIN E069GRE GRE
  ON GRE.CODGRE = CLI.CODGRE
JOIN E090REP REP
  ON REP.CODREP = PED.CODREP
LEFT JOIN USU_TCANAIS CAN
  ON CAN.USU_CODRAM = CLI.USU_CODRAM
LEFT JOIN USU_VIEW_CATEGORIA_REPRESENTANTE CAT
  ON CAT.CODCLI = PED.CODCLI
WHERE PED.DATGER >= TO_DATE('27/07/2026', 'DD/MM/YYYY')
  AND PED.DATGER <  TO_DATE('31/07/2026', 'DD/MM/YYYY') + 1
ORDER BY PED.CODFIL, PED.NUMPED
```
