# Model Quality in Football: Quantifying the Quality of an Expected Threat Model

<div align="center">

[![ArXiv](https://img.shields.io/badge/arXiv-preprint-b31b1b?style=flat-square&logo=arxiv)](https://arxiv.org)
[![Python](https://img.shields.io/badge/Python-3.12.3-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![StatsBomb](https://img.shields.io/badge/Data-StatsBomb%20Open%20Data-0066CC?style=flat-square)](https://github.com/statsbomb/open-data)

*Koen van Arem · Jakob Söhl · Mirjam Bruinsma · Geurt Jongbloed*

*Delft University of Technology*

</div>

---

## Overview

This repository contains the data, code, and analysis accompanying the preprint:

> **"Model quality in football: quantifying the quality of an Expected Threat model"**
> Koen van Arem, Jakob Söhl, Mirjam Bruinsma, Geurt Jongbloed
> *[Link to paper](https://arxiv.org/abs/2604.21087)*

Expected Threat (xT) models are widely used in football analytics to value ball actions. This work addresses a fundamental but underexplored question: **how good is a trained xT model, really?** We develop a framework to rigorously quantify model quality, provide uncertainty estimates, and derive practical rules of thumb for practitioners.


---
## `xThreat` — Core Model Class

The backbone of this repository. `src/xThreat/model.py` implements the `XThreat` model class used across nearly all scripts and notebooks. It provides the main interface to **train, visualize, and apply** an Expected Threat model, and is a natural starting point if you want to understand or reuse the modelling framework.

---

## Repository Structure
Repository Structure

The repository is organized into three main components:

### 1. Python package (src/xthreat)

Reusable implementation of the Expected Threat model.

```
src/xthreat/
├── model.py        ← Core XThreat model implementation
└── __init__.py
```

The package provides a clean API for training and applying xT models programmatically.
After installing it via pip, the model can be imported via 
```
from xThreat import xThreat
```


Legacy entry point:
```
xThreat.py  ← deprecated standalone script (kept for compatibility with computing cluster)
```
---
### 2. Experimental pipeline (paper-2026/)

This folder contains the full reproducibility pipeline used in the paper.
```
paper-2026/
├── 1-data-preparation/
├── 2-train-models/
├── 3-calculate-values/
└── 4-investigate-results/
```
---

#### Part 1 — Data Preparation

📁 `1-data-preparation/`

| File | Description |
|------|-------------|
| `download_clean_join.ipynb` | End-to-end pipeline: download, clean, and join StatsBomb event data |
| `datadownloader.py` | Fetches raw event data from the StatsBomb open dataset |
| `datacleaner.py` | Cleans and filters raw events |
| `datasetcreator.py` | Joins cleaned data into a unified modelling-ready format |

Run `download_clean_join.ipynb` first to produce the dataset used in all subsequent steps.

---

#### Part 2 — Model Training

📁 `2-train-models/`

| Subfolder | Description |
|-----------|-------------|
| `sample_distribution.py` | Script which illustrates how the ground truth can be used to resample new models |
| `resampled-models/model-error-distribution/` | Scripts for simulations of estimation error (parallel versions for HPC) |
| `resampled-models/max-acceptable-error/` | Scripts for simulations for maximal acceptable error (parallel versions for HPC) |

> **⚠️ Note on large-scale computations**
>
> The full resampling procedure was run on the **[DelftBlue supercomputer](https://www.tudelft.nl/dhpc/ark:/44463/DelftBluePhase1)** at TU Delft and produces a large number of model files not included here due to storage constraints. Two example models are provided. The sampling scripts can be adapted to run locally at smaller scale.
>
> If you are interested in the full set of resampled models, feel free to reach out to [k.w.vanarem@tudelft.nl](mailto:k.w.vanarem@tudelft.nl) to discuss transfer options.

---

#### Part 3 — Calculate Values

📁 `3-calculate-values/`

Contains the core computations that analyse the resampled models. Subfolders are labelled to match the paper's section numbering.

| Subfolder | Paper section | Description |
|-----------|---------------|-------------|
| `example_calculate_errors.py` | Example of calculating the errors without parallel computing |
| `example_calculate_errors_parallel.py` | Example of calculating the errors wit parallel computing |
| `s4.1-model-error-distribution/` | §4.1.1 | Computes bootstrap error distributions; pre-computed results saved as `.csv` |
| `s4.2-max-error-quartile-changes/calc-model-error/` | §4.1.2 | Computes maximum model errors across configurations; is done in two batches (A & B) |
| `s4.2-max-error-quartile-changes/calc-player-xT-created/` | §4.1.2 | Computes resampled player ratings and corresponding model errors |

> **⚠️ Note on player ratings**
>
> The computed player rating values from `calc-player-xT-created/` are **not included** in the repository due to file size. Please contact [k.w.vanarem@tudelft.nl](mailto:k.w.vanarem@tudelft.nl) if you need these files.

---

#### Part 4 — Investigate Results

📁 `4-investigate-results/`

> 🌟 **Recommended starting point for most visitors**

Notebooks that present the paper's findings. Subfolders correspond to paper sections.

##### §4.1 — Distribution of Model Error
📁 `s4.1-distribution-model-error/`

| Notebook | Description |
|----------|-------------|
| `distribution_fitting.ipynb` | Fits parametric distributions to the bootstrap errors |
| `model_parameters_influence.ipynb` | Analyses how grid resolution and sample size affect model quality |
| `example_large_error.ipynb` | A quick illustration of a case with a large estimation error due to insufficient data |

##### §4.2 — Acceptable Model Error
📁 `s4.2-acceptable-model-error/`

| Notebook | Description |
|----------|-------------|
| `find_maximal_error.ipynb` | Determines the threshold for acceptable model error in terms of quartile changes in player rankings |

##### §5 — Application and Illustration
📁 `s5-application-and-illustration/`

| Notebook | Description |
|----------|-------------|
| `rules_of_thumb_paper.ipynb` | Derives and demonstrates practical rules of thumb for model quality |
| `example_euros_2020.ipynb` | Applied example: using the xT model on UEFA Euro 2020 data |

---
### 3. Data storage (data-storage/)

All datasets, model artifacts, and computed outputs are stored here.
```
data-storage/
├── raw/                        ← Raw StatsBomb data
├── preprocessed/              ← Cleaned datasets
├── models/
│   ├── ground-truth/          ← Trained xT models (various grid sizes)
│   └── resampled/             ← Bootstrap / sampled models
└── outputs/
    ├── distribution/          ← Error distribution results
    └── maximal-error/         ← Threshold / sensitivity results
```
---
## Installation

Install dependencies:
```bash
pip install -r requirements.txt
```
Or install the package in editable mode (recommended):
```bash
pip install -e .
```

## Repository Structure

```text
.
├── src/
│   └── xthreat/
│       ├── __init__.py
│       └── model.py                  ← Core xThreat model implementation (package version)
│
├── xThreat.py                        ← Legacy / standalone entry-point for model usage
│
├── paper-2026/                      ← Full experimental pipeline used in the paper
│
│   ├── 1-data-preparation/
│   │   ├── download_clean_join.ipynb
│   │   ├── datacleaner.py
│   │   ├── datadownloader.py
│   │   └── datasetcreator.py
│
│   ├── 2-train-models/
│   │   ├── sample_distribution.py
│   │   ├── true-models/
│   │   │   └── calculate_true_models.py
│   │   │
│   │   └── resampled-models/
│   │       ├── model-error-distribution/
│   │       │   ├── sample_distribution_parallel_A.py
│   │       │   └── sample_distribution_parallel_B.py
│   │       │
│   │       └── max-acceptable-error/
│   │           └── sample_16x12_model_parallel.py
│
│   ├── 3-calculate-values/
│   │   ├── example_calculate_errors.py
│   │   ├── example_calculate_errors_parallel.py
│   │   │
│   │   ├── s4.1-model-error-distribution/
│   │   │   ├── calculate_errors_A.py
│   │   │   └── calculate_errors_B.py
│   │   │
│   │   └── s4.2-max-error-quartile-changes/
│   │       ├── calculate_errors.py
│   │       └── calculate_resampled_player_ratings.py
│
│   └── 4-investigate-results/
│       ├── s4.1-distribution-model-error/
│       │   ├── distribution_fitting.ipynb
│       │   ├── example_large_error.ipynb
│       │   └── model_parameters_influence.ipynb
│       │
│       ├── s4.2-acceptable-model-error/
│       │   └── find_maximal_error.ipynb
│       │
│       └── s5-application-and-illustration/
│           ├── example_euros_2020.ipynb
│           └── rules_of_thumb.ipynb
│
├── data-storage/
│   ├── raw/
│   │   ├── euros_2020/
│   │   └── top_5_leagues/
│   │
│   ├── preprocessed/
│   │   └── euros_2020.parquet
│   │
│   ├── models/
│   │   ├── ground-truth/
│   │   │   ├── xt-full-data-n_x8-n_y6.pickle
│   │   │   ├── xt-full-data-n_x64-n_y48.pickle
│   │   │   └── (other grid resolutions)
│   │   │
│   │   └── resampled/
│   │       ├── xT_16x12_N_3_350_000.pkl
│   │       ├── xt-N100000-...pickle
│   │       └── xt-N1300000-...pickle
│   │
│   └── outputs/
│       ├── distribution/
│       │   ├── bootstrap_errors_A.csv
│       │   └── bootstrap_errors_B.csv
│       │
│       └── maximal-error/
│
├── requirements.txt
├── pyproject.toml
└── README.md
```

---

## Getting Started

### Installation

```bash
pip install -r requirements.txt
```

### Recommended Workflow
Follow the structure in paper-2026. This structure is:
```
1-data-preparation  ──►  2-train-models  ──►  3-calculate-values  ──►  4-investigate-results
```

Most visitors can jump straight to **Part 4** — the notebooks allow for a quick visual inspection of the methods

---

## Authors & Contact

| Author | Affiliation | Contact |
|--------|-------------|---------|
| **Koen van Arem** | Delft University of Technology | [k.w.vanarem@tudelft.nl](mailto:k.w.vanarem@tudelft.nl) |
| **Jakob Söhl** | Delft University of Technology | — |
| **Mirjam Bruinsma** | AFC Ajax | — |
| **Geurt Jongbloed** | Delft University of Technology | — |

For questions about large data files (resampled models, player ratings), please contact Koen van Arem at [k.w.vanarem@tudelft.nl](mailto:k.w.vanarem@tudelft.nl).

---

## Citation

If you use this code or data in your research, please cite:

```bibtex
@article{vanArem2026xthreat,
  title   = {Model quality in football: quantifying the quality of an Expected Threat model},
  author  = {{Van Arem}, Koen and S{\"o}hl, Jakob and Bruinsma, Mirjam and Jongbloed, Geurt},
  journal = {arXiv preprint},
  year    = {2026},
  note    = {arXiv link to be added}
}
```

---

## Data

This project uses the **[StatsBomb Open Data](https://github.com/statsbomb/open-data)** dataset. Please comply with StatsBomb's [terms of use](https://github.com/statsbomb/open-data/blob/master/LICENSE.pdf) when using this repository.

---

<div align="center">
<sub>Made with ⚽ at Delft University of Technology</sub>
</div>