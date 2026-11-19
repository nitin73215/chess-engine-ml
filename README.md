# Chess Engine Evaluation Results

This repository contains documentation and aggregate result files from a multi-stage chess-engine project. It records work on a handcrafted classical evaluator, an XGBoost position evaluator, and a HalfKP NNUE evaluator integrated into a chess search engine.

**Scope:** This is a results and project-overview repository; implementation code is not included. The raw data, match PGNs, model checkpoints, opening books, engine binaries, and Syzygy tablebases are also excluded.

## Project overview

The project explored how different position evaluators perform within a conventional chess engine:

- **Classical evaluation:** manually designed material, piece-square, pawn-structure, mobility, king-safety, and endgame terms.
- **XGBoost evaluation:** 63 engineered position features with a material baseline and a learned residual, trained on Stockfish-labelled positions.
- **NNUE evaluation:** sparse HalfKP features, incremental accumulators, PyTorch training, and NumPy inference during search.
- **Search and protocol work:** legal move generation, alpha-beta/PVS, iterative deepening, quiescence, transposition tables, move ordering, time management, and UCI support.

## Results

### NNUE validation

The safe symmetry fine-tuning run reduced normal validation MAE from **95.705 cp** to **92.154 cp** at epoch 3 (−3.552 cp). This is a validation-set result, not a final independent test score. On the same run, mirrored MAE was **183.800 cp** and mirror-consistency error was **145.964 cp**, so mirrored-position behavior remains a significant limitation.

A separate v2 baseline report recorded **90.72 cp** normal MAE on a 2,000-position sample and **189.50 cp** mirrored MAE. That sample uses a different evaluation protocol and is not directly comparable with the safe fine-tuning run.

The epoch-level source metrics are in [`nnue_safe_symmetry_finetuning.csv`](reports/results/nnue_safe_symmetry_finetuning.csv).

### Preliminary engine match ladders

These adaptive Elo ladders use small match batches and are marked in progress in their source reports. The ranges below are rough competitive-region estimates, not measured final ratings. Protocols differ, so do not compare the three estimates as if they came from one controlled tournament.

| Engine | Reported estimate | Result evidence | Qualification |
|---|---:|---|---|
| Handmade classical | 1,750–1,850 | Against 1,750: 3 wins, 0 draws, 2 losses; against 1,800: 1 win, 0 draws, 4 losses | Five games per batch; report says test in progress |
| XGBoost evaluation | 1,550–1,650 | At 1,600: 7 wins, 2 draws, 11 losses (40% score) | Confirmation batch is 20 games; one earlier row completed 10 despite requesting 5 |
| Fixed NNUE | 1,700–1,800 | At 1,800: 5–0–1; at 1,900: 3–0–3; at 1,950: 1–0–5 | 18 games total; report says test in progress |

W–D–L is from the engine's perspective. The match CSVs are under `reports/results/`.

### XGBoost position evaluation

The selected strength-tuning model (`strength_04`) recorded validation MAE **162.047 cp**, RMSE **277.237 cp**, R² **0.6697**, sign accuracy **70.61%**, and median CPU single-position latency **639.6 μs**. Eight tuning trials are preserved in [`xgboost_strength_tuning.csv`](reports/results/xgboost_strength_tuning.csv). These are validation results; no held-out final test metric is claimed here.

### Search and correctness measurements

The project report records a fixed-depth-4 search benchmark moving from **12,393 NPS** (one baseline measurement) to **16,435 NPS** (median of three after changes), about **32.6%** in that local comparison. Laptop load varied, so this is a local benchmark rather than a general performance claim.

The NNUE engineering report records start-position perft counts through depth 5 of 20, 400, 8,902, 197,281, and 4,865,609; special-position perft and make/undo/hash checks; 7/7 tactical EPD cases; and 12 paired search A/B games without crashes or time losses. These are reported measurements and were not rerun for this results repository.

## Data and reproducibility

Training experiments reference a multi-gigabyte Lichess PGN and Stockfish-generated labels. The PGN, generated feature tables, model weights, Stockfish binaries, Syzygy files, opening books, and raw match PGNs are not included. The source folder does not contain a complete external-data license manifest. Obtain assets directly from their official sources under terms that permit your use.

This repository has no executable source, so it does not provide run or installation instructions for the engine. Exact dataset version, preprocessing configuration, package versions, and full tournament settings should be recorded before treating the reported figures as reproducible benchmarks.

## Result files

- `reports/results/handmade_engine_elo_batches.csv`
- `reports/results/xgboost_engine_elo_batches.csv`
- `reports/results/xgboost_strength_tuning.csv`
- `reports/results/nnue_safe_symmetry_finetuning.csv`
- `reports/results/nnue_fixed_engine_elo_batches.csv`

## Limitations

- Elo ranges are preliminary and have substantial uncertainty.
- Lower offline centipawn error does not guarantee stronger play; evaluator latency affects search depth.
- The NNUE mirrored-position error remains high, and separate hard-position retraining work is incomplete.
- Results are not a substitute for a larger, controlled tournament or a held-out evaluation.
- The engine implementation is intentionally absent from this repository; this is an overview and results portfolio, not a runnable project.
