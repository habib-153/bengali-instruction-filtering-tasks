# Task 1 — Literature Screening, Extraction, and Research Gap

**Project:** Evaluating Data-Quality Filtering Strategies for Bengali Instruction Tuning
**Source:** NeuralSight Assignment 2 — Paper Tracker (14 papers currently in the knowledge base)
**Inclusion criteria used:** PROJECT_BRIEF.md, Section 3

---

## Part A — Screening

Screening decision for every paper currently logged in the tracker, checked against the include/exclude/unsure rules in PROJECT_BRIEF.md §3 (a data-selection or filtering method for instruction tuning; a quality-vs-quantity study; low-resource/Indic instruction tuning; Bengali resources or benchmarks; deduplication; translationese/MT artifacts; or a usable Bengali/multilingual benchmark).

| # | Paper | Logged by | Link | Decision | Reason |
|---|---|---|---|---|---|
| 1 | BanglaLlama: LLaMA for Bangla Language | Mukti | arxiv.org/abs/2410.21200 | **Include** | Releases the Bangla-Orca / Bangla-Alpaca instruction sets with no post-hoc quality filtering — this is the direct "unfiltered baseline" our thesis argues against. |
| 2 | Superfiltering: Weak-to-Strong Data Filtering for Fast Instruction-Tuning | Mukti | aclanthology.org/2024.acl-long.769 | **Include** | Proposes a filtering method for instruction-tuning data (weak-model-scores-strong-model-data); falls under Strategy family C (instruction–response quality filtering). |
| 3 | CoachLM: Automatic Instruction Revisions Improve the Data Quality in LLM Instruction Tuning | Hasan | ieeexplore.ieee.org/document/10597991 | **Include** | Improves instruction data quality via automatic revision rather than pure filtering — relevant comparison point for our C-family strategies, and for what "quality improvement" can mean beyond keep/discard. |
| 4 | Data Diversity Matters for Robust Instruction Tuning | Hasan | aclanthology.org/2024.findings-emnlp.195 | **Include** | Directly studies data diversity's effect on instruction-tuning robustness — grounds Strategy family D (diversity/coverage) and the composability question (RQ3). |
| 5 | An Instruction-Response Perspective on Large Language Models in Information Retrieval Tasks | Hasan | dl.acm.org/10.1145/3726302.3730346 | **Unsure** | Title suggests a downstream IR application of instruction-response formatting rather than a data-selection or quality method. PDF is accessible but not yet read in depth — needs a first pass before a final call; likely candidate for exclusion under "task-specific fine-tuning with no instruction-following component" if it turns out to be purely about IR ranking. |
| 6 | INSTRUCTEVAL: Towards Holistic Evaluation of Instruction-Tuned Large Language Models | Hasan | aclanthology.org/2024.scalellm-1.4 | **Unsure** | An evaluation-framework paper, not a filtering method, and (based on the title) English-focused. Per the brief's rule for "English-only but the method is clearly transferable," keep as Unsure — potentially useful for shaping our automatic-evaluation protocol in Section 6, not as a filtering strategy source. |
| 7 | Too late to train, too early to use? A study on necessity and viability of low-resource Bengali LLMs | Habibur Rahman | aclanthology.org/2025.coling-main.79 | **Include** | Directly addresses whether dedicated low-resource Bengali LLMs are still needed given cross-lingual transfer — core evidence for RQ2 (do English-developed interventions transfer to Bengali) and for motivating the thesis itself. |
| 8 | Evaluating LLMs' Multilingual Capabilities for Bengali: Benchmark Creation and Performance Analysis | Habibur Rahman | arxiv.org/abs/2507.23248 | **Include** | Builds a Bengali evaluation benchmark and documents a tokenization-efficiency-vs-accuracy relationship — directly usable in Section 6 (evaluation plan) and Section 8 (tokenizer fertility check for base-model selection). |
| 9 | TituLLMs: A Family of Bangla LLMs with Comprehensive Benchmarking | Habibur Rahman | aclanthology.org/2025.findings-acl.1279 | **Unsure** | PDF access not yet confirmed in the tracker (no accessibility status logged). Title indicates it is a Bengali-LLM resource/benchmarking paper, which would qualify it for Include once access and content are confirmed. |
| 10 | BanglaLlama: LLaMA for Bangla Language (published venue version) | Habibur Rahman | aclanthology.org/2026.loreslm-1.7 | **Unsure — duplicate/venue-version of #1** | Same paper as #1, logged twice under different links (arXiv preprint vs. LoResLM 2026 proceedings version). PDF access not yet confirmed for this version. Action: confirm whether this is the camera-ready version of #1 and merge the two tracker rows into one before the spreadsheet is finalized, citing the published version. |
| 11 | TigerLLM: A Family of Bangla Large Language Models | Shadman Kabir | arxiv.org/abs/2503.10995 | **Include** | Our primary dataset source (Bangla-TextBook + Bangla-Instruct) and the paper whose multi-criteria filtering pipeline we are directly extending with random-subset controls. |
| 12 | What Makes Good Data for Alignment? A Comprehensive Study of Automatic Data Selection in Instruction Tuning (DEITA) | Shadman Kabir | arxiv.org/pdf/2312.15685 | **Include** | Foundational complexity–quality–diversity selection framework; structurally underlies our A/B/C/D taxonomy and is the closest English-language analogue to what we are testing in Bengali. |
| 13 | AlpaGasus: Training a Better Alpaca with Fewer Data | Shadman Kabir | arxiv.org/pdf/2307.08701 | **Include** | Canonical LLM-as-judge single-score filtering method (Strategy C1) with a documented failure mode (skill-category imbalance after filtering) directly relevant to our threat audit in Task 3. |
| 14 | Large-Scale Data Selection for Instruction Tuning | Shadman Kabir | arxiv.org/pdf/2503.01807 | **Include** | Title indicates a direct data-selection-for-SFT method at scale; PDF accessible but not yet read in depth — flagged for full extraction before the spreadsheet is finalized. |
| 15 | A Survey on Data Selection for LLM Instruction Tuning | Shadman Kabir | arxiv.org/pdf/2402.05123 | **Include** | Survey covering the method landscape our taxonomy (PROJECT_BRIEF §4) is built on; used as the structural backbone for the gap synthesis below, not as a primary empirical source. |

