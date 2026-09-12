# Task 4 — Feasibility Pilot

**Project:** Evaluating Data-Quality Filtering Strategies for Bengali Instruction Tuning
**Purpose:** the smallest run that could falsify the pipeline design in Tasks 2–3 before committing the full compute budget to it.

---

## Part A — Pilot Specification

**What this pilot tests:** not model quality — just whether the *data pipeline itself* (loading the V0 pool, running semantic deduplication, running language-quality/translationese filtering) behaves sanely on real TigerLLM-ecosystem data. No training happens in this pilot.

**Sample:** 2,000 examples, randomly sampled from the confirmed V0 pool (Bangla-Orca + Bangla-Alpaca, per Task 2's pool decision — pending the license/provenance checks listed there being resolved first, since we should not process data whose terms we haven't confirmed even for a pilot).

**Steps run on the sample:**
1. Basic cleaning (exact-duplicate removal, malformed-row stripping, Unicode normalization) — this defines the pilot's own V0.
2. Semantic deduplication (embedding generation + similarity clustering) — produces the pilot's V1.
3. Language-quality and translationese filtering (script-purity check, Unicode-malformation check, translationese heuristic) — produces the pilot's V2.

**Estimated wall-clock:** approximately 2 hours total on a single mid-range GPU (T4/A10-class) — embedding generation and clustering for 2,000 examples is the dominant cost; the language-quality filtering step is largely CPU-bound and fast at this scale.

**What "success" looks like (the pipeline is sound, proceed to full-scale runs):** retention rates after each step fall in a plausible range (roughly 70–95% surviving semantic dedup, roughly 60–90% of the remainder surviving language-quality filtering — wide ranges deliberately, since we have no prior data point for Bengali specifically), and a manual spot-check of ~30 discarded examples per step confirms they were actually low-quality/duplicate/malformed rather than good examples caught by a broken filter.

---

## Part B — Stop-and-Redesign Criteria

These are the specific results that mean the pipeline itself is broken, not just that the data is noisy — i.e., results that should stop scaling up and trigger a redesign of the filtering step rather than a decision about the data:

**If semantic deduplication removes more than 60% of the sample:** this would mean either the embedding model is not Bengali-capable (collapsing distinct sentences into near-identical vectors) or the similarity threshold is miscalibrated for Bengali text. Action: before touching the threshold, manually inspect 20 pairs flagged as near-duplicates — if they are not actually near-duplicates in meaning, the embedding model itself is the problem and needs to be swapped for one with verified Bengali performance, not just re-thresholded.

**If the language-quality filter flags more than 50% of the sample as malformed/low-quality:** this is far more likely to mean the Unicode normalizer or script-purity check is misconfigured (e.g., treating valid Bengali conjunct forms as malformed, or mis-handling legitimate Bengali digits/punctuation) than it is to mean half of BanglaLlama's data is genuinely broken — BanglaLlama's own authors reported the data as translation-imperfect, not majority-malformed. Action: immediate review of the normalizer against a small hand-verified set of known-good Bengali sentences before scaling up; do not proceed to full-pool filtering on an unverified normalizer.

**If either step's discarded sample, on manual spot-check, contains clearly good examples being wrongly removed:** even if the aggregate retention-rate numbers look plausible, this is a stop condition on its own — it means the filter is systematically biased in a way that could silently distort the final dataset (echoing AlpaGasus's documented category-collapse failure mode from Task 1). Action: identify what property the wrongly-removed examples share (length, dialect, topic, sentence structure) before re-running at scale.

**If retention rates differ drastically between Bangla-Orca and Bangla-Alpaca within the same pilot:** since both are built by the same translation pipeline (BanglaLlama, Google Cloud Translation API), a large divergence between the two sub-pools would suggest the filter is reacting to something incidental to one dataset (e.g., formatting differences from the OpenOrca vs. Alpaca source templates) rather than to genuine language quality. Action: check whether the divergence tracks a formatting artifact before concluding it reflects real quality differences.

**What does *not* trigger a stop:** moderate retention-rate differences between the two filtering steps, or an overall pool shrinkage that still leaves enough examples for the planned variant sizes (Task 3) — those are expected outcomes the experiment is designed to measure, not failures of the pipeline itself.

---

## Part C — Cost and Timeline Note

Given the submission deadline, this pilot should run immediately after Task 2's license/provenance checks are confirmed (do not run it on data whose terms haven't been verified, even for a 2,000-row test). At roughly 2 hours of GPU time and no API spend beyond whatever the language-quality filtering step requires (a rule-based/heuristic approach, not an LLM call, is recommended for the pilot specifically to keep it fast and free of API cost before the full LLM-judge step (C1) is even reached), this pilot fits inside a single working session and should be run before the full experimental matrix (Task 3) is scheduled.
