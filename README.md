# A Comparison of Neural Decoding Methods

Code and figures for the Integrating Project (PIC) **"A Comparison of Neural
Decoding Methods"**, comparing four neural decoders (SVM, LDA, Naive Bayes,
and an adapted Kalman filter) on Neuropixels electrophysiology recorded from
the Basal Ganglia (BG) and Cerebellum (CB) of five mice performing a lever
push/pull reaching task.

**Author:** Diogo Carvalho (ist109988) — Instituto Superior Técnico
**Supervisors:** Teresa Serradas Duarte, Simon Zamora — Paton Lab, Champalimaud Foundation

This repository hosts the analysis notebooks and the full set of comparison
figures, referenced from the report under "Code and data availability".

---

## Overview

Each trial, a mouse pushes or pulls a lever; reach direction is additionally
classified along a mediolateral axis (dominant / center / non-dominant). Decoders
are trained on spike counts in a sliding window (100 ms width, 25 ms step) over
[−2.5, +2.5] s around the lever press (Init) and reach endpoint (Reach), and
evaluated on four independent binary contrasts:

- **pp** — push vs pull
- **D** — dominant vs (center + non-dominant)
- **C** — center vs (dominant + non-dominant)
- **nD** — non-dominant vs (dominant + center)

Performance is balanced accuracy (BAC), so chance is 0.5 for every contrast.

---

## Repository structure

```
decoding_pipeline.ipynb   Main analysis notebook (BG/CB decoding, Kalman, full comparison, robustness)
cross_temporal.ipynb      Cross-temporal (temporal generalisation) decoding
figures/                  All comparison figures
```

### `decoding_pipeline.ipynb`
The complete pipeline, one section per stage (run in order):

| Section | Script | Purpose |
|---------|--------|---------|
| 1 | `s06_SVM_perSess.py`        | Per-session decoding (SVM, LDA, Naive Bayes) for the Basal Ganglia → `SVM_sess_BG.npz` |
| 2 | `s06_SVM_perSess_CB.py`     | Same pipeline for the Cerebellum, with per-cell-type handling → `SVM_sess_CB.npz` |
| 3 | `add_kalman.py`             | Adds the adapted binary Kalman decoder to the saved `.npz` files |
| 4 | `compare_all.py` (robust)   | Comprehensive comparison: per-session / per-animal / grand-average, BG vs CB, AUC, pointwise (Wilcoxon + Cohen's d), latency, per-animal statistics, for all four decoders |
| 5 | `per_animal_robustness.py`  | Neuron-count control, neuron-dropping, and cross-session reliability analyses |

### `cross_temporal.ipynb`
Temporal-generalisation decoding: a decoder trained at each time bin is tested,
without retraining, at every other bin, producing a train-bin × test-bin BAC
matrix per session for BG and CB.

### `figures/`
| Subfolder | Content |
|-----------|---------|
| `per_session/{BG,CB}/`    | One figure per recording session, all decoders and contrasts |
| `per_animal/{BG,CB}/`     | Sessions averaged within each animal |
| `per_animal/BG_vs_CB/`    | Per-animal BG vs CB (all contrasts + pointwise CB−BG) |
| `grand/{BG,CB}/`          | Grand averages across animals (SEM across animals) |
| `grand/`                  | BG vs CB grand-average overviews |
| `auc/`                    | Area-under-curve (total and per 0.5 s window) |
| `pointwise/`              | Bin-by-bin BG vs CB comparison (Wilcoxon + Cohen's d) |
| `neuron_count/`           | Neuron-count control (peak BAC vs number of neurons) |
| `neuron_dropping/`        | Neuron-dropping curves (BAC vs subsampled population size) |
| `cross_temporal/`         | Temporal-generalisation matrices (train bin × test bin) |
| `stats/`                  | CSV outputs of all statistical analyses |

---

## Running the notebooks

The notebooks were developed with Python 3.9 and use `numpy`, `scipy`,
`scikit-learn`, `pandas`, `matplotlib`, `h5py`, and `joblib`.

Before running, set the data paths near the top of each script section:

- `data/SVM_sess_BG.npz`, `data/SVM_sess_CB.npz` — saved decoder outputs.
- `path/to/ephys_data` — the `ephys_and_behavior/mat_files` directory containing
  the raw `.mat` files (only needed to regenerate the `.npz` from scratch).
- `output/` — where figures and stats are written.

Stages 1–2 produce the `.npz` files; stage 3 augments them with the Kalman
decoder; stages 4–5 produce the figures and statistics. `cross_temporal.ipynb`
reads the `.npz` files directly.

---

## Data

The raw electrophysiology (`.mat` files) is **not** included here, as it belongs
to the Paton Lab.

---

## Key findings

1. **All four decoders are equivalent** — no decoder-pair differences; the signal
   is linearly separable and robust to the choice of classifier. The Kalman filter
   confirms the patterns with greater variance (no regularisation).
2. **Push/Pull is the most strongly encoded contrast**; Center is the hardest.
3. **The Cerebellum outperforms the Basal Ganglia** in 21/24 conditions.
4. **Prospective information**: BAC rises above chance 1–2 s before movement onset.
5. The cohort (n = 5) limits group-level statistics (Wilcoxon floor p = 0.0625);
   evidence rests on the consistency of directional trends and per-animal analyses.

---

## License

- **Code** (notebooks): MIT License (see `LICENSE`).
- **Figures and data** (`figures/`): Creative Commons Attribution 4.0 (CC-BY-4.0).

If you use this material, please cite via the DOI (see `CITATION.cff` / the Zenodo record).
