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

The `traffic-contracts` files combine 2023 ICS values with station-balanced gross-weight fits and axle-group load regressions. They are the exact inputs used in the structural simulations.

`data/processed/state_groups_aadtt.csv` reproduces the per-state composition group and busiest-corridor AADTT that supported the manuscript's composition groups.

## Simulation results

The files in `data/simulation-results/` are frozen exports (results computed on 14–15 September 2026 and exported on 23 September 2026) of the free-flow simulations used in the manuscript: seven state traffic streams and the traffic of Rossigali (2013), four lane layouts, twelve bridges and 30 days per combination. Characteristic effects come from generalized Pareto fits above a threshold common to all streams, with the shape parameter restricted to non-positive values. The TB-45 reference is the literal NBR 7188 model and the Portela reference includes multiple-presence factors. The Rossigali traffic appears only through its simulated effects, and its source database is not redistributed. Simulation histories, intermediate files and source code are intentionally excluded.

Version 2.0.0 replaced the results of version 1.0.0 (ten bridges, Weibull extrapolation, controlled factorial and robustness studies), which no longer correspond to the manuscript. They remain available in the Git history of the repository.
