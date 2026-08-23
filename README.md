# Multi-State Heavy-Vehicle Traffic Data for Brazilian Federal Bridges

This is the data-only repository associated with the manuscript **“Multi-State Heavy-Vehicle Weight Distributions on the Brazilian Federal Highway Network.”** It contains the public DNIT source workbooks, consolidated state-level traffic tables, fitted traffic-model inputs, and numerical outputs supporting the structural applications.

No analysis notebooks, source code, simulation code, or manuscript files are included.

## Repository contents

```text
data/
├── raw/dnit/
│   ├── 2021/                 Original traffic-count and UMO weighing workbooks
│   └── 2023/                 Original traffic-count and UMO weighing workbooks
├── processed/
│   ├── 2021/                 Consolidated 2021 traffic tables and fitted models
│   ├── 2023/                 Consolidated 2023 traffic tables and fitted models
│   ├── diagnostics/          Coverage and adequacy summaries
│   └── traffic-contracts/    State traffic inputs used by the structural simulator
└── simulation-results/
    ├── state-specific/       Results for state fleets at measured AADTT
    ├── controlled-factorial/ Results for the 75-cell volume/composition design
    ├── robustness/           Tail-model and bootstrap sensitivity outputs
    └── reference-effects/    Numerical structural-reference effects
docs/
└── DATA_DICTIONARY.md
metadata/
├── FILES.csv                 File sizes, SHA-256 checksums, and LFS status
└── SOURCES.md                Provenance and original-file mapping
```

## Main derived tables

For each campaign edition, the principal reusable outputs are:

- `vmda_selected_all_ufs.csv`: retained high-volume monitored federal-road corridors and directional truck counts;
- `umo_ucp_within_group_all_ufs.csv`: intra-group class proportions balanced across mobile weighing units;
- `umo_qfv_share_scaled_all_ufs.csv`: integrated state vehicle-class shares combining counts and weighing;
- `gvw_distribution_fits_station_balanced.xlsx`: station-balanced class-specific gross-vehicle-weight fits;
- `axle_group_load_regressions.csv`: fitted axle-group load relationships.

Definitions, units, and column descriptions are provided in [`docs/DATA_DICTIONARY.md`](docs/DATA_DICTIONARY.md).

## Source data

The source workbooks were published by the Brazilian National Department of Transport Infrastructure (DNIT) through the National Traffic Counting Program (PNCT):

- PNCT data portal: https://servicos.dnit.gov.br/dadospnct
- DNIT open-data page: https://www.gov.br/dnit/pt-br/dadosabertos

The four source workbooks are redistributed without changing their contents; only their filenames were standardized. Original names and checksums are recorded in [`metadata/SOURCES.md`](metadata/SOURCES.md) and [`metadata/FILES.csv`](metadata/FILES.csv).

## Git LFS

Excel workbooks are stored using [Git Large File Storage](https://git-lfs.com/). Install Git LFS before cloning:

```bash
git lfs install
git clone https://github.com/brenocbaroni/traffic-data-brazil-federal-bridges-dataset.git
```

To download only the repository metadata first:

```bash
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/brenocbaroni/traffic-data-brazil-federal-bridges-dataset.git
cd traffic-data-brazil-federal-bridges-dataset
git lfs pull
```

## Privacy and scope

The UMO workbooks were checked for direct personal identifiers. Their published fields do not include vehicle plates, names, CPF numbers, or driver identifiers. They do contain weighing date/time and operational location fields. Users remain responsible for complying with applicable source terms and data-protection requirements.

The processed tables describe monitored federal corridors and enforcement-weighing campaigns. They are not probability samples of all Brazilian truck traffic and should not be interpreted as a complete national census.

## Integrity

`metadata/FILES.csv` provides the byte size and SHA-256 checksum of every distributed data file. After downloading the LFS objects, files can be checked independently against this manifest.

## Citation

Citation metadata are available in [`CITATION.cff`](CITATION.cff). A DOI can be added after archiving a tagged release in Zenodo or another research-data repository.

## Licensing and attribution

The DNIT source workbooks remain government-source data and are not relicensed by this repository. Derived research tables and outputs require a license choice by the authors before a versioned public release. See [`LICENSES.md`](LICENSES.md).
