# Comprehensive Mitigation Plan

# Responsible AI Risk Mitigation for a Generative AI Language Model

**System:** distilgpt2-gender-bias-ft · **Author:** Amos Bunde · **Date:** 2026-10-04
**Companion document:** Ethical Audit Report (section references below point to it)

## **1. Overview and Objectives**

## 1.1 Purpose of This Mitigation Plan

The Ethical Audit Report established that the model injects gendered language into 70% of neutral hiring-domain prompts, frames competence along stereotype lines, overrides explicit counter-stereotypical instructions, and fabricates identities and contact details. This plan translates those findings into concrete, prioritized safeguards that must be in place **before any deployment decision**, and defines owners, timelines, and measurable success criteria for each.

## 1.2 Scope of Mitigation

**In scope:** gender bias in generated text; instruction-override behavior; fabricated people/contact-detail artifacts; prompt-manipulation exposure; governance and monitoring for this model and its retrained successors.
**Out of scope:** bias dimensions not yet audited (race, age, disability — flagged as required follow-up audit work), model performance/quality tuning unrelated to safety, and infrastructure security beyond the model's input/output path.

## **2. Summary of Identified Risks**

| Risk Category | Description | Severity (Low / Medium / High) |
| :---- | :---- | :---- |
| Ethical | Occupational gender stereotyping in neutral prompts (Audit 4.1–4.2); explicit user intent overridden (role-reversal failures); biased comparative hiring recommendations (Audit 4.3) | High |
| Legal | Employment-discrimination exposure (Title VII / EU equal treatment); EU AI Act Annex III high-risk classification for recruitment use; defamation risk from real-person references (Audit 5.2) | High |
| Privacy | Fabricated personal names and email addresses; demonstrated leak-shaped failure mode if retrained on real HR data (Audit 4.4, 5.3) | Medium |
| Security | No refusal/filter layer; weak instruction following makes prompt-level guardrails unreliable; trivially elicitable biased content (Audit 5.4) | Medium |
| Environmental | Small model (82M params), CPU-scale inference; retraining cycles cheap in cost and carbon (Audit 5.5) | Low |

## **3. Mitigation Strategies**

## 3.1 Bias and Fairness Mitigation

- **Data repair and rebalancing (root cause):** audit `gender_bias_train.jsonl`, remove or rewrite stereotyped records, and add counterfactual augmentation — for every role/gender pairing, generate the swapped-gender and gender-neutral variants so each role appears with balanced gender associations. Target: ≤5% gendered-term rate on the neutral prompt suite after retraining (baseline: 70%).
- **Retrain and re-audit:** fine-tune on the repaired dataset and rerun the full notebook suite (sensitivity, counterfactual, lexicon) as an acceptance gate. Counterfactual Jaccard similarity on gender-swap pairs should rise materially (baseline 0.22–0.29), and role-reversal prompts must honor the requested gender.
- **Comparative-recommendation ban:** block "who is better, X or Y" hiring prompts at the application layer regardless of retraining results — candidate ranking is out of scope for any generative model in this program.

## 3.2 Prompt and Output Controls

- **Output bias filter:** run every generation through the audit's gendered-language lexicon; auto-neutralize pronouns in role descriptions (he/she → they) and flag outputs whose leadership/support trait framing correlates with gender terms.
- **Artifact filters:** regex-based blocking of generated email addresses, URLs, social handles, and a named-entity check that suppresses real-person names in role/hiring content.
- **Prompt hygiene:** constrain the application to approved instruction templates; reject or escalate prompts that request gendered candidate profiles ("write a job description for a female X") rather than passing them to the model.
- **Refusal behavior:** since the base model has none, enforce refusals in the serving layer (policy classifier in front of the model).
- **Red-team evidence (Audit 4.5):** a prototype of this output filter was tested against six adversarial prompts and intercepted 5 of 6 (1 BLOCK, 4 REVIEW); the filter must use plural-aware gender lexicons (gap found during red-teaming) and be paired with human review for subtly skewed outputs that avoid lexicon terms.

## 3.3 Explainability and Monitoring

- **Regression suite in CI:** the audit notebook's prompt suite and lexicon metrics run automatically on every model update; deployment is blocked if the neutral-prompt gendered rate, trait-split, or counterfactual similarity regresses beyond thresholds.
- **Production sampling:** log a random 5% sample of production outputs (with consent/privacy controls) and score them weekly with the same lexicon analyzer; trend dashboards reviewed monthly.
- **Drift alerts:** alert when weekly gendered-term rate or artifact rate exceeds 2× the post-retraining baseline.

## 3.4 Human Oversight and Governance

- **Human-in-the-loop:** all people-affecting content (job descriptions, reviews, any HR text) requires named-reviewer approval before use; reviewers get a checklist derived from the audit findings (gendered defaults, trait framing, fabricated details).
- **Escalation path:** flagged outputs route to an AI governance lead; systematic failures trigger model rollback and Ethics Committee notification within 5 business days.
- **Ethics Committee gate:** the committee approves (a) the retraining acceptance report, (b) any scope expansion, and (c) the annual re-audit.

## **4. Implementation Plan**

