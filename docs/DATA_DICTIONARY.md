# Data dictionary

## Conventions

- **UF:** Brazilian federal-unit abbreviation.
- **BR:** federal highway identifier.
- **UMO:** mobile operational weighing unit (*Unidade Móvel Operacional*).
- **QFV:** DNIT vehicle-configuration classification.
- **AADTT:** annual average daily truck traffic. Unless otherwise stated, structural-simulation files use directional vehicles/day.
- **GVW/PBT:** gross vehicle weight (*Peso Bruto Total*).
- **AGS:** state axle-group share obtained from traffic counts.
- **UCP/IGCP:** class share conditional on axle group, balanced across UMOs.
- **ICS:** integrated class share obtained from AGS × UCP.
- **Q117:** characteristic load effect extrapolated to a 117-year return period.

Blank fields represent unavailable or non-applicable values unless a file-specific flag states otherwise.

## Raw DNIT workbooks

### `data/raw/dnit/2021/VMDA_2021.xlsx`

- Main sheet: `VMDa 2021` (23,048 data rows; 41 columns).
- Metadata sheet: `Metadados`.
- Contains PNCT count locations, road descriptors, traffic volumes, and directional heavy-vehicle classes.

### `data/raw/dnit/2023/VMDA_2023.xlsx`

- Main sheet: `SNV202401A` (6,423 data rows; 55 columns).
- Metadata sheet: `Metadados`.
- Heavy-vehicle directional columns follow the pattern `{A..J}_C` and `{A..J}_D`.

### `data/raw/dnit/2021/UMO_weighing_2021.xlsx`

- 178,033 weighing records and 59 columns.
- Identification/location: `UMO`, `Equipamento`, `DataHoraRegistroPesagem`, `UF`, `Rodovia`, `Km`, `Latitude`, `Longitude`.
- Classification: `VeiculoClassificacaoQFV`.
- Measurements: `MedicaoRealizadaPBT` and `MedicaoRealizadaEixo1` through `MedicaoRealizadaEixo9`, in the source units.
- Remaining fields contain regulatory limits, tolerances, and measured excesses.

### `data/raw/dnit/2023/UMO_weighing_2023.xlsx`

- 493,693 weighing records and 28 columns.
- Identification/location: `Data Hora`, `UF`, `Municipio`, `Local`.
- Classification: `ClassificacaoQFV`.
- Measurements: `MedicaoPBT` and `MedicaoGrupoEixo1` through `MedicaoGrupoEixo9`.
- Paired `Excesso...` columns report measured regulatory excesses; `MedicaoCMT` is the measured combined gross weight where applicable.

## Consolidated traffic tables

### `vmda_selected_all_ufs.csv`

One row per retained federal-highway corridor and UF. The 2021 and 2023 editions preserve their source-specific road descriptors.

- `UF` or `sg_uf`: federal unit.
- `CODIGO_BR` or `vl_br`: federal highway.
- `{A..H}_C`, `{A..H}_D` (and source-edition extensions): directional heavy-vehicle counts by axle group.
- `_PESADOS_TOTAL`: total heavy-vehicle traffic used during selection.
- `_is_ref`, `_is_est` (2023): reference/estimated-segment flags.

### `umo_ucp_within_group_all_ufs.csv`

One row per UF, axle group, and QFV class.

- `VMDa_Class`: count-data axle group.
- `VeiculoClassificacaoQFV`: QFV vehicle class.
- `share_within_group_balanced`: UCP/IGCP, giving the class proportion conditional on axle group.
- `n_umos_used`: number of UMOs contributing to the balanced estimate.
- `n_rows_used`: number of underlying weighing records.

### `umo_qfv_share_scaled_all_ufs.csv`

One row per UF, axle group, and QFV class after integrating counts and weighing.

- `vmda_group_share`: AGS from the traffic-count layer.
- `scaled`: unnormalized AGS × UCP product.
- `share_scaled_by_vmda`: final normalized ICS.
- `fallback_all_umos`: whether pooled weighing information was used instead of a direct state estimate.

### Fitted-model files

- `gvw_distribution_fits_station_balanced.xlsx`: class-specific parametric GVW fits after equal weighting of UMOs.
- `axle_group_load_regressions.csv`: coefficients relating axle-group loads to GVW by vehicle class/group.
- `traffic-contracts/stpg_contract_<UF>.yaml`: complete numeric traffic input for each simulated state fleet; weights are expressed in kN and lengths in m as declared inside each file.
- `traffic-contracts/lane_headways_by_uf.csv`: state directional traffic volumes and associated mean headways used in traffic generation.

## Diagnostics

- `aadtt_by_state_summary.csv`: state summaries (`mean`, `max`, `p90`, and `p95`) of AADTT.
- `umo_adequacy_assessment.csv`: record/station coverage checks and fallback reasons by UF and axle group.
- `umo_bootstrap_ci.csv`: bootstrap precision diagnostics for dominant and class-level UCP estimates.

## Simulation results

### State-specific application

- `measured_volume_all.csv`: numerical load-effect results for state traffic contracts under measured AADTT.
- `worst_scenario_summary.csv`: governing scenario and extrapolated effect by bridge, state, and structural response.

### Controlled factorial application

- `factorial_cells.csv`: cell-level results for the 75 bridge × composition × volume simulations. Multiple structural responses from the same simulation produce 165 bridge–effect response rows.
- `factorial_contrasts.csv`: fitted volume elasticities, composition contrasts, curvature measures, model coefficients, and confidence intervals by bridge–effect family.
- `factorial_validity.csv`: recording, tail-size, and multiple-presence diagnostics.
- `factorial_families.json`: common thresholds and family-level settings used in the comparison.
- `factorial_bootstrap_draws.csv.gz`: paired block-bootstrap draws supporting the reported 90% intervals.

In contrast tables, suffixes `_lo` and `_hi` identify lower and upper confidence limits. Columns ending in `_pct` are percentages; `elasticity_per_doubling_pct` is the percent response change for each doubling of directional AADTT.

### Robustness and references

- `robustness/`: tail-threshold, tail-model, and bootstrap sensitivity results.
- `reference-effects/tb45_effects.csv`: calculated TB-45 reference effects.
- `reference-effects/portela_effects.csv`: structural reference values based on the Portela comparison procedure used in the manuscript.
