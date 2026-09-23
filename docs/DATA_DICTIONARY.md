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
- **Q117:** characteristic load effect with a 35% probability of exceedance in 50 years, that is, a 117-year return period.
- **Lane layouts:** `2o` two lanes in opposite directions; `2s` two lanes in the same direction; `3s` three lanes in the same direction; `3o` three lanes, one of them opposing. When lanes share a direction, the directional flow is split 0.85/0.15 (two lanes) or 0.80/0.18/0.02 (three lanes).
- **Bridges:** `S-` simply supported and `C-` two-span continuous, followed by the span in metres (each span of the continuous decks). Spans of 10, 20 and 30 m have two girders and spans of 30, 40 and 50 m five girders; the asterisk (`S-30*`, `C-30*`) marks the five-girder 30 m decks. All decks are 13 m wide.
- **Load effects:** `M+` positive bending moment, `M-` negative bending moment (continuous decks only), `V` shear. Moments in kN.m, shear in kN.
- **Streams:** state traffic streams `PB`, `RJ`, `SP` (SP corridors), `MG`, `PR`, `SC`, `MT`, and `ROSSIGALI`, the traffic of Rossigali (2013) simulated with the same rules.

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
- `traffic-contracts/stpg_contract_<UF>.yaml`: complete numeric traffic input for each simulated state traffic stream; weights are expressed in kN and lengths in m as declared inside each file.
- `traffic-contracts/lane_headways_by_uf.csv`: state directional traffic volumes and associated mean headways used in traffic generation.

### `state_groups_aadtt.csv`

One row per federal unit (27 rows; Amapá has no eligible count location and is left blank).

- `uf`, `state`: federal unit code and name.
- `share_6plus_axles_2023_pct`: share of heavy vehicles with six or more axles in the 2023 counts.
- `composition_group`: `light` (below 22%), `intermediate` (22–35%) or `heavy` (above 35%).
- `corridor_2023`, `lanes_per_direction_2023`: busiest monitored corridor in 2023 and its number of lanes per direction.
- `aadtt_dir_2021`, `aadtt_dir_2023`: directional heavy-vehicle AADTT of the busiest monitored corridor in each edition (blank when the edition has no eligible count location).

## Diagnostics

- `aadtt_by_state_summary.csv`: state summaries (`mean`, `max`, `p90`, and `p95`) of AADTT.
- `umo_adequacy_assessment.csv`: record/station coverage checks and fallback reasons by UF and axle group.
- `umo_bootstrap_ci.csv`: bootstrap precision diagnostics for dominant and class-level UCP estimates.

## Simulation results

Each of the 336 combinations of stream, lane layout and bridge was simulated for 30 independent days of free-flow traffic, recording the peak effect of every loading event. For each case, load effect and lane layout, only the peaks above a threshold common to all streams enter the extrapolation. The threshold is set so that the state stream with the fewest peaks keeps 200. The peaks are fitted with a generalized Pareto distribution whose shape parameter is restricted to non-positive values and extrapolated from 30 days to Q117. All bridge, girder and effect columns share the conventions above; `bridge`, `system`, `span_m` and `n_girders` describe the deck.

### `q117_cells.csv`

One row per stream, bridge–effect case and lane layout (8 × 30 × 4 = 960 rows). Within each row, the effect is the largest over the monitored sections and girders.

- `stream`, `composition_group`, `stream_aadtt_dir`: simulated stream, its composition group (blank for `ROSSIGALI`) and directional AADTT (7,019 for the Rossigali reference flow).
- `load_effect`, `unit`, `lane_layout`.
- `governing_section`, `governing_girder`: section and girder that give the largest effect (girder 1 is at the deck edge; sections are `midspan`, `first_end_support`, `second_end_support`, `first_span_0.4L`, `second_span_0.4L` and `central_support`).
- `threshold_u`, `n_exceedances`: common threshold and number of peaks above it.
- `max_observed_30d`: largest peak recorded in the 30 days.
- `gpd_shape_xi`, `gpd_scale_sigma`: fitted generalized Pareto parameters. Values of about −1e-10 mean that the fit sits at the boundary ξ = 0 (exponential tail).
- `q117`, `q117_over_max_observed`: characteristic effect and its ratio to the largest observed peak.
- `tail_fraction_pct`: percentage of all recorded peaks above the threshold.
- `ks_statistic`, `ks_critical`, `ks_pass`: Kolmogorov–Smirnov goodness-of-fit check of the tail.
- `gpd_support_ok`: whether the extrapolated value lies within the support of the fitted distribution.

### `q117_cases.csv`

One row per stream and bridge–effect case (8 × 30 = 240 rows): `q117` is the largest value over the four lane layouts, with the `governing_lane_layout`, `governing_section` and `governing_girder` that produce it.

### `case_summary.csv`

One row per bridge–effect case (30 rows), over the seven state streams.

- `envelope_q117`: largest Q117 of the seven state streams.
- `governing_stream`, `governing_group`, `runner_up_stream`: stream that gives it, its composition group, and the second stream.
- `margin_pct`: percentage by which the governing stream exceeds the second.
- `spread_pct`: percentage by which the largest Q117 of the seven streams exceeds the smallest.
- `tb45_static`, `portela_static`, `rossigali_q117`: reference effects on the same deck.
- `envelope_over_tb45`, `envelope_over_rossigali`, `envelope_over_portela`: ratios of the envelope to each reference.

### Reference effects

- `reference-effects/tb45_effects.csv`: static effect of the TB-45 model of NBR 7188 applied to the same influence surfaces at the governing position, with the crowd load interrupted under the vehicle and without dynamic amplification (`tb45_static`), for the governing girder.
- `reference-effects/portela_effects.csv`: static effect of the load model of Portela (2018) on the same decks, with multiple-presence factors over three design lanes and without dynamic amplification (`portela_static`).
