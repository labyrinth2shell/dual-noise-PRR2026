# Reproduce Figure 3A–C — Dual-noise associative memory

This repository-ready notebook reproduces the **numerical simulations** underlying Figure 3A–C of:

T. Kurikawa and K. Kaneko, *Fast Neural Noise Stabilizes Associative Memory against Slow Synaptic Noise*.

**Main file:** [`Fig3_ABC_reproduction.ipynb`](Fig3_ABC_reproduction.ipynb)

The notebook is derived from the authors' original final-paper simulation notebook. It keeps the original neural–synaptic simulation, first-passage learning criterion, random-stream allocation, and deterministic recall procedure. The AMF theory, single-pattern calibration, and analytical plots not needed for Fig. 3A–C have been omitted.

## Getting started

1. Install Python 3.10+ and the dependencies listed in `requirements_fig3.txt`. PyTorch is optional; CUDA is strongly recommended for a complete paper run.
2. Open `Fig3_ABC_reproduction.ipynb` in Jupyter or Google Colab.
3. In the configuration cell, keep `RUN_MODE = "paper"`, `RUN_TRAINING = True`, and select `DEVICE = "auto"` (or `"cuda"` to require GPU).
4. Run the cells in order. Results appear in `fig3_reproduction_outputs/run_<config_hash>/`. For persistent storage in Colab, set `USE_GOOGLE_DRIVE = True`.
5. To **redraw without retraining**, leave all configuration parameters unchanged and set `RUN_TRAINING = False`.

A quick end-to-end software test is available with `RUN_MODE = "smoke"`. **Smoke-test output is not representative of the paper results.**

## Paper simulation protocol

| Parameter | Value |
| --- | --- |
| Number of neurons, `N` | 200 |
| Patterns per sequentially trained network | 20 |
| `beta`, `alpha`, `tauJ` | 10, 0.001, 10 |
| Euler–Maruyama step | 0.01 |
| Learning stopping rule | First passage of instantaneous overlap to 0.99 |
| Recall simulations | 5 independent starts per trained network and memory |
| Recall overlap average | t = 50–100, with fixed weights and no training noise |
| Global seed | 1729 |
| Baseline noise sweep | 0.01; 0.05, 0.10, ..., 1.00 |
| Fig. 3A conditions | Ds=0.01; Dn=0.01, 0.25, 0.5, 1.0 |
| Fig. 3A independent networks per condition | 30 |
| Other conditions | 10 networks per condition |
| Total | 80 unique (Dn, Ds) pairs; 880 trained networks |

The paper's "capacity" is the average recall overlap across all 20 stored memories, not the count of memories recalled above a threshold. Error bars are **SEM over independent trained networks**, rather than SEM over recall starts.

## Saved outputs

- `figures/Fig3A.pdf`, `Fig3B.pdf`, `Fig3C.pdf` and `Fig3_ABC.pdf` (also PNG).
- `tables/fig3_recall_trials.csv`: one record per network × memory × recall initial state.
- `tables/fig3_network_memory.csv`: mean recall for each network and memory.
- `tables/fig3_network_capacity.csv`: mean recall for each trained network.
- `tables/fig3A_summary.csv`: point estimates and SEM for Fig. 3A.
- `tables/fig3BC_summary.csv`: point estimates and SEM for Fig. 3B/C.
- `raw/`: per-network compressed NPZ checkpoints containing recall and training-step summaries.
- `run_config.json`: numerical settings, backend, and environment metadata.

The full paper run is computationally demanding. To avoid accidental mixing of different numerical experiments, changing numerical parameters or backend creates a separate run directory. A missing or corrupted checkpoint causes an explicit error; unsuccessful training runs are not silently excluded.

## Validation and limitations

The notebook has been validated on a short *smoke-test* execution through simulation, CSV aggregation, checkpoint reload, and figure export. The complete 880-network publication run has **not** been re-executed as part of preparing the public notebook. The results can differ slightly between NumPy and CUDA/PyTorch due to their different random-number generators and floating-point operations. To recover the actual publication figures, execute the full `paper` mode using the recorded settings (or use the already completed checkpoints from a run using those settings).

When using or redistributing this notebook, cite the corresponding research article and verify its final bibliographic details from the published version.
