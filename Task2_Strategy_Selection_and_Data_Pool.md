# Task 2 — Filtering Strategy Selection and V0 Data Pool

**Project:** Evaluating Data-Quality Filtering Strategies for Bengali Instruction Tuning
**Inputs:** PROJECT_BRIEF.md §4 (taxonomy) and §7 (candidate data sources), plus the extraction findings in Task 1.

---

## Part A — Scoring Every Candidate Strategy in the Taxonomy

Each candidate is scored on four criteria, all High/Medium/Low:

- **Evidence strength** — how well the literature we actually read (Task 1) supports this strategy working, for instruction data in general.
- **Implementation cost** — compute, API spend, and engineering effort relative to our stated compute budget (PROJECT_BRIEF §9).
- **Independence** — how little this strategy's effect is likely to overlap with the others, which matters for the composability question (RQ3).
- **Bengali relevance** — how specifically this strategy addresses a problem that is known (from Task 1) to be Bengali/script/tokenization-specific, as opposed to a generic instruction-quality problem that happens to also apply to Bengali.

| Code | Strategy | Evidence strength | Implementation cost | Independence | Bengali relevance | Overall |
|---|---|---|---|---|---|---|
| A1 | Exact / hash-based duplicate removal | High (standard, well-understood) | Low | High | Low (language-agnostic) | Worth doing but not worth reporting as a "finding" — treat as part of basic cleaning (V0), not a tested variant. |
| A2 | Near-duplicate removal (MinHash+LSH, n-gram overlap) | Medium | Medium | Medium (overlaps with A3) | Low–Medium (n-gram overlap is weaker on Bengali due to different word-boundary and morphology behavior) | **Medium** |
| A3 | Semantic deduplication (embedding clustering) | Medium (conceptually supported by DEITA's diversity signal, not tested as a standalone intervention anywhere in our set) | Low (cheap once a Bengali-capable embedding model is chosen) | High (distinct mechanism from B/C families) | Medium (self-instruct generation, like TigerLLM's, is a known source of near-duplicate outputs) | **High — recommended** |
| B1 | Language identification / script purity | Low direct evidence (no paper in our set implements this as a measured filter), but the *problem* it targets is the best-documented Bengali-specific failure mode in Task 1 (script generation errors, code-switching) | Low | High | High | **High** |
| B2 | Unicode normalization / malformed-text removal | Low direct evidence, same reasoning as B1 | Low | High | High (Bengali ZWJ/ZWNJ and combining-mark errors are a known, distinct failure class from script-purity or translationese) | **High — recommended (paired with B5)** |
| B3 | Perplexity / LM-likelihood filtering against a Bengali reference LM | Low — no reliable, domain-balanced Bengali reference LM identified yet | High (would require training or sourcing a clean reference LM first — a confound-prone prerequisite) | Low (likely correlates heavily with B1/B5, since translationese and disfluent Bengali both raise perplexity) | Medium | **Low — drop (see rationale below)** |
| B4 | Heuristic quality rules (length, symbol ratio, repetition, truncation markers) | Medium (standard practice, implicitly present in most cleaning pipelines including TigerLLM's) | Low | Medium (overlaps with B2) | Low (mostly language-agnostic) | **Medium — fold into V0 basic cleaning, not a standalone variant** |
| B5 | Translationese / MT-artifact detection | Low direct evidence (no paper in our set implements this as a filter), but it is the single most-repeated concern across the Bengali-specific papers we read (BanglaLlama's own authors note translation-loss artifacts; "Too late to train" flags MT bias in existing datasets) | Medium (requires either a translationese classifier or heuristic proxy — needs scoping) | Medium (conceptually close to B1/B2, may need to be reported jointly rather than as a fully separable effect) | High | **High — recommended (paired with B2)** |
| C1 | LLM-as-judge scoring of instruction/response quality | High (AlpaGasus, DEITA both report strong effects on English) | Medium (API cost for scoring the full pool) | Medium (may overlap with B-family if judge implicitly penalizes disfluent Bengali) | Medium (judge reliability in Bengali is unverified — this is itself part of what we're testing) | **High — recommended** |
| C2 | Complexity + quality scoring (DEITA-style) | High on English (DEITA) | High (requires training scorer models, per DEITA's own pipeline) | Low (substantially overlaps with C1) | Medium | **Medium — not selected, too costly for our budget given overlap with C1** |
| C3 | Instruction-following difficulty / loss-based selection | Medium (referenced in the Survey as an existing signal; not deeply tested in our set) | Medium–High (requires running the base model over the pool to get loss signals) | Low (correlates with C1/C2 conceptually) | Low | **Low — not selected** |
| C4 | Reward-model scoring | Low evidence in our set (Survey mentions it as an existing signal, no paper we read implements it) | High (no ready-made Bengali reward model; would need one trained or adapted) | Medium | Low | **Low — drop, no Bengali reward model available** |
| C5 | Response-groundedness / instruction–response alignment checks | Low evidence (not implemented by any paper we read) | Medium | Medium | Low | **Low — not selected this round** |
| C6 | Template-artifact / refusal / degenerate-response removal | Medium (standard hygiene step, implicit in most cleaning pipelines) | Low | Medium (overlaps with B4) | Low | **Medium — fold into V0 basic cleaning** |
| D1 | Embedding-space coverage maximization | Low direct evidence for Bengali; conceptually related to A3 | Medium | Low (mechanically similar to A3) | Medium | **Low — not selected this round, redundant with A3** |
| D2 | Task-type / domain balancing | Medium ("Data Diversity Matters," pending full read, is the closest evidence) | Medium | Medium | Low–Medium | **Medium — not selected this round; flagged as the natural next strategy if compute allows a 4th variant** |

## Part B — Recommendation

**Recommended 2–3 strategies, in the order they should be applied (stacked):**

1. **Semantic deduplication (A3)** — cheapest of the three, most independent of the others, and directly targets a known TigerLLM-style risk (near-duplicate self-instruct outputs) that no paper in our set has measured.
2. **Language-quality and translationese filtering (B2 Unicode normalization + B5 translationese/MT-artifact detection, applied together as one Family-B step)** — the strategy with the weakest existing evidence base but the strongest Bengali-specific justification; this is the part of the taxonomy the literature (Task 1, Part C) leaves almost entirely untested.
3. **LLM-as-judge instruction–response quality scoring (C1)** — the strategy with the strongest English precedent (AlpaGasus, DEITA), included specifically to test whether that precedent transfers to Bengali (RQ2) rather than because it is novel.

**What we're dropping, and why:**

- **B3 (perplexity/LM-likelihood filtering) — dropped.** There is no robust, domain-balanced Bengali reference LM identified yet, and building one is itself a research project, not a preprocessing step. Using a weak or narrow reference LM would introduce a domain-matching confound we cannot cleanly separate from the effect we're trying to measure. If a suitable open Bengali LM turns out to exist (verify before fully discarding this), it could become a 4th variant later — but it should not block the current timeline.
- **C2 (DEITA-style complexity+quality scoring) — not selected, despite strong English evidence.** It requires training separate complexity and quality scorer models per DEITA's own pipeline, which is a heavier compute/engineering cost than our budget supports, and its effect substantially overlaps with the simpler C1 LLM-judge approach. If C1 alone shows a strong effect, C2 is not worth the added cost; if C1 shows a weak or unreliable effect in Bengali, that is itself evidence C2 would likely fail the same way (same underlying judge-model dependency).
- **D2 (task-type/domain balancing) — held in reserve, not dropped outright.** "Data Diversity Matters for Robust Instruction Tuning" is suggestive evidence but still a partial extraction (see Task 1). Recommend completing that paper's full read before deciding whether D2 becomes a 4th variant; for now, three stacked variants (A3 → B2/B5 → C1) is a defensible, budget-fitting spread per PROJECT_BRIEF §4's own guidance (cheap, independent, Bengali-sensitive).

This gives one strategy from Family A, one combined step from Family B, and one from Family C — matching the spread PROJECT_BRIEF §4 recommends, now justified against the actual extracted evidence rather than asserted.

---

## Part C — V0 Data Pool Confirmation

**Primary pool (confirmed as our focus per direction to prioritize the TigerLLM ecosystem):**

- **Bangla-Instruct** (TigerLLM) — ~100K instruction–response pairs, expanded from 500 volunteer-curated seed tasks via GPT-4o/Claude-3.5-Sonnet self-instruct generation with the authors' own multi-stage filtering already applied. This is our core pool precisely because it is *already* filtered by TigerLLM's own pipeline — our baseline (V0) needs to be defined carefully here (see note below).

**Secondary/comparison pool, for the unfiltered-baseline contrast the thesis argues from:**

- **Bangla-Orca** (BanglaLlama) — 172K samples, machine-translated from OpenOrca via Google Cloud Translation API, explicitly *not* quality-filtered by its authors (only random human spot-checks). This is the closest thing in our literature set to a true "raw, unfiltered" Bengali instruction pool, and is a stronger match for a genuine V0 baseline than Bangla-Instruct is.
- **Bangla-Alpaca** (BanglaLlama) — 52K pairs, same translation pipeline, same lack of filtering.

**Important scoping note (this needs a decision before Meeting/submission, not after):** TigerLLM's Bangla-Instruct is not an unfiltered pool — the source paper already applies its own multi-stage filtering during generation. If we treat Bangla-Instruct as V0, our "V0 baseline" is already partially filtered by someone else's undocumented criteria, which muddies what our own filtering variants (V1–V3) are being measured against. Two options:
1. Use **Bangla-Orca / Bangla-Alpaca (BanglaLlama, genuinely unfiltered)** as V0, and treat Bangla-Instruct's published numbers as an external reference point / sanity check, not as our own baseline.
2. Use **Bangla-Instruct as V0 anyway**, but explicitly document it as "V0 = TigerLLM's own filtered pool" in the write-up, reframing the contribution as "do *additional* filtering interventions improve on an already-filtered pool" rather than "does filtering beat no filtering." This is a weaker but still valid framing.

Recommend option 1 for the main experiment (a true unfiltered-vs-filtered contrast is the stronger, more publishable claim) with Bangla-Instruct used as a secondary comparison arm — but this is exactly the kind of decision that should be confirmed with the supervisor, since it changes what "our baseline" means.

**License / provenance items to verify before any of this pool is used for training (do not assume from memory — PROJECT_BRIEF §7's own recording requirement):**

- [ ] Bangla-Instruct Hugging Face repository license terms (check the actual dataset card at the `md-nishat-008` / TigerLLM Hugging Face collection — do not assume MIT/Apache without checking).
- [ ] Whether Bangla-Instruct pairs are tagged by generation source (GPT-4o vs. Claude-3.5-Sonnet) in the released data, since teacher-model identity is itself a provenance variable per PROJECT_BRIEF §7.
- [ ] Bangla-Orca / Bangla-Alpaca Hugging Face license terms, and whether Google Cloud Translation API's terms of service impose any restriction on redistributing or further processing translated output at scale.
- [ ] CulturaX Bengali subset license, if we end up needing a reference corpus for anything downstream (not currently needed since B3/perplexity filtering was dropped).
- [ ] Row-count and split verification for both pools by actually opening the files — do not cite the paper's reported sizes (172K / 52K / 100K) as final without confirming what's actually downloadable today, since dataset cards sometimes differ from paper-reported figures.

None of these have been checked yet — they are leads from the papers, not confirmed facts, per PROJECT_BRIEF §7's own instruction not to cite sizes or licenses from memory.
