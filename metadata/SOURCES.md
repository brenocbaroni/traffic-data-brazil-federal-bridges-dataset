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

The class gross-weight distributions pool all 2023 weighing records of each class nationally, every record weighted equally. `data/processed/2023/gvw_models.csv` summarizes the models fitted to them: below the 95th percentile, the distribution selected by the procedure of Rossigali (2013); above it, a generalized Pareto tail fitted by maximum likelihood and truncated at the physical weight limit of the class. Classes with fewer than 1,000 records keep a single distribution of the same procedure.

The `traffic-contracts` files combine 2023 ICS values with these gross-weight models, tabulated at 0.25 t as used by the simulator, and with the axle-group load regressions. They are the exact inputs used in the structural simulations.

`data/processed/state_groups_aadtt.csv` reproduces the per-state composition group and busiest-corridor AADTT that supported the manuscript's composition groups.

## Simulation results

The files in `data/simulation-results/` are frozen exports (simulations completed on 7 October 2026 and exported on 9 October 2026) of the free-flow simulations used in the manuscript: seven state traffic streams and the traffic of Rossigali (2013), four lane layouts, twelve bridges and 30 days per combination. For each stream and effect, an exponential tail (a generalized Pareto distribution with zero shape) is fitted to the 50 largest peaks and extrapolated to Q117. The girder and lane layout of each stream are those with the largest simulated peak. The TB-45 reference is the literal NBR 7188 model and the Portela reference includes multiple-presence factors. The Rossigali traffic appears only through its simulated effects, and its source database is not redistributed. Simulation histories, intermediate files and source code are intentionally excluded.

Version 3.0.0 replaced the results of version 2.0.0 (station-balanced gross-weight fits, generalized Pareto tails above a threshold common to all streams), which no longer correspond to the manuscript. Version 2.0.0 had replaced version 1.0.0 (ten bridges, Weibull extrapolation, controlled factorial and robustness studies). Both remain available in the Git history of the repository.
