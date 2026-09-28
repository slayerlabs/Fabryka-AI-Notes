# GoLLeM-v5 recipe

This folder accompanies the GoLLeM-v5 report (`../paper.pdf`). It answers two questions a reader outside our team asks first:
**how do I rebuild the final models**, and **what did we try, and what worked**. The report explains why.

| file | what it is |
|---|---|
| `data_mix.yaml` | the training data of the final runs: sources with revisions, token counts, removal passes, licences, the sha256 of the final pool |
| `train_config_64m.yaml`, `train_config_128m.yaml` | the exact flags of the two final runs, the optimiser, the schedule and where the result checkpoint is |
| `experiments.md` | every experiment of the campaign: question, design, decision rule, result with intervals, verdict, report section |

## Rebuilding a final model

1. **Tokenizer.** `SlayerLab/gollem-v5-ckpts/tokenizer.json` (BPE, 12,288 tokens; sha256 in `data_mix.yaml`).
2. **Data.** Download the packaged ARC-MIX token stream named in `data_mix.yaml` (Hugging Face dataset, pinned commit)
   and run `SlayerLab/gollem-v5-ckpts/pool_build/rebuild_final_pool.py` with `pool_build/removed_documents.json`
   (document indices only). The script checks the source sha256, the document and token counts, and the sha256 of the
   result, which must be `ecfd0a40…` as in `data_mix.yaml`. We verified this rebuild end to end from the public files;
   it takes about ten minutes and less than 1 GB of RAM.
3. **Trainer.** `SlayerLab/gollem-v5-ckpts/train_gpt_ref_r6.py` (sha256 in the config files). It is published exactly
   as it ran; code comments are in Polish.
4. **Run.** Use the `command` in `train_config_*.yaml` with your own `<pool dir>` and `<run dir>`. On one RTX 5090 the
   64M model trains in about 31–34 hours and the 128M model in about 49–50 hours; the speed depended on the host CPU
   (22.2k and 24.6k steps per hour for 64M on our two hosts, 15.3–15.5k for 128M).
5. **Result.** The result is the **last** checkpoint (step 760,000), unless the tail-averaging rule (`experiments.md`,
   row 13) chooses the average of the last three checkpoints; for 64M it did not. Intermediate checkpoints are
   published for study, not for selection.

## Evaluating

Board numbers use the leaderboard's own protocol (Glint-1.3 `benchmark.py`): ARC-Easy test (2,376 questions),
BLiMP (67,000 pairs), WikiText-2 test byte perplexity; eff = (ARC-Easy + BLiMP + WikiScore) / 3 × size multiplier.
These are **our measurements with that protocol, not official scores**. Section 2 of the report describes the protocol
and the ways a harness can drift from it.

## Honest gaps

- The FineWeb-Edu revision used inside ARC-MIX was not recorded; rebuild from the packaged ARC-MIX file, not from
  FineWeb-Edu from scratch.
- Rule times in `experiments.md` come from our internal, timestamped team log; the rules were not published before
  the results.
- The final runs were trained once each (one seed). A difference below about 0.5 eff between two runs is within the
  noise we measured.
- The 128M final run is still training; its result will be added here when it finishes.

## Licences

Data licences are per source and listed in `data_mix.yaml` (OpenStax texts are CC BY 4.0 and require the attribution
files named there). The licence of this repository and of these files is not yet decided; until it is, all rights are
reserved by the authors.