**Tally:** 10 Include, 4 Unsure (#5, #6, #9, #10), 0 Exclude. Row #10 is a duplicate of row #1 and should be merged once confirmed — so the working count of distinct papers is 14, matching the knowledge base.

**Action items before the spreadsheet is final:** resolve the 4 Unsure rows (confirm PDF access for #9 and #10, do a first read of #5 and #6 to decide relevance) and complete full extraction on #2, #3, #4, and #14, which are currently Include but only partially read (see Part B).

---

## Part B — Extraction

Extraction cards for the 10 Include papers, grouped in batches of 4 as requested, formatted to paste as rows into the Literature Review Spreadsheet. Columns follow the tracker's existing structure: Problem/Motivation, Data Used, Method/Pipeline, Baseline(s), Evaluation Approach, Results, Limitations, Reproducibility.

Seven of these ten already have a full read logged in the tracker (BanglaLlama, Too Late to Train, Evaluating LLMs' Multilingual Capabilities, TigerLLM, DEITA, AlpaGasus, and the Survey). The other three (Superfiltering, CoachLM, Data Diversity Matters) and one more (Large-Scale Data Selection) only have partial or no extraction logged — those cards are marked **Partial** and should not be treated as final until someone on the team does a full read.

### Batch 1

**1. BanglaLlama: LLaMA for Bangla Language** (Mukti, 2024/2026 — arxiv.org/abs/2410.21200)
- *Problem:* Bengali lacks reproducible, openly documented instruction-tuning resources built on LLaMA.
- *Data used:* Bangla-Orca (172k samples, translated from OpenOrca, code/math excluded), Bangla-Alpaca (52k pairs, translated from Stanford Alpaca), CulturaX Bengali subset for pretraining. Translated via Google Cloud Translation API (~$10k cost) with manual injection of Bangladeshi cultural references.
- *Method/pipeline:* Machine translation of existing English instruction corpora, no post-translation quality filtering — only random human spot-checks.
- *Baseline(s):* Prior LLaMA 2/3/3.1/3.2 base checkpoints (1B–8B), compared as base vs. instruct variants.
- *Evaluation approach:* Not detailed in the extracted notes beyond model release; treat as a resource paper for this axis.
- *Results:* Five base/instruct model variants released; no ablation of filtered vs. unfiltered data.
- *Limitations:* No data-quality filtering step; translation-loss artifacts and literal-translation errors acknowledged by the authors but not measured or corrected.
- *Reproducibility:* Code and model weights on Hugging Face (hf.co/collections/BanglaLLM/banglallama).
- **Why it matters here:** This is the direct unfiltered baseline our thesis is positioned against — 224k noisy translated samples with no filtering is exactly the V0-style pool we are testing interventions on.

**2. Superfiltering: Weak-to-Strong Data Filtering for Fast Instruction-Tuning** (ACL 2024 — aclanthology.org/2024.acl-long.769) — **Partial**
- *What's logged:* PDF accessible; no extraction fields filled in yet in the tracker.
- *What we know from the title/venue:* Proposes using a small ("weak") model's scoring signal to filter data for a larger ("strong") model's instruction tuning, aimed at making filtering fast/cheap.
- *Relevance if confirmed:* Would sit in Strategy family C, and matters specifically because it targets filtering *cost*, which is one of our practical constraints (§9 compute budget in PROJECT_BRIEF).
- **Action:** Needs a full read before it can support any claim in the gap or strategy sections.

**3. CoachLM: Automatic Instruction Revisions Improve the Data Quality in LLM Instruction Tuning** (IEEE — ieeexplore.ieee.org/document/10597991) — **Partial**
- *What's logged:* PDF accessible; dataset used = Alpaca-52k; no other fields filled in.
- *What we know from the title:* Revises (rewrites) low-quality instructions/responses automatically rather than discarding them — a "repair" strategy, distinct from keep/discard filtering.
- *Relevance if confirmed:* Offers an alternative framing to our filtering-only design — worth naming explicitly as future work / out-of-scope in our limitations, since our variant matrix (Task 3) only filters, it does not revise.
- **Action:** Needs a full read to confirm the revision mechanism and whether it reports a quality delta comparable to filtering-only methods.

**4. Data Diversity Matters for Robust Instruction Tuning** (EMNLP Findings 2024 — aclanthology.org/2024.findings-emnlp.195) — **Partial**
- *What's logged:* PDF accessible; datasets used = Alpaca-52k, Dolly-15k (small-scale) and UltraChat-1.3M, LMSYS-Chat-1M (large-scale); no other fields filled in.
- *What we know from the title/dataset pairing:* Compares small curated sets against much larger chat corpora to isolate the effect of diversity on robustness.
- *Relevance if confirmed:* Direct evidence source for Strategy family D (diversity/coverage) and for RQ3 (do interventions compose, or does diversity alone explain gains attributed to other filters).
- **Action:** Needs a full read to extract the actual diversity metric used and whether it is transferable to a Bengali embedding space.

### Batch 2

**5. Too late to train, too early to use? A study on necessity and viability of low-resource Bengali LLMs** (COLING 2025 — aclanthology.org/2025.coling-main.79)
- *Problem:* Whether it is still worth building dedicated low-resource Bengali LLMs given how well English-oriented LLMs now transfer cross-lingually.
- *Data used:* A compiled benchmark of 7 Bengali NLU/NLG tasks — BanglaNMT, XLSum, CrossSum, BanglaParaphrase, SQuAD-bn/BQA, BanglaRQA, BEnQA, XNLI-bn.
- *Method/pipeline:* Compares state-of-the-art open-weight and closed-source LLMs (e.g., LLaMA-3, GPT-4) against traditional fine-tuned encoder-decoder baselines on these tasks.
- *Baseline(s):* Fine-tuned encoder-decoder models (the pre-LLM standard for these tasks).
- *Evaluation approach:* Reasoning capability, Bengali script generation accuracy, and computational cost from tokenization.
- *Results:* LLMs are strong at reasoning but inconsistent at generating correct Bengali script; inefficient tokenization raises both cost and error rate.
- *Limitations:* The field still lacks high-quality Bengali pretraining and instruction-tuning data; widely used datasets carry machine-translation bias.
- *Reproducibility:* No code/data repository mentioned.
- **Why it matters here:** Directly supports RQ2 — it shows English-LLM transfer is not a substitute for good Bengali-specific data, which is the premise our whole thesis rests on.

**6. Evaluating LLMs' Multilingual Capabilities for Bengali: Benchmark Creation and Performance Analysis** (arxiv.org/abs/2507.23248)
- *Problem:* No standardized Bengali evaluation benchmark exists, and poor tokenization quietly degrades model performance.
- *Data used:* 8 major English NLP benchmarks (HellaSwag, Winogrande, CommonsenseQA, BoolQ, OpenBookQA, and others spanning commonsense/science/math/multidomain) machine-translated into Bengali using GPT-4o-mini, chosen over Google Translate/Azure after a blind human review. Total translation cost ~$200.
- *Method/pipeline:* Automated LLM translation → regex-based cleanup of malformed JSON outputs → benchmarking of 10 open-source LLMs (including Mistral and DeepSeek families).
- *Baseline(s):* The same 10 LLMs' performance on the original English benchmark versions, to isolate the multilingual performance gap.
- *Evaluation approach:* Inference accuracy on translated questions, plus correlation analysis between tokenization granularity (bytes/token, tokens/word) and accuracy.
- *Results:* Bengali performance is consistently worse than English across models; Mistral-family models degrade most, DeepSeek is most stable; higher tokens-per-word correlates with lower accuracy.
- *Limitations:* Benchmark data is itself machine-translated by an LLM, so it can carry translationese and the source model's alignment biases rather than natural Bengali.
- *Reproducibility:* Datasets on Hugging Face; translation and evaluation code on GitHub.
- **Why it matters here:** Gives us a concrete, reusable finding — tokenizer fertility predicts performance loss — that feeds directly into base-model selection (PROJECT_BRIEF §8) and into our automatic evaluation metrics (Task 3).

### Batch 3

**7. TigerLLM: A Family of Bangla Large Language Models** (arxiv.org/abs/2503.10995)
- *Problem:* Existing Bangla LLMs suffer from poor reproducibility, low data quality, and heavy reliance on translated synthetic data, despite Bangla having ~237M native speakers.
- *Data used:* Bangla-TextBook corpus (~9.9M tokens / 697,903 sentences from 163 National Curriculum and Textbook Board textbooks, Grades 6–12) for pretraining; Bangla-Instruct (100K instruction-response pairs, expanded from 500 volunteer-written seed tasks via GPT-4/Claude-3.5-Sonnet self-instruct with multi-stage filtering) for fine-tuning.
- *Method/pipeline:* Continual pretraining of LLaMA-3.2-1B and Gemma-2-9B on Bangla-TextBook, then full fine-tuning (no LoRA, Flash Attention) on Bangla-Instruct.
- *Baseline(s):* Prior open Bangla LLMs (Titu-Gemma, Titu-LLaMA, Bangla-LLaMA, BongLLama) and proprietary models (GPT-3.5, GPT-4o-mini).
- *Evaluation approach:* Pass@1 accuracy on Bangla benchmarks (MMLU-bn, PangBench-bn, BanglaQuaD, mHumanEval-bn, BEnQA, BanglaRQA); no significance testing or user study.
- *Results:* TigerLLM-9B leads across all six benchmarks, beating GPT-3.5 and mostly beating GPT-4o-mini; TigerLLM-1B outperforms all prior open Bangla LLMs despite its size.
- *Limitations:* Textbook corpus restricted to Grade 6–12 academic register; Bangla-Instruct has no unanswerable-question category; model scale capped at 9B; regional/dialectal variation underrepresented. **No ablation comparing filtered vs. unfiltered Bangla-Instruct, and no random-subset control** — the paper treats its "native + curated" pipeline as inherently high quality without testing that claim empirically.
- *Reproducibility:* Both datasets and both models open-sourced on Hugging Face; pipeline code on GitHub (github.com/mraihan-gmu/TigerLLM).
- **Why it matters here:** This is our primary dataset and our closest related work simultaneously — the missing ablation identified above is the exact gap this thesis fills.

**8. What Makes Good Data for Alignment? A Comprehensive Study of Automatic Data Selection in Instruction Tuning (DEITA)** (arxiv.org/pdf/2312.15685)
- *Problem:* No principled method existed for automatically selecting instruction-tuning data; prior work showed small curated sets can align models well without explaining *why* or *how* to select systematically.
- *Data used:* Two pools built from existing public instruction sets — a "high-quality" pool (ShareGPT, UltraChat, WizardLM, ~300K) and a "lower-quality/redundant" pool (Alpaca, Dolly, OpenAssistant, FLAN 2022, ~100K).
- *Method/pipeline:* Three signals — complexity and quality (each scored by evolving samples through iterations and training a LLaMA-7B scorer on ChatGPT-ranked seed data) and diversity (embedding distance to the nearest already-selected sample) — combined into one score, then greedily selected under a diversity threshold.
- *Baseline(s):* Random selection, instruction length, perplexity, instruction-following difficulty, InsTag complexity/diversity, and full models like LIMA, AlpaGasus, Vicuna, WizardLM, Zephyr, Tülu 2.
- *Evaluation approach:* MT-Bench, AlpacaEval, the Open LLM Leaderboard, plus a 4-annotator human study on 100 examples; no formal significance testing reported.
- *Results:* A 6K-sample DEITA-selected set matches or beats Zephyr-beta (trained on 260K samples, ~30x more data) on MT-Bench/AlpacaEval.
- *Limitations:* Entirely reliant on ChatGPT/GPT-4 for evolution, scoring, and judging (untested outside English); narrow quality definition (no factuality/safety check); some design choices unablated; judge circularity (GPT judging GPT-trained outputs); small human panel.
- *Reproducibility:* Model checkpoints and selected datasets released on GitHub (github.com/hkust-nlp/deita).
- **Why it matters here:** Structurally, this paper *is* our taxonomy — complexity/quality/diversity map directly onto our C and D strategy families. Its untested-outside-English limitation is exactly what RQ2 asks about.

### Batch 4

**9. AlpaGasus: Training a Better Alpaca with Fewer Data** (arxiv.org/pdf/2307.08701)
- *Problem:* Widely used instruction datasets (e.g., Alpaca-52k) contain low-quality, incorrect, or irrelevant responses that mislead instruction tuning and waste compute.
- *Data used:* Alpaca (52,002 pairs), Dolly-15k, GPT4LLM (Alpaca instructions with GPT-4 responses) as training pools; four human-curated test sets (Self-Instruct, Vicuna, WizardLM, Koala) for evaluation.
- *Method/pipeline:* ChatGPT (or Claude-2) scores each (instruction, input, response) triplet 0–5 for accuracy on a single dimension; triplets scoring ≥4.5 are kept (~9K of 52K); LLaMA-1/2 fine-tuned on the filtered subset with Alpaca's original hyperparameters.
- *Baseline(s):* Full unfiltered Alpaca-52k, and a size-matched random 9K subset (Alpaca-9k-random) — this is the closest existing example of an R-control in the literature we reviewed.
- *Evaluation approach:* GPT-4-as-judge pairwise win/tie/lose across four test sets (order-swapped to control position bias), a 3-rater human study (160 prompts), and standard benchmarks (MMLU, DROP, HumanEval, BBH).
- *Results:* The 9K-filtered model beats the 52K-unfiltered model on all four test sets; 13B model reaches >90% of text-davinci-003 capability; training cost drops from $27–225 to $5–41 depending on model size.
- *Limitations:* Single-dimension scoring with no category-balance control disproportionately strips coding examples (~88% filtered vs. ~82% average); relies entirely on proprietary LLM judges; the 4.5 score threshold is chosen heuristically, not theoretically justified.
- *Reproducibility:* Code, the filtered 9K dataset, and checkpoints public on GitHub, plus an independent community reimplementation.
- **Why it matters here:** AlpaGasus is the strongest existing template for our own R-control design (Task 3), and its documented coding-category collapse is the concrete cautionary example behind our threat audit — a single-score filter can silently distort category balance, which for Bengali could mean stripping out a register or dialect rather than a skill.

**10. A Survey on Data Selection for LLM Instruction Tuning** (Wang et al., arxiv.org/pdf/2402.05123)
- *Problem:* Frames the field-level question this whole project sits inside — how to automatically select a small, high-quality subset from large instruction sets, since quality outweighs quantity and manual selection is costly and biased.
- *Data used:* None collected (survey); catalogs existing sets — Self-Instruct (52K/252), Alpaca (52,002), WizardLM (250K via Evol-Instruct), LIMA (1,000/300/50), Dolly-v2 (15,000, Wikipedia-restricted), P3 (170 datasets, 2,052 templates).
- *Method/pipeline (of the papers it surveys):* Instruction length, perplexity, reward-model scores, k-nearest-neighbor distance, CLIP score, instruction-following-difficulty ratios, sentence embeddings, InsTag labels, complexity/quality scores, semantic-parse-tree node counts.
- *Baseline(s):* N/A (survey).
- *Evaluation approach:* N/A (survey; aggregates reported numbers across surveyed papers).
- *Results:* N/A (survey).
- *Limitations (as stated by the authors):* No uniform evaluation standard across selection methods; heavy time/API cost when filtering hundreds of thousands of instructions with strong LLMs; quality-assessment models are built almost entirely for English and general domains. Additional limitation we note: the survey's comparison tables aggregate numbers reported under different base models and benchmarks rather than a single controlled re-run, and it leans on GPT-4-as-judge throughout.
- *Reproducibility:* No new code/data; maintains a curated paper list at github.com/Bolin97/awesome-instruction-selector.
- **Why it matters here:** Confirms, from a field-wide view, the exact hole this thesis is aimed at — the survey's own stated limitation ("quality-assessment models... built almost entirely for English") is our research gap, stated by someone else first.

---

## Part C — Gap Synthesis, Organized by the Strategy Taxonomy (PROJECT_BRIEF §4)

**Family A — Deduplication.** No paper in our 14 tests deduplication specifically on Bengali or any low-resource Indic language. DEITA and the Survey both discuss diversity/redundancy conceptually (embedding-distance selection), but neither runs semantic deduplication as a standalone, measured intervention with a random-subset control. TigerLLM's Bangla-Instruct pipeline is self-instruct generated from 500 seed tasks, which is a plausible source of near-duplicate outputs, but the paper does not measure or remove duplication.

**Family B — Language-quality filtering.** This is the family most specific to our problem and least covered by the literature we reviewed. None of the 10 confirmed-Include papers implements script-purity filtering, Unicode-malformation cleanup, or translationese detection as a measured intervention with reported effect size. Two papers document the *symptom* without proposing the *fix*: "Too late to train, too early to use?" reports inconsistent Bengali script generation and flags machine-translation bias in existing datasets, and "Evaluating LLMs' Multilingual Capabilities for Bengali" quantifies a tokenization-fertility-to-accuracy relationship. Both are diagnostic, not corrective — they tell us the problem exists in Bengali data/models but not how to filter for it upstream.

**Family C — Instruction–response quality filtering.** This is the best-covered family, but entirely on English data. AlpaGasus (single LLM-judge score), DEITA (complexity × quality composite score), Superfiltering (weak-model scoring signal, pending full read), and CoachLM (revision rather than filtering, pending full read) all propose concrete C-family methods and report gains — but every reported result is on English or English-source instruction data (Alpaca, Dolly, ShareGPT, UltraChat, WizardLM). TigerLLM applies "multi-criteria filtering" when generating Bangla-Instruct, but does not ablate which criteria matter or compare against an unfiltered/randomly-subsampled version.

**Family D — Diversity/coverage.** "Data Diversity Matters for Robust Instruction Tuning" is the one paper in our set that isolates diversity as a variable (pending full read for the specific metric used), again on English/general-purpose chat corpora. No Bengali-specific diversity metric exists in the reviewed set — embedding-space coverage would need a Bengali-capable embedding model, which is not verified yet (see Task 2's V0 pool checks).

**Consensus across sources.** Every method-proposing paper in the C family that reports a comparison (AlpaGasus, DEITA) shows that a smaller, filtered/selected subset matches or beats the full unfiltered pool — and does so with far less training compute. This is the mechanism our thesis is built on.

**Divergence / the actual hole.** Every one of those results is produced and validated on English or English-sourced data, using judges and scorers (ChatGPT, GPT-4, LLaMA-7B scorers trained on ChatGPT rankings) that were themselves trained overwhelmingly on English. None of the C-family papers report a random-subset control at the same size as their filtered set except AlpaGasus (Alpaca-9k-random) — DEITA and Superfiltering compare against other selection heuristics, not against "just take this many examples at random," which means part of their reported gain could still be a data-size effect rather than a pure quality effect. Meanwhile, the two Bengali-specific papers in our set (Too Late to Train, Evaluating Multilingual Capabilities) document exactly the failure modes — script errors, tokenizer fragmentation, translationese — that would make an English-tuned filter behave unpredictably on Bengali, but neither paper tests a filter at all.

**Research gap statement.** Data-quality filtering for instruction tuning is well-studied and empirically validated on English instruction data, and the mechanism it relies on (smaller filtered sets matching or beating larger unfiltered ones) is consistently reproduced. Whether that mechanism holds for Bengali — a low-resource, script-distinct, tokenizer-inefficient language whose available instruction data (as shown by TigerLLM and BanglaLlama) is a mix of unfiltered machine translation and self-instruct generation — is untested. No paper in our reviewed set filters Bengali instruction data using language-quality-specific criteria (script purity, Unicode normalization, translationese detection), and none of the English-language filtering methods we reviewed report a same-size random-subset control rigorous enough to separate a true quality effect from a data-volume effect.

**Contribution claim.** A controlled evaluation of semantic deduplication, Bengali-specific language-quality filtering, and instruction–response quality scoring on TigerLLM's Bangla-Instruct pool, with equal-size random-subset controls at every step — isolating whether each intervention's gain is a genuine quality effect or a data-volume effect, something the closest English-language precedents (AlpaGasus partially, DEITA and Superfiltering not at all) do not fully establish even for English.

---

## Part D — Pressure-Testing the Gap Against the Three Sub-Questions (PROJECT_BRIEF §1)

**RQ1 — Does each intervention improve downstream performance beyond what an equal-size random subset achieves?**
The gap survives this test. AlpaGasus is the only paper in our set with a comparable control, and it shows filtering *does* beat random selection at the same size for English — but that result has never been checked for Bengali, where the filter itself (an LLM judge) may be far less reliable at recognizing quality in Bengali text. If our Bengali result matches AlpaGasus's English finding, that is itself informative (transfer holds); if it doesn't, that is the more interesting finding the current literature cannot predict either way.

**RQ2 — Do interventions developed on English transfer to Bengali, or do Bengali-specific properties change what works?**
This is the sub-question best supported by our extracted evidence. "Too late to train, too early to use?" and "Evaluating LLMs' Multilingual Capabilities for Bengali" both independently document Bengali-specific failure modes (script generation errors, tokenizer fragmentation) that have no English equivalent in the C-family papers we reviewed. This directly motivates why Family B (language-quality filtering) needs to exist as a distinct strategy family rather than assuming Family C (English-style LLM-judge filtering) is sufficient on its own.

**RQ3 — Do interventions compose, or do their gains overlap?**
This is the weakest-supported sub-question in our current reading. "Data Diversity Matters for Robust Instruction Tuning" is the only candidate source and it is still a partial extraction (pending full read). No paper in our set runs a stacked-intervention ablation (dedup → language filter → quality filter, measuring marginal gain at each step) the way our variant matrix (Task 3) proposes. This is a genuine open question the literature does not answer, which is exactly why the variant matrix is built as a stacking design (V1 → V2 → V3) rather than three independent one-off comparisons.

**Verdict:** the gap holds under all three pressure tests, but its strength differs — RQ2 is well-evidenced by papers already fully read, RQ1 is evidenced by one partial analogue (AlpaGasus) that has never been run on Bengali, and RQ3 is essentially unaddressed in the literature we have access to, which is a limitation to state plainly in the thesis rather than paper over.
