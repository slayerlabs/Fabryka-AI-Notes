# GoLLeM-v5: list of experiments

Every experiment of the GoLLeM-v5 campaign (25–27 September 2026), in the order it was run. For each one: the question, the design, the decision rule and when it was fixed, the result, and the verdict. Details, tables and sources are in the report (`../paper.pdf`); the section is given in the last column.

**How to read the numbers.**

- **eff** is the board formula `(ARC-Easy + BLiMP + WikiScore) / 3 × size multiplier`.
- **Board-test numbers** use the board protocol: ARC-Easy test (2,376 questions), BLiMP (67,000 pairs), WikiText-2 test byte perplexity.
- **Decisions** were taken on a separate selection axis. It uses ARC-Easy validation and train questions with contaminated ones removed (2,783 questions), one half of the BLiMP pairs, and WikiText-2 validation without 8 contaminated articles. The ARC-Easy and WikiText parts of this axis are disjoint from the board test. BLiMP has no separate split, so its selection half is also half of the board's BLiMP score.
- **Intervals** in brackets are 95% paired bootstrap intervals over items.
- **Noise:** two runs on the same data differ by about ±0.5 eff (rounds 3–4).
- **Rule times** are from our internal, timestamped team log; the rules were not published before the results.

**Terms used below.**

- **arcmix:** our training data mix (9.42B tokens); the final pool after the removal passes has 9.39B (`data_mix.yaml`).
- **64M / 128M / 32M:** model sizes in parameters (64M and 128M share one matrix between input and output embedding, counted once). **14×576** means 14 layers of width 576.
- **Step:** one update of 32 × 1,024 tokens; 760k steps are about 24.9B tokens.
- **Cosine:** the learning rate falls smoothly from its peak to 10% of it at the last step. **WSD** (warm-up, stable, decay) holds it constant and drops it only at the end.
- **Flagship (v1):** our first 64M model, the starting point of rounds 1–4 and S12.
- **Checkpoint:** the saved model every 20k steps; the board result is the last one, unless the tail-averaging rule (row 13) chooses the average.
- **Arm:** one variant in a comparison. **Seed** is the random seed; two seeds of the same arm show the noise.