## 4.1 Technical Implementation

| # | Action | Owner | Timeline | Success criterion |
| :---- | :---- | :---- | :---- | :---- |
| 1 | Dataset audit + counterfactual augmentation | ML Engineering | Weeks 1–2 | Balanced role×gender distribution documented |
| 2 | Retrain + rerun audit notebook | ML Engineering | Weeks 3–4 | Neutral gendered rate ≤5%; role-reversal prompts honored |
| 3 | Output filter service (lexicon + PII/NER) | Platform Engineering | Weeks 2–4 | 100% of outputs scanned; <1% artifact pass-through on test suite |
| 4 | CI regression gate wired to repo | Platform Engineering | Week 4 | Pipeline blocks on threshold breach |
| 5 | Monitoring dashboard + alerts | MLOps | Weeks 5–6 | Weekly scored sample live; alert tested |

## 4.2 Operational Implementation

- HR/recruiting use remains **prohibited** until the Ethics Committee lifts the restriction post-retraining (policy owner: AI Governance Lead, effective immediately).
- Reviewer training (1-hour session + checklist) for all HITL reviewers before any pilot (People Ops, Week 5).
- Quarterly governance review of incident log, drift dashboards, and any new bias dimensions; annual full re-audit including race/age/disability prompts.
- Model card kept current: every retrain updates known-limitations and evaluation sections.

**Regulatory mapping (stand-out addition):** these controls map to the **EU AI Act** high-risk obligations — risk management system (Art. 9 → this plan + CI gate), data governance (Art. 10 → 3.1 data repair), record-keeping (Art. 12 → audit logs), transparency (Art. 13 → model card), human oversight (Art. 14 → 3.4) — and to **NIST AI RMF** functions: GOVERN (committee gate, policy ban), MAP (audit report), MEASURE (lexicon metrics, regression suite), MANAGE (filters, HITL, rollback path).

## **5. Residual Risk Assessment**

| Risk | Remaining Risk Level | Rationale |
| :---- | :---- | :---- |
| Gender bias in neutral roles | Medium | Retraining + filters cut measured skew, but lexicons are English-only and binary; subtle framing bias will survive automated checks and relies on HITL review |
| Misuse in hiring decisions | Low–Medium | Comparative prompts blocked and policy prohibits HR use, but determined misuse of raw model weights cannot be fully prevented; mitigated by access controls on the checkpoint |
| Prompt manipulation | Medium | Serving-layer refusals and template constraints help, but the base model itself remains steerable; defense depends on the wrapper staying in the path |

## **6. Validation and Success Criteria**

**Iteration-1 retraining evidence** (`starter/data_repair_and_retraining.ipynb`, `outputs/retraining_bias_comparison.csv`): counterfactual gender-swap augmentation balanced the *direction* of bias (symmetric male/female term counts; mean counterfactual similarity 0.245 → 0.294) but **did not meet the ≤5% neutral gendered-rate gate** — outputs remained gendered because the repaired data was still gendered text, only balanced.

**Iteration-2 retraining evidence** (`starter/data_repair_and_retraining_iter2.ipynb`, `outputs/retraining_iter2_comparison.csv`): retraining on a fully **neutralized** dataset (604 → 0 gendered terms in training data) **meets the gate: 0/10 (0%) neutral prompts gendered** (baseline 10/10), mean gendered terms 3.2 → 0.0, counterfactual similarity 0.245 → 0.298, with substantive non-degenerate outputs. The production acceptance suite should confirm with many samples per prompt and add name-based probes; output filters and HITL review (3.2–3.4) remain in force regardless.

- **Quantitative gates (per retrain):** neutral-prompt gendered-language rate ≤5% (baseline 70%); counterfactual gender-swap similarity ≥0.60 mean (baseline 0.24); 100% of role-reversal prompts honor the requested gender; 0 fabricated emails/real-person names on the artifact test suite.
- **Operational gates:** 100% HITL coverage for people-affecting content; drift alerts tested quarterly; zero unreviewed HR outputs in audit samples.
- **Governance gate:** Ethics Committee sign-off recorded before any scope change; re-audit completed annually.

## **7. Limitations of the Mitigation Plan**

- Lexicon-based measurement undercounts subtle and intersectional bias; thresholds are necessary but not sufficient evidence of fairness.
- The plan addresses gender bias only; other protected characteristics require their own audit cycle before any deployment claim.
- Post-processing filters can over-correct (e.g., neutralizing legitimately gendered content) and add latency.
- Residual risk never reaches zero: the unfiltered checkpoint still exists and must stay access-controlled for educational use only.

## **8. Conclusion**

The audited model is unsafe for any people-affecting use in its current state, and this plan deliberately pairs **root-cause repair** (data rebalancing and retraining with measurable acceptance gates) with **defense-in-depth** (output filtering, prompt controls, HITL review, continuous monitoring, and committee governance). With the technical work executing over ~6 weeks and the operational controls in force immediately, the risks identified in the audit are reduced to a managed residual level — and the regression suite ensures they stay that way across future model versions. Deployment in any hiring-adjacent context remains conditional on the Ethics Committee's review of the post-retraining audit evidence.
