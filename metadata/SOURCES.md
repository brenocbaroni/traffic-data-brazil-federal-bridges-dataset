# Data sources and provenance

## DNIT source workbooks

The source workbooks were copied byte-for-byte and renamed only to produce stable, machine-friendly paths.

| Repository path | Original filename | Campaign | Content |
|---|---|---:|---|
| `data/raw/dnit/2021/VMDA_2021.xlsx` | `VMDa 2021.xlsx` | 2021 | PNCT annual average daily traffic database |
| `data/raw/dnit/2021/UMO_weighing_2021.xlsx` | `Dados_Solicitados_de_UMOs.xlsx` | 2021 | Mobile operational unit weighing records |
| `data/raw/dnit/2023/VMDA_2023.xlsx` | `VMDA 2023.xlsx` | 2023 | PNCT annual average daily traffic database |
| `data/raw/dnit/2023/UMO_weighing_2023.xlsx` | `RegistroPesagemUMO_DNIT_2023.xlsx` | 2023 | Mobile operational unit weighing records |

Official source: https://servicos.dnit.gov.br/dadospnct

## Processing lineage

The derived traffic tables follow three main layers:

1. **Axle-group shares (AGS):** directional heavy-vehicle frequencies from selected PNCT count segments.
2. **Intra-group class proportions (UCP/IGCP):** QFV class proportions conditional on axle group, balanced equally across UMOs within each state.
3. **Integrated class shares (ICS):** AGS multiplied by UCP and normalized within state.

The `traffic-contracts` files combine 2023 ICS values with station-balanced gross-weight fits and axle-group load regressions. The numerical simulation-result files are frozen exports supporting the figures and tables in the associated manuscript; simulation histories and source code are intentionally excluded.