| # | Experiment | Question and design | Rule (fixed before the result) | Result | Verdict | Report |
|---|---|---|---|---|---|---|
| 1 | Tail data, round 1 | 64M, branch at 320k, finish the cosine at 400k: arcmix (control) vs arcmix + FineWeb-Edu (fork-B). | Decision gate 25.09: Δeff over the 3 axes with a paired bootstrap; a lever only if Δ exceeds the training and evaluation floors. | Δeff −0.22 [−0.64, +0.21]; ARC −1.35 [−2.57, −0.13], BLiMP +0.91 [+0.70, +1.10], wiki worse by 0.027. | **Confounded.** The blend was built wrongly: it took only the first 1.15B arcmix tokens, used the wrong separator, and repeated data differently. The result is reported only as "build fork-B vs control". | §6.2 |
| 2 | Tail data, round 3 | Same branch: A = arcmix + FineWeb-Edu 45:55 (fixed build) vs B = arcmix with QA documents ×2. | Same gate. | A−B at 340k/360k/380k/400k: +0.46, −0.13, −0.02, +0.11; at 400k +0.11 [−0.30, +0.51]. FineWeb-Edu worsens wiki at every checkpoint. | **Tie.** The round-1 pattern came from the faulty build. | §6.2 |
| 3 | Tail data, round 4 | Anchor (QA ×1) and a second seed of B. | Same gate. | Seed pair B1338−B1337: +0.26, −0.34, −0.32, −0.28 eff. QA ×2 and FineWeb-Edu are within that noise; FineWeb-Edu worsens wiki 4 of 4 times. | **Tie.** Changing data in the last 20% of a cosine run moves eff by less than seed noise. | §6.2 |
| 4 | Scale ×2 | 128M (16×768) vs 64M (14×576), same recipe, 400k steps. | Compared on the board protocol at the final step. | 75.84 vs 75.81 (+0.03). | **Tie.** | §6.3 |
| 5 | Continuation from the flagship (S12) | Both arms restart from 64M step 400k with WSD (re-warm to 3e-4): A arcmix vs B arcmix + FineWeb-Edu. A guard on A's probe (wiki-val ≤ 2.3785, half-BLiMP ≥ 75.57) was written first. | Arm choice on the selection axis; a tie keeps the current arm. | Guard did not fire. Segment 1 (424k): B−A +0.14 [−0.78, +1.05], BLiMP +1.17. Segment 2 (444k): −0.24 [−0.68, +0.20]; the BLiMP gain did not repeat, the wiki loss did. | **Tie** in both segments; stopped. | §6.4 |
| 6 | Decay to 0 (H3) | Same continuation with the learning rate decayed to 0 instead of 6e-5. | Thresholds on all axes and eff. | ARC-E −1.94 [−3.71, −0.18], ARC-C −2.0, BLiMP +0.45, wiki −0.0038, Δeff −0.51. | **Rejected.** | §6.4 |
| 7 | FineWeb-Edu from scratch (Z1) | 32M, 4.9B tokens: Z = FineWeb-Edu (seeds 1337, 1338) vs C = arcmix, identical flags. | Written before any checkpoint: Z wins if ARC improves by > 2 with an interval excluding 0, eff improves, BLiMP loses ≤ 1.0 and wiki loses ≤ 0.05. Corpus choice for the finals: higher eff if the gap is > max(0.6; 2×seed gap). | Z−C at 150k: ARC +0.97 / +0.94 (intervals include 0), BLiMP −0.71 / −0.36, wiki +0.155 / +0.166 (worse), eff −0.28 / −0.19. Z̄−C = −0.24. | **Z does not win**; the finals keep arcmix. | §6.5 |
| 8 | Deeper shape (H4) | 32M deep-narrow (22×320×5) vs the mean of the Z seeds, same recipe. | Written before the run: BLiMP ≥ +1.0, ARC ≥ −1.0 and eff better. | At 150k: BLiMP +0.41, ARC −0.13, eff +0.11. At 120k H4 had been +1.7 BLiMP, but that did not hold. | **No win**; the finals keep the reference shapes. | §6.6 |
| 9 | Stricter educational filter (Z4) | 32M on FineWeb-Edu with int score ≥ 4 only. | For the dataset catalogue only (no effect on the finals). | Pool density of ARC-test hits is 3.7× Z (ARC-train questions 13.3×). The evaluation axis is being rebuilt without those questions. | **Pending.** | §6.7 |
| 10 | Weight averaging at a high learning rate | 64M, average of three checkpoints 20k apart vs the last one: S1 = 120/140/160k, S2 = 180/200/220k (learning rate 91% and 83% of peak). | 26.09 19:59Z: the average helps if its selection eff is higher and no axis falls beyond noise (ARC 1.0, half-BLiMP 0.35, wiki 0.002). | S1 74.010 vs 73.646 (+0.364); S2 74.489 vs 74.062 (+0.427); gain mostly from BLiMP and wiki. | **Helps.** | note *checkpoint-weight-averaging* |
| 11 | 64M checkpoint at 480k | Is the final 64M still improving at 480k? | 26.09 23:06Z, on the selection axis: A if eff480 > 73.88 (240k + 0.30). B and C were the fallback outcomes. | eff480 75.149 vs 73.578 at 240k; train loss 2.6821 → 2.6126. | **A**: training continues unchanged. | §7 |
| 12 | Second pass over the pool | Does the gap between wiki loss and train loss grow after the first pass over the pool ends (step 286,612)? | 27.09 04:28Z: supported if the mean gap after the boundary (300–380k) exceeds the mean before (240–280k) by > 0.010 nats/token; not supported below 0.005. | 64M +0.0067; 128M (replica) +0.0059. | **Inconclusive** in both sizes: a weak, consistent signal. | §7.4 |
| 13 | Tail averaging of the final runs | Average of 720k/740k/760k vs 760k (learning rate about 10% of peak). | 27.09 00:57Z: the average goes to the board only if its selection eff exceeds 760k by > 0.30 and no axis falls beyond noise. Board numbers are computed only after the choice. | 64M: selection 75.959 vs 75.764 (+0.195). Board after the choice: 760k **76.07**, average 76.20 (recorded only). 128M: pending (28.09). | **64M: 760k** (single checkpoint). | §7.3, note v1 |

**Final results (board protocol).**

- **64M:** 760k checkpoint, 76.07 eff (ARC-Easy 48.19, BLiMP 76.16, WikiText-2 byte perplexity 2.3474). That is +0.26 over v1, below our 0.6 decision floor and from a single seed.
- **128M:** training ends on 28 September.
