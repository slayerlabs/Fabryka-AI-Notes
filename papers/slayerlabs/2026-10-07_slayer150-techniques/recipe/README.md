# recipe/ — files behind the numbers in this paper

All hashes are SHA-256, first 8 hex characters, computed from the files on 2026-10-07.

## Shared evaluation (Section 7, Appendix A)

| File | SHA-256 (8) |
|---|---|
| `glint_parity_eval.py` | d7a38ca0 |
| shared harness `glint_hf_eval` r3 | f6c38ef6 |
| shared harness `glint_hf_eval` r4 | 53ba1829 |
| `board_measure_r5.py` | 4bb0b96a |
| top-20 measurement note and table | 2c3997fe |

Per-model results (shared harness, run r6; values used in the table of Section 7):

| Model | ARC-Easy | BLiMP | Wiki byte_ppl | Result file SHA-256 (8) |
|---|---|---|---|---|
| GPT-X2-125M | 55.72 | 80.28 | 2.1985 | 1b22ec44 |
| Haidass-143M | 56.23 | 76.50 | 2.2946 | 9b09a376 |
| JugnuLM-110M-R2+ | 54.08 | 77.73 | 2.2560 | 2c2f3292 |

The remaining nine result files of run r6 are listed with their hashes in the measurement note (12/12 hashes verified).

## lm-eval 0.4.13 (Section 7, harness effect)

| File | SHA-256 (8) |
|---|---|
| GoLLeM-v5 128M `results.json` | 02dca486 |
| GoLLeM-v5 128M `run.log` (dataset revisions) | 01461451 |
| JugnuLM-110M-R2+ `results.json` | e0f92339 |
| JugnuLM-110M-R2+ `run.log` | 2a4d0ec5 |
| wrapper `lmeval_ours.py` | aadcf650 |
| test record | 28ba4da2 |

## Ablation verdicts (Section 3)

| File | SHA-256 (8) |
|---|---|
| `verify_verdicts.py` (second, independent implementation of all rules) | 45acc223 |
| noise file (σ for the 64M ablations) | 56fccb89 |

Per-arm checkpoints and summaries are listed in the decision log; the list will be attached in the next version.
