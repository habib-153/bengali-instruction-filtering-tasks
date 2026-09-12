# Task 3 — Experimental Comparison Design

**Project:** Evaluating Data-Quality Filtering Strategies for Bengali Instruction Tuning
**Builds on:** Task 2's recommended strategies (semantic deduplication → language-quality/translationese filtering → LLM-as-judge quality scoring) and PROJECT_BRIEF §5–6.

---

## Part A — Variant Matrix

**Comparison unit decided:** equal example count. Justification — our filtering strategies each have a different, unpredictable retention rate (we don't yet know what fraction semantic dedup or translationese filtering will remove), so fixing token count instead would silently let variants differ in how many distinct examples they see, and fixing compute would conflate filtering-strategy effects with training-efficiency effects. Equal example count is the cleanest way to isolate "does this selection of data produce a better model," which is the question this thesis actually asks. This choice should be revisited only if the pilot (Task 4) shows retention rates so extreme that equal-example-count becomes impractical (e.g., a variant would need to reuse examples to hit the target count).

| Run | Description | Source pool | Size relative to V0 | Purpose |
|---|---|---|---|---|
| **V0 — Baseline** | Basic cleaning only: exact-duplicate removal, empty/malformed row stripping, Unicode normalization to a single canonical form. No quality judgment applied. | Bangla-Orca + Bangla-Alpaca (BanglaLlama), per Task 2's V0 decision | 100% (full cleaned pool) | The floor every variant is measured against. |
| **V1** | V0 + semantic deduplication (near-duplicate removal via embedding-similarity clustering) | V0 output | Retained size (≤100%, exact % determined empirically) | Tests whether removing embedding-near-duplicate examples alone improves downstream performance. |
| **R1** | Random subset of V0, same size as V1's retained count | V0 | Matched to V1 | Isolates whether V1's effect (if any) is a quality effect or just a smaller-pool-size effect. |
| **V2** | V1 + language-quality and translationese filtering (Unicode/script-purity cleanup + translationese/MT-artifact detection) | V1 output | Retained size (≤ V1's size) | Tests the marginal effect of Bengali-specific language-quality filtering on top of deduplication. |
| **R2** | Random subset of V0, same size as V2's retained count | V0 | Matched to V2 | Same isolation logic as R1, applied at V2's size. |
| **V3** | V2 + LLM-as-judge instruction–response quality scoring | V2 output | Retained size (≤ V2's size) | Tests the marginal effect of quality-score filtering on top of the first two interventions. |
| **R3** | Random subset of V0, same size as V3's retained count | V0 | Matched to V3 | Same isolation logic, applied at V3's size — this is the comparison that most directly answers RQ1. |
| **Full-data control** | V0 at its full, unfiltered size (same run as V0 above, reported again here for clarity) | V0 | 100% | Measures what each variant gives up in raw volume — answers "is filtering worth the data it throws away." |

**Held constant across every run (V0, V1–V3, R1–R3):** base model and checkpoint (base, not instruction-tuned — see PROJECT_BRIEF §8), LoRA/QLoRA rank and target modules, learning rate and schedule, number of epochs, maximum sequence length, prompt template, and decoding parameters at evaluation time (temperature, top-p, max new tokens).

**Seeds:** 3 seeds per run if compute allows (7 runs × 3 seeds = 21 training runs total, at equal example count so training cost per run is roughly comparable). If the Task 4 pilot shows this is not affordable within the compute budget, drop to 1 seed per run — but this must be stated explicitly as a limitation, and no significance claims should be made on 1-seed results.

**Stacking, not independent testing:** V1→V2→V3 are deliberately stacked (each variant is built on the previous one's output) rather than three independent single-strategy runs. This directly targets RQ3 (do interventions compose) — the marginal gain from V1→V2 and V2→V3 is itself the measurement of composability, which Task 1's gap synthesis identified as the weakest-evidenced sub-question in the existing literature.

---

## Part B — Threat Audit

**Contamination.** Our evaluation benchmarks (Task 1's "Too late to train" 7-task compilation, and "Evaluating LLMs' Multilingual Capabilities for Bengali") are themselves built partly from machine-translated English sources — the same class of process (Google Translate / GPT-4o-mini translation) used to build parts of our training pool (Bangla-Orca, Bangla-Alpaca). Before finalizing the evaluation set, we must check for direct overlap between benchmark source sentences and training-pool source sentences (e.g., did both draw from overlapping OpenOrca/Alpaca English source text before translation). If either benchmark's underlying English source overlaps with OpenOrca or Alpaca, that benchmark is contaminated for our purposes and must be dropped or replaced.

**Circular filter–judge bias.** The C1 quality-scoring step (Task 2, Part B) and any LLM-judge used for open-ended evaluation win-rate scoring (Part C below) must not be the same model family. If both the training-data filter and the evaluation judge are, say, GPT-4o-based, then a variant that happens to produce GPT-4o-flavored text could win both the filtering step and the evaluation step for reasons that have nothing to do with actual Bengali quality — the model would just be agreeing with itself. Decision to record before running anything: name the filter-judge model and the eval-judge model now, confirm they are from different providers/families, and if that's not possible within budget, state the circularity explicitly as a limitation rather than discovering it after results are in.

**Judge reliability in Bengali.** Every LLM-judge step in our pipeline (C1 filtering, and any judge-based evaluation) is weaker and less calibrated in Bengali than in English — this is the same risk PROJECT_BRIEF §6 already flags, and it is not fully separable from the effect we're trying to measure: if V3 (quality-filtered) underperforms V2, we cannot immediately tell whether the underlying data got worse or the judge simply misjudged Bengali quality. Mitigation: report judge agreement with the small human-annotated sample (PROJECT_BRIEF's §Open Decisions item on annotator sample size) as a calibration check before trusting judge-based results at face value.

**Translationese/quality confound between training and evaluation.** If the language-quality filter (V2) is specifically tuned to catch translationese, and part of our evaluation benchmark is itself translationese (machine-translated benchmark questions), V2 and V3 models could score worse on the benchmark simply because they were never trained on the kind of translated phrasing the benchmark uses — not because the underlying model is worse. This needs to be reported as a possible explanation if V2/V3 underperform on translated-benchmark tasks specifically, distinct from a genuine capability loss.

**Tokenizer fertility interacting with equal-example-count comparison.** From "Evaluating LLMs' Multilingual Capabilities for Bengali" (Task 1), Bengali tokenization fertility varies significantly by model family. If filtering systematically changes the average sequence length of retained examples (e.g., translationese filtering disproportionately removes long, awkward machine-translated responses), then two variants with the same *example count* may not have the same *token count* — which is exactly the confound our equal-example-count decision (Part A) was meant to control for, just showing up one level down. Mitigation: report token counts alongside example counts for every variant (PROJECT_BRIEF §5 already requires this), and flag any variant where token count diverges sharply from V0's at the same example count.

---

## Part C — Metric Definitions

**Automatic / data-level metrics (measured on the training pool itself, before any model is trained):**
- **Retention rate** — retained examples ÷ starting pool size, reported per variant, per PROJECT_BRIEF §5's reporting requirement.
- **Script purity ratio** — fraction of characters in each retained response that fall within the Bengali Unicode block, out of all alphabetic characters; reported as a pool-level average and distribution, not just a mean.
- **Malformed-Unicode rate** — fraction of retained examples containing unresolved combining-mark or ZWJ/ZWNJ errors after normalization, used as a sanity check that the language-quality filter actually did its job.
- **Token fertility** — average tokens per Bengali word for the chosen base model's tokenizer, measured on each variant's retained pool, to check whether filtering shifts the pool toward or away from tokenizer-friendly text.
- **Exact and near-duplicate rate** — measured on V0 (to justify why A3 was worth doing) and on V1 onward (to confirm A3 actually reduced it), using the same similarity threshold used to build V1.

**Model output / generation-quality sanity checks (measured on model outputs at evaluation time, not training data):**
- **Output script purity** — same definition as above, applied to generated responses, to catch models that regress into code-switched or malformed output.
- **Response length distribution** — mean and spread of generated response length per variant, to catch degenerate over-short or over-long behavior introduced by a variant.
- **Degenerate-repetition rate** — fraction of generated responses containing detectable repeated n-grams above a fixed threshold.
- **Refusal rate** — fraction of generated responses that are non-answers/refusals on prompts that should have a substantive answer, to catch a variant that inadvertently trained the model toward over-caution.

**Downstream performance metrics:**
- **Knowledge/reasoning benchmark accuracy** — Pass@1 or exact-match accuracy (metric depends on final benchmark choice) on the Bengali benchmark(s) confirmed contamination-free in Part B.
- **Natural-language-understanding accuracy** — classification/inference accuracy on a stable Bengali NLU task (e.g., XNLI-bn or BanglaParaphrase, contamination-checked), included specifically because it gives a lower-variance signal than open-ended generation, per PROJECT_BRIEF §6.
- **Open-ended instruction-following win rate** — percentage of held-out Bengali prompts where the variant's response is preferred over the V0 baseline's response by the evaluation judge, with response order swapped across two runs per pair and averaged to control position bias. Report the judge model name, the exact judge prompt, and the swap/averaging procedure alongside the number, per PROJECT_BRIEF §6's recording requirement.

**Reporting format per variant (every run, every seed):** starting pool size, retained size, retention rate, token count, wall-clock time, every metric above, and seed-to-seed variance where 3 seeds were run. This matches PROJECT_BRIEF §5's reporting requirement exactly and should be the literal column structure of the results table in the final write-up.
