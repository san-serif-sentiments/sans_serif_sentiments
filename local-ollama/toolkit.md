---
title: Hallucination Risk—Field Guide
archetype: guide
status: draft
owner: AI Reliability Team
maintainer: Platform ML
version: 0.1
tags: [hallucination, calibration, bayes, linguistics, narrative]
last_reviewed: 2025-09-03
---

## Overview
A practitioner’s guide to quantifying and reducing hallucinations in LLM outputs using:
1) information-theoretic risk bounding (HallBayes-style rolling priors), and
2) linguistic/literary checks (pragmatics, discourse coherence, intertextual verification).

## Why It Matters
Fluent wrong answers erode trust, create liability, and block adoption in regulated or high-stakes workflows. This guide enables measurable reliability (SLA-style) and explainable gates (why we answered or refused).

## Audience, Scope & Personas
- **ML Engineers:** integrate risk gating and signals
- **Applied Researchers:** experiment and tune
- **Product/Compliance:** interpret risk and coverage trade-offs  
Scope: QA, RAG, summarization, long-form generation, agent workflows.

## Prerequisites
- Access to an LLM API (or local model) capable of multi-sample generation
- Basic Python stack for orchestration and logging
- Optional: retrieval/search capability; NER + dependency parsing

## Security, Compliance & Privacy
- Log anonymized metrics; redact PII in prompts/outputs
- Keep retrieval sources auditable (links, timestamps)
- Store only hashes for sensitive content; rotate keys and rate-limit external calls

## Tasks & Step-by-Step Instructions
1. **Define the Event \(𝓐\):**  
   - Answer/Refuse (decision safety) or Correct/Incorrect (when ground truth available).
2. **Set Target Risk \(h^*\):** Example 5% error at 95% confidence.
3. **Rolling Priors (Bayesian in Expectation):**  
   - Generate weakened prompts (evidence-suppressed or closed-book); take n samples each.
   - Compute average prior success \(\bar{q}\) and information gain \(\bar{\Delta}\).
   - Bound success \(p_{\max}\) via KL( Ber(\(p\)) || Ber(\(\bar{q}\)) ); risk = \(1 - p_{\max}\).
4. **Decision Rule:**  
   - Bits-to-Trust (B2T) from target \(h^*\); Information Sufficiency Ratio (ISR) = \(\bar{\Delta}/\text{B2T}\).  
   - **ANSWER** if ISR ≥ 1 (plus safety margin); else **REFUSE**.
5. **Linguistic/Literary Signals (post-hoc or inline):**  
   - Pragmatics & presupposition check; discourse reference check; internal consistency; intertextual verification of proper nouns, dates, quotes.
6. **Calibration:**  
   - Validate on labeled set; tune safety margin to meet \(h^*\) with Wilson intervals.
7. **Portfolio Reporting:**  
   - Coverage (answer rate), empirical error + CI, worst-case risk bound, reasons for refusals.

## Access Control & Permissions
- Separate roles to change \(h^*\)/thresholds vs. only read metrics.
- Feature flag for “strict mode” in regulated products.

## Practical Examples & Templates (✅/❌)
- ✅ Evidence Q&A (RAG): remove evidence in priors; refuse if model answers without it.
- ✅ Fact-list outputs: intertextual probes for each bullet with cached wiki/KB lookup.
- ❌ Creative fiction blocked by factual checker: allow but enforce narrative consistency instead.
- ❌ Single-sample gates: unstable; use ≥5 samples per prompt.

## Known Issues & Friction Points
- Latency/cost from multi-call analysis  
- Confident-but-wrong parametric beliefs may evade uncertainty checks  
- Arithmetic or templated tasks can over-trigger refusals (tune event 𝓐)

## Tips & Best Practices
- Start strict (low \(h^*\)), relax with evidence; layer retrieval for verification.
- Cache priors for common queries; parallelize sampling; degrade gracefully on timeouts.
- Log rationales users can understand (“missing evidence,” “unverifiable entity”).

## Troubleshooting Guidance
- High false refusals → reduce masking strength / increase allowed margin.
- Missed hallucinations → add intertextual probe on OOD entities & dates.
- Latency SLO breaches → adaptive early-exit (refuse when early priors are bleak).

## Dependencies, Risks & Escalation Path
- Dependencies: LLM API, retrieval, lightweight NLP (NER, coref), metrics store
- Risks: over/under gating; external search reliability; model version drift
- Escalation: Reliability On-Call → Applied Research → Legal/Compliance (if SLA breach)

## Success Metrics & Outcomes
- ≤ \(h^*\) empirical error on answered items (95% CI)  
- Answer coverage at target (e.g., ≥60%)  
- Time-to-answer within SLO; top-3 refusal reasons actionable

## Resources & References
- Internal: risk dashboard, evaluator datasets, prompt packs  
- External: research on semantic entropy, multi-agent critique, narrative coherence

## Last Reviewed / Last Updated
- 2025-09-03


---

---
title: Hybrid Hallucination POC Plan (Bayes + Linguistics/Literature)
archetype: playbook
status: draft
owner: Applied Research
maintainer: Reliability Eng
version: 0.1
tags: [POC, pipeline, evaluation]
last_reviewed: 2025-09-03
---

