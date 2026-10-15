# Interpretability-of-EEG-Models-via-Sparse-Autoencoders

**Purpose:**  Session-T-only SAE discovery and frozen Session-E evaluation on pre-exported 96-D CIACNet embeddings; automatically export all *available* outputs and a strict coverage audit against the October 7, 2026 paper.

## What is integrated

- `src/sae_eeg/` — tested discovery and frozen replication scaffold from the prior deliverable.
- `legacy_sources/EEG_CIACNET_ONCE_SAE_ONLY_BCI2B_FULL_METHODS_V3.ipynb` — original 2b CIACNet + SAE workflow.
- `legacy_sources/CIACNet_SAE_BCI2AB_LOCAL_CACHE_LAYER_EXPERIMENTS.ipynb` — local 2a/2b original architectural, SAE and physiology routines.
- `legacy_sources/BCI2A_Frozen_SessionE_Feature_Replication.py` — original four-feature 2a replication; requires original frozen release and expected notebook state.
- `legacy_sources/BCI2B_Final8_Frozen_SessionE_Replication.py` — original eight-feature 2b replication; requires Block 6–8 state.
- `legacy_sources/old_release_b6_functional_exact_recovery_v5.py` — audited frozen original 2a restoration; requires archived release/checkpoints.
- `legacy_sources/RUN_ALL_BLOCKS_1_TO_10.py` — historical executor **requires ten separate original Block scripts not retrieved**. 
- `scripts/run_all.py` — single executable entry point; stops on failure.

## REAL DATA: one command for BCI-IV-2a and BCI-IV-2b (recommended)

This is the **real-data entry point**: it calls MOABB `BNCI2014_001` (IV-2a) and
`BNCI2014_004` (IV-2b), downloads their EEG, keeps LH/RH labels, and strictly
splits the original recording sessions (2a: 0/T,1/E; 2b: 0–2/T,3–4/E).
It then performs run-disjoint Session-T OOF subject competence, trains a
Session-T-only spectral-96 **BASELINE** encoder and SAE, freezes the
artifacts, evaluates held-out E and automatically exports available tables/figures.

```bash
python -m pip install -e '.[real,dev]'
python scripts/run_real.py --dataset both --out runs/real --data data/real --epochs 250
```

MOABB may download gigabytes; internet and dataset host availability are needed.
`--subjects 1 3` can exercise the real dataset pipeline quickly, but is **not**
a full nine-subject paper analysis. Dataset paths are local and never committed.

**Scientific status:** the download and session split use real EEG. The included
standalone fallback encoder uses log-bandpower + T-fitted
CIACNet: its feature IDs, classification, inference and figures are NOT the
published CIACNet/SAE results. Exact paper reproduction still requires the
original CIACNet fitted SAE with AuxK and balance,
and checkpoint/release artifacts. 

## Run (Windows, Linux, macOS)

```bash
python -m pip install -e '.[dev]'
python -m pytest -q
python -m sae_eeg.cli demo --output examples/data
python scripts/run_all.py --dataset iv2a --train examples/data/session_T.npz --eval examples/data/session_E.npz --out runs/demo --epochs 15
```

Example is **synthetic smoke testing only**, . With real exported data:

```bash
python scripts/run_all.py --dataset iv2a --train data/iv2a_T.npz --eval data/iv2a_E.npz --oof data/iv2a_CIACNet_OOF.npz --out runs/iv2a
python scripts/run_all.py --dataset iv2b --train data/iv2b_T.npz --eval data/iv2b_E.npz --oof data/iv2b_CIACNet_OOF.npz --out runs/iv2b
```

`*_T.npz` and `*_E.npz` contain trial-aligned `embeddings` `(N,96)`, `subject` `(N,)` and `y` `(N,)` coded `0=LH,1=RH`. OOF `.npz` contains `oof_pred` aligned with Session-T. Sessions for BCI-IV-2a: T=1, E=2; BCI-IV-2b: T=1–3, E=4–5. The same **Session-T-fitted, frozen CIACNet encoder** must generate both T and E arrays. Real paper results require the *original* full decoder and artifacts.

## Outputs

`runs/<dataset>/paper/tables/` stores generated CSV and LaTeX tables; `figures/` stores generated PNGs if supported; `COVERAGE_LEDGER.csv` marks every paper Table 1–14 and Figure 1–10. This report **does not pretend** missing original experiment outputs exist. Paper tables 7, 10, and 13/14 use provisional/different methods in the scaffold and are clearly labeled. True paper figures 7–10 require the original causal/physiology analysis, checkpoints and raw EEG. The historical scripts are **not automatically executed**, because they rely on missing runtime globals and archived releases; simply concatenating them would fail or alter the experimental protocol.

## Original paper feature identity, frozen per model/dataset

- 2a: F0003 RH, F0020 LH, F0070 LH, F0078 LH.
- 2b (October 7 paper and recovered script): F0029 RH, F0015 RH, F0041 LH, F0095 LH, F0024 RH, F0032 LH, F0088 LH, F0045 LH.

**Do not use feature indices from a newly trained SAE as if they were the original dictionary identities.** Exact replication requires the frozen original checkpoint. In the integrated scaffold, newly learned dictionary feature IDs are new experimental identities, not the paper features.


## GitHub

```bash
git init && git add . && git commit -m "Integrate EEG CIACNet SAE sources and reproducible reporting"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
git push -u origin main
```

