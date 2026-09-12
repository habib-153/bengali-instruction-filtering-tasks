# Meeting 2 Prep Tasks

Tracking the four things that need to be done before Meeting 2 with the supervisor.

## 1. Literature review and research gap
- [x] Screened all 14 papers in the shared Literature Review Spreadsheet (10 marked Include, 4 still Unsure and need a full read).
- [x] Extracted the key details (data, method, results, limitations) for every included paper.
- [x] Synthesized a clear research gap: filtering has been shown to work for English instruction data, but nobody has tested it on Bengali data yet.
- [ ] Finish reading the 4 Unsure papers and confirm PDF access for the ones still pending.

## 2. Data sources and filtering strategies
- [x] Scored all 16 candidate filtering strategies from the project brief against evidence strength, cost, independence, and Bengali relevance.
- [x] Picked 3 strategies to combine: semantic deduplication, language-quality and translationese filtering, and LLM-as-judge quality scoring.
- [x] Proposed a data pool: BanglaLlama's Bangla-Orca and Bangla-Alpaca as the unfiltered baseline, TigerLLM's Bangla-Instruct as a comparison point.
- [ ] Verify dataset licenses and actual row counts before using any of this data for training.

## 3. Initial experimental comparison
- [x] Designed a 7-run comparison matrix: a baseline, three filtered variants, and a random-subset control matched to each one.
- [x] Listed the evaluation metrics to report, covering both data-level checks and downstream model performance.
- [x] Flagged the main risks to watch for, including benchmark contamination and judge bias.

## 4. Feasibility pilot
- [x] Planned a small pilot on 2,000 examples to sanity-check the data pipeline before committing to full runs.
- [x] Set clear stop-and-redesign criteria so we know when the pipeline itself needs fixing versus when the data is just noisy.
- [ ] Actually run the pilot once the license checks from Task 2 are cleared.

## Open questions for the supervisor
- Should the baseline (V0) be BanglaLlama's unfiltered data, or TigerLLM's already-filtered Bangla-Instruct?
- Any guidance on the 4 papers we're still unsure about including?
