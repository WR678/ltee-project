# LTEE variant calling

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.24535626.svg)](https://doi.org/10.5281/zenodo.24535626)

Variant calling on three *E. coli* clones from population Ara-3 of Richard
Lenski's Long-Term Evolution Experiment (LTEE), frozen at 5,000, 15,000 and
50,000 generations. Question: how many mutations accumulated, and is the
answer robust to the minimum read depth used to filter variant calls?

Built as the example project of the Reproducible and Open Science lessons
(CBIO7501, MBZUAI, Fall 2026). The analysis steps follow the Data Carpentry
lesson *Wrangling Genomics* (CC BY 4.0).

Author: Rui Wang (ORCID [0009-0008-5273-0060](https://orcid.org/0009-0008-5273-0060))

## Publication

None yet. The data and biology are from Tenaillon et al. (2016), *Nature*
536:165–170, <https://doi.org/10.1038/nature18959>.

## Reproduce the results

Requirements: [Pixi](https://pixi.prefix.dev/latest/) and an internet
connection. Linux or macOS (on Windows use WSL2: `bwa`, `samtools` and
`bcftools` have no Windows builds). No other installation is needed.

```bash
git clone https://github.com/WR678/ltee-project.git
cd ltee-project
pixi run reproduce
```

`pixi run reproduce` installs the exact software versions in `pixi.lock`,
downloads the raw data and checks its checksums, runs the Snakemake workflow
and renders the report. A GitHub Action runs the same command on every push.

Other entry points: `pixi run run-nextflow` (the same pipeline in Nextflow),
`pixi run compare-workflows` (checks that both give identical variant calls),
`pixi run compare-to-shell` (the same check against the original shell
script), `pixi task list` (all tasks).

## Container

A container image of the pipeline environment can be built from
`containers/Dockerfile` (see the comments in that file):
`docker build --platform linux/amd64 -f containers/Dockerfile --build-arg ENV_NAME=pipeline -t ltee-project:0.1.0 .`
The image built on 2026-11-11 from `pixi.lock` at commit 3a11dd5 is on the
GitHub Container Registry as `ghcr.io/wr678/ltee-project:0.1.0`, digest
`sha256:be7b36879a9f7967c7d8e0fac30b82f6061216af0908e06c057b02e034641076`
(local image ID `sha256:38d3278bb392f66928df57dccc18fadd22d6daf7dde46c0f38cfcb58f1abb2a5`).
Run the pipeline in it with
`docker run --rm --platform linux/amd64 -v "$PWD":/work -w /work ghcr.io/wr678/ltee-project:0.1.0 bash scripts/run_variant_calling.sh`.

## Hardware

Any laptop from the last few years. Measured on 2026-11-18 (Intel Core
i7-12700H, Ubuntu 26.04 under WSL2, 4 cores): about 5 GB of disk for the two
software environments, 333 MB for the raw data and 100 MB for the Snakemake
results; the workflow takes under a minute and less than 400 MB of memory.

## Outputs

| Path | Content |
|------|---------|
| `reports/ltee-report.html` | The report: data, read quality, variants, the depth sensitivity analysis, software versions |
| `results/qc/multiqc/multiqc_report.html` | Read quality summary |
| `results/vcf/` | Filtered variant calls per sample (minimum depth from `config/config.yaml`) |
| `results/sensitivity/` | Variant calls at each depth in `sensitivity_depths` |
| `results/stats/` | Alignment (`flagstat`) and variant (`bcftools stats`) statistics |

Expected filtered variants at minimum depth 2: SRR2589044 10, SRR2584863 25,
SRR2584866 775.

## Data

Raw reads: a subset (175,000 read pairs per sample) of three runs prepared by
Data Carpentry, figshare, <https://doi.org/10.6084/m9.figshare.7726454> (file
`sub.tar.gz`, CC0). Original runs in NCBI BioProject PRJNA295606. Reference:
*E. coli* B str. REL606, GenBank CP000819.1. Sources, provider checksums and
our checksums are in `metadata/`; the data is not stored in this repository.

## Data availability

The raw sequencing reads are in the NCBI Sequence Read Archive under
BioProject PRJNA295606 (runs SRR2589044, SRR2584863 and SRR2584866). This
project uses the trimmed and subsampled copy prepared by Data Carpentry, on
figshare at <https://doi.org/10.6084/m9.figshare.7726454> (file `sub.tar.gz`,
CC0). The reference genome is *E. coli* B str. REL606, GenBank CP000819.1
(assembly GCA_000017985.1), freely available from NCBI.
`scripts/download_data.py` downloads every raw file from the sources listed in
`metadata/sources.tsv` and checks it against `metadata/checksums.sha256`.
Derived data and results are not distributed: they are regenerated from the
raw data by the code in this repository. Code and environment:
<https://github.com/WR678/ltee-project> (MIT). Release v1.0.0 is archived on
Zenodo at <https://doi.org/10.5281/zenodo.24535626>; the concept DOI
<https://doi.org/10.5281/zenodo.24535625> always points to the latest release.

## Repository contents

See the table in `docs/structure.md` (each folder also has its own README).

## Cite

See `CITATION.cff` (GitHub: "Cite this repository").

## License

Code: MIT (`LICENSE`). Report text and figures: CC BY 4.0. The raw data is
CC0 (see Data).

## Contact

Questions and problems: open an issue on GitHub. Contributions: see
`CONTRIBUTING.md`. Everyone taking part follows `CODE_OF_CONDUCT.md`.