## Overview
Design and evaluate a pipeline that merges HallBayes-style risk bounding with pragmatic, discourse, and intertextual checks.

## Why It Matters
Combines quantitative guarantees with interpretable language-level signals; improves precision and provides human-auditable reasons.

## Audience, Scope & Personas
Research+Platform teams validating feasibility and ROI on 2–4 target tasks (RAG QA, summarization, factual explainer).

## Prerequisites
Seed dataset (≥500 items) with correctness labels, retrieval corpus (if RAG), LLM access, lightweight NER, search connector.

## Security, Compliance & Privacy
Use synthetic or de-identified test data; log only hashes of sensitive text; document external sources used.

## Tasks & Step-by-Step Instructions
1. **Dataset & Splits:** train/val/test with domain diversity; mark “high-stakes” vs “low-stakes.”
2. **Baselines:** single-call LLM; entropy thresholding; self-consistency voting.
3. **HallBayes Module:** implement rolling priors, ISR decision, risk bounds; tune margin on validation (Wilson CI).
4. **Linguistic Modules:**  
   - Pragmatics: presupposition detect → auto-clarify or refuse.  
   - Discourse: unresolved references, orphaned markers, new-entity spikes.  
   - Narrative consistency: contradiction scan across sentences; date/number conflicts.  
   - Intertextual probes: verify proper nouns, dates, quotes via KB/web.
5. **Fusion Strategy:** score union (logical OR) vs. weighted ensemble; ablation to quantify each signal’s contribution.
6. **KPIs:** error@answered (95% CI), answer coverage, latency p95, top error modes, human-acceptance rate in blind review.
7. **Rollout Plan:** offline → shadow → limited GA; feature flag + kill switch.

## Access Control & Permissions
Read-only dashboards to product; thresholds editable by Reliability Eng only.

## Practical Examples & Templates (✅/❌)
- ✅ “Contrast prompts” for negation/phrasings; flag divergence.
- ✅ Entity-verify hook: any new proper noun triggers retrieval check.
- ❌ Hard-block creative outputs with fact-checker; prefer consistency checks.

## Known Issues & Friction Points
Latency; flaky web sources; entity ambiguity; model version drift.

## Tips & Best Practices
Cache retrieval; parallelize priors; prioritize verification on sentences with numbers/dates/names.

## Troubleshooting Guidance
- Too slow → reduce priors m or samples n; adaptive early-stop on clear refusals.  
- Over-refusal → relax margin; narrower masking; whitelist benign entities.  
- Missed errors → tighten intertextual checks; add secondary critic model.

## Dependencies, Risks & Escalation Path
LLM, retriever, NER, KB/search API; risks: cost, drift, false alarms; escalation: Eng Lead → Research Lead.

## Success Metrics & Outcomes
Meet target \(h^*\) with ≥X% coverage; latency within SLO; stakeholder sign-off after blind review (≥Y% accept).

## Resources & References
Prompt pack; evaluation notebook; calibration report template.

## Last Reviewed / Last Updated
2025-09-03


---

---
title: Prompt Pack—Contrast, Pragmatics, Intertextual
archetype: templates
status: draft
owner: Prompt Eng
maintainer: Applied Research
version: 0.1
tags: [prompts, templates]
last_reviewed: 2025-09-03
---

## Overview
Reusable prompt scaffolds to surface hallucinations via contrastive phrasing, presupposition checks, and verification prompts.

## Why It Matters
Simple, composable templates that expose instability, false premises, and unsupported specifics.

## Audience, Scope & Personas
Prompt engineers and evaluators; applies to QA, summaries, explainers.

## Prerequisites
Model supports system/user messages and multi-sample generation.

## Security, Compliance & Privacy
Avoid injecting sensitive hypotheses; prefer neutral rephrasings.

## Tasks & Step-by-Step Instructions
- Use **A/B prompts**; compare answers semantically (exact entity/date match where applicable).
- Trigger **Verify** prompts for sentences with proper nouns/dates/quotes.

## Practical Examples & Templates (✅/❌)
**1) Contrast Prompt (Neutral vs Challenge)**

System: You must answer only when certain. Otherwise say: "I don't have enough evidence." User (A): {QUESTION}

User (B): Some people claim {LIKELY_ANSWER}. Is that correct? Justify briefly.

**2) Presupposition Extractor**

System: Identify assumptions in the question. If unverifiable, advise refusal. User: List presuppositions in: "{QUESTION}". Then answer only if they hold.

**3) Intertextual Verifier**

System: For each claim, state if it is supported by the provided snippets. If unsupported, mark "VERIFY". User: Context: {TOP_K_SNIPPETS} Answer to check: {MODEL_ANSWER} Return a table: [claim | support_evidence | status].

**4) New-Entity Sentinel**

System: If the answer introduces names/titles/dates not present in the context, flag them as NEW and advise verification/refusal. User: {QUESTION}

**5) Narrative Consistency Probe**

System: Check contradictions in the text (entities, dates, numbers, roles). List conflicts. User: Text: {MODEL_ANSWER}

