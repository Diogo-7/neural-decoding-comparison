# A Comparison of Neural Decoding Methods

Code and figures for the Integrating Project (PIC) **"A Comparison of Neural
Decoding Methods"**, comparing four neural decoders (SVM, LDA, Naive Bayes,
and an adapted Kalman filter) on Neuropixels electrophysiology recorded from
the Basal Ganglia (BG) and Cerebellum (CB) of five mice performing a lever
push/pull reaching task.

**Author:** Diogo Carvalho (ist109988) — Instituto Superior Técnico
**Supervisors:** Teresa Serradas Duarte, Simon Zamora — Paton Lab, Champalimaud Foundation

This repository hosts the full set of comparison figures and the analysis code,
referenced from the report under "Code and data availability".

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
code/        Analysis scripts
figures/     All comparison figures
```

### code/
| File | Purpose |
|------|---------|
| `decoder_mc_cv.py`        | Unified decoder implementations (SVM, LDA, NB, Kalman) with Monte-Carlo cross-validation |
| `s06_SVM_perSess.py`      | BG decoding pipeline (per session) |
| `s06_SVM_perSess_CB.py`   | CB decoding pipeline (per session) |
| `add_kalman.py`           | Adds the Kalman decoder results to the saved `.npz` files |
| `compare_all.py`          | Comprehensive comparison: per-session / per-animal / grand-average, BG vs CB, AUC, pointwise, latency, per-animal statistics |
| `per_animal_robustness.py`| Robustness analyses (neuron-count control, neuron-dropping, cross-session reliability) |
| `run_cross_temporal.py`   | Cross-temporal (temporal generalisation) decoding |

### figures/
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

- **Code** (`code/`): MIT License (see `LICENSE`).
- **Figures and data** (`figures/`): Creative Commons Attribution 4.0 (CC-BY-4.0).

If you use this material, please cite via the DOI (see `CITATION.cff` / the Zenodo record).