✅ Use on high-stakes claims, named entities, dates.  
❌ Use on purely creative fiction to block output; use consistency-only mode instead.

## Known Issues & Friction Points
Challenge phrasing can bias answers; tune carefully. Intertext prompts need decent retrieval.

## Tips & Best Practices
Normalize entities (coref); compare canonicalized dates; run 3–5 samples for stability.

## Troubleshooting Guidance
Divergent A/B but both wrong → escalate to retrieval or refuse.  
Frequent refusals → relax system rule or increase evidence.

## Dependencies, Risks & Escalation Path
Relies on retrieval (optional) and sampling; risks: latency, over-refusal.

## Success Metrics & Outcomes
Increased disagreement detection rate; reduced unsupported-specifics; improved reviewer satisfaction.

## Resources & References
Evaluator scripts; similarity metrics; claim splitter.

## Last Reviewed / Last Updated
2025-09-03


---

---
title: Evaluation & Reporting Checklist (Hallucination Control)
archetype: checklist
status: draft
owner: QA Lead
maintainer: Reliability Eng
version: 0.1
tags: [evaluation, reporting, sla]
last_reviewed: 2025-09-03
---

## Overview
A concise checklist for experiments and rollouts that claim hallucination control.

## Why It Matters
Prevents “trust theater” by requiring measurable, reproducible evidence.

## Audience, Scope & Personas
QA, Compliance, Product; applies to offline evals, shadowing, and GA.

## Prerequisites
Version-locked models; frozen datasets; reproducible seeds.

## Checklist
- **Event Definition \(𝓐\):** Answer/Refuse or Correct/Incorrect documented.
- **Target \(h^*\):** error tolerance (e.g., 5%) + confidence level stated.
- **Sampling Plan:** m priors, n samples; parallelization strategy; timeouts.
- **Calibration:** Wilson CI used; safety margin tuned on validation only.
- **Ablations:** Bayes-only vs +Linguistic vs +Intertextual; report deltas.
- **Error Taxonomy:** confabulations vs systematic errors vs grounding misses.
- **Latency/Cost:** p50/p95 times; cost/query; cache hit rate.
- **Drift Watch:** model version pin; weekly smoke tests; threshold re-check.
- **Audit Artifacts:** per-item rationale, priors, verifier findings; redacted logs.
- **Portfolio Report:** coverage, empirical error + CI, worst-case bound, top refusal reasons.

## Access Control & Permissions
Only Reliability Eng can change thresholds; QA can export reports.

## Practical Examples & Templates (✅/❌)
✅ Include confusion matrices and CI bands.  
❌ Claim “5% error” without CI, or with tuned-on-test thresholds.

## Known Issues & Friction Points
Ground-truth scarcity; flaky retrieval; ambiguous questions.

## Tips & Best Practices
Prioritize sentences with numbers/names for verification; keep a whitelist of benign entities.

## Troubleshooting Guidance
Spikes in error → inspect new entities and dates; increase verification depth temporarily.

## Dependencies, Risks & Escalation Path
LLM, retriever, metrics store; risks: cost, false alarms; escalation: QA Lead → Platform Eng.

## Success Metrics & Outcomes
Meets \(h^*\) with ≥X% coverage; stable latency; stakeholder sign-off.

## Resources & References
Calibration notebook; report template.

## Last Reviewed / Last Updated
2025-09-03


---

---
title: Author & Maintainer Question Bank (Targeted, Non-Generic)
archetype: questions
status: draft
owner: Research Review
maintainer: Applied Research
version: 0.1
tags: [review, due-diligence]
last_reviewed: 2025-09-03
---

## Overview
Curated, curiosity-driven questions that probe methodology, masking/priors, and deployment realities.

## Why It Matters
Accelerates deep due diligence; avoids surface-level Q&A.

## Audience, Scope & Personas
Reviewers, integrators, partner teams.

## Questions
1. **Rolling Priors:** How do you select weakened prompts in closed-book vs evidence modes, and how sensitive are risk bounds to that selection?
2. **Bound Tightness:** Empirically, how close are EDFL-derived \(p_{\max}\) bounds to observed error on answered items across domains?
3. **Systematic Errors:** What happens when the model is confidently wrong due to training bias—how do you detect without external grounding?
4. **Granularity:** Did you test single-fact vs multi-span context removal; how did precision/recall of detection change?
5. **Fusion:** Have you tried weighting Bayes risk with linguistic signals (new-entity spikes, unresolved references)? Any learned meta-classifier?
6. **Cost/Latency:** What is the best-known configuration (m, n) that meets \(h^*\) with acceptable p95 latency in production?
7. **Version Drift:** How often must thresholds be re-tuned as base models update? Any automatic drift detectors?
8. **User Experience:** What refusal rationales best preserve satisfaction while maintaining safety (empirical phrasing tests)?
9. **Coverage Strategy:** For tasks with low baseline coverage under strict gates, which mitigations helped (evidence enrichment, softer priors)?
10. **Auditability:** What per-item artifacts are stored (priors, samples, verifier hits) and how are they redacted for privacy?

## Last Reviewed / Last Updated
2025-09-03


---
