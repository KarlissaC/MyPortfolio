# PRD — Contexta: Intent-Based Localization Platform

> **Note on placeholders:** Fields like author, reviewer names, and doc links are marked `[TBD]` / `[link]` because they depend on your org. Everything else is filled with PM-grade reasoning and defensible targets you can adjust to real baselines once instrumented.

---

## 0. Document Metadata

| Field | Value |
|---|---|
| **Title** | Contexta — Intent-Based Localization Platform |
| **Author / DRI** | [TBD — Product DRI] |
| **Status** | Draft |
| **Version / Last updated** | v0.1 — 2026-06-23 |
| **Reviewers / approvers** | Eng Lead [TBD] · Design Lead [TBD] · Head of Localization [TBD] · Legal/Trust [TBD] · Exec Sponsor [TBD] |
| **Related docs** | Designs `[link]` · Tech spec `[link]` · CCKG research `[link]` · ALPO model card `[link]` · Tickets/epic `[link]` · Resonance dashboard `[link]` |
| **Target release** | V1 GA — Q4 2026 (private beta Q3 2026) |
| **Live prototype** | https://contexta-982732324454.us-west1.run.app |

---

## 1. Problem & Context (Discovery) ⭐

**⭐ Problem statement.** When a streaming platform launches a title in a non-English market, viewers experience flattened, literal subtitles where jokes, idioms, and cultural references fail to land — so the story feels foreign and engagement drops, even when the translation is technically "accurate."

**⭐ Evidence the problem is real.**
- Platforms lose an estimated **20–40% of potential viewership** in non-English markets to weak localization, per the product brief and consistent with industry completion-rate gaps.
- Standard machine translation optimizes for lexical accuracy, not resonance; there is no quantitative pre-commit signal for cultural impact, so studios localize blind.
- Qualitative signal: recurring "the subtitles are robotic / the joke didn't translate" complaints in regional social listening and viewer NPS verbatims `[attach research link]`.

**Who is affected and how often.** Every non-English viewer of every localized title (millions of streams/day at scale), plus the localization ops teams who own the workflow for every title release (continuous). Severity is highest for comedy, dialogue-driven drama, and reality formats where humor/cultural texture *is* the product.

**Why now.** (1) LLM + knowledge-graph reasoning is finally good enough to model cultural intent, not just lexical mapping; (2) streamers are in a margin-discipline era and need ROI proof before spending on premium localization; (3) global content competition has shifted "good subtitles" from nice-to-have to a retention lever.

**⭐ Strategic alignment.** Ladders to the company goal of *international revenue growth and retention*. Directly supports an OKR around non-English market engagement and the strategy pillar of "make every title feel native everywhere."

**Cost of inaction.** Continued silent churn in high-growth regions, no defensible localization quality metric, and ceding premium-localization differentiation to competitors and specialist agencies.

**Existing workarounds.** Expensive human-only localization for flagship titles only; literal MT for the long tail; ad-hoc "transcreation" briefs that aren't measurable or repeatable.

---

## 2. Goals & Success Metrics ⭐

**⭐ Business goals.** Lift non-English engagement/retention on localized titles; reduce cost-per-localized-hour; create a defensible, measurable localization-quality moat.

**⭐ User goals (JTBD).**
- *Localization Ops:* ship subtitles that resonate as if natively written, with evidence, without ballooning cost or timeline.
- *Internationalization PM:* decide which markets are worth premium localization *before* committing budget.
- *Viewer:* understand and enjoy the story in their language without cognitive friction.

**⭐ Success metrics / KPIs (baseline → target).**

| Metric | Baseline | V1 Target |
|---|---|---|
| Resonance lift vs. baseline MT (Creative Testing Lab score) | 0% (MT baseline) | **+25%** |
| Cost per localized hour | Current blended rate (index 100) | **−50%** |
| Subtitle-attributed completion rate, non-EN titles | [instrument] | **+5–8 pts** on treated titles |
| Time-to-localize per title-hour | Current | **−40%** |

**North Star.** *High-resonance localized hours delivered* (hours that pass the resonance threshold), tying quality + volume + adoption into one number.

**Guardrail metrics.** Subtitle timing/readability quality, latency of real-time personalization, brand-safety incident rate (must not increase), per-title localization cost ceiling.

**Counter-metrics.** "Over-localization" complaints (viewers feeling the text was changed too far from intent), reviewer override rate spiking (signals the AI is wrong, not just bold), personalization opt-out rate.

**⭐ Non-goals (V1).** Dubbing/audio generation; on-screen text/graphics localization; real-time live-event subtitling; full self-serve for external studios (V1 is internal + design-partner only); languages outside the launch set.

---

## 3. Users & Market

**⭐ Personas.**
- **Localization Ops Manager (primary):** owns subtitle delivery SLAs across a title slate; needs throughput + quality evidence.
- **Internationalization PM (primary):** decides market prioritization and localization investment; needs ROI/CDI forecasts.
- **Dubbing/Subtitling Agency (secondary):** uses Contexta as a force-multiplier on contracted work.
- **Cultural Reviewer / Linguist (internal user):** human-in-the-loop quality gate and ALPO feedback source.

**Jobs-To-Be-Done.**
- "When I'm localizing a comedy into a distant market, I want jokes mapped to local equivalents, so I can preserve the laugh without a custom transcreation contract."
- "When deciding which of 12 markets to fund, I want an ROI/cultural-distance forecast, so I can spend where uplift is highest."

**Segment prioritization.** Primary: in-house localization ops + internationalization PMs at large streamers. Secondary: subtitling agencies. Deprioritized for V1: indie creators, non-entertainment enterprise content.

**Opportunity sizing.** SOM (V1) = the localized-hours volume of the design-partner streamer's non-EN slate. SAM = large/mid streamers' premium-localization spend. TAM = global media localization market. (Right-size to the design-partner contract for V1 business case.)

**Competitive landscape.** Generic MT (DeepL/Google) = accurate-but-flat, no resonance metric. Traditional LSPs/transcreation = high quality, slow, expensive, unmeasurable. Contexta's differentiation: **measurable intent-based localization + pre-commit ROI forecasting + continuous ALPO learning** — quality *with* a number attached.

---

## 4. Solution & Requirements ⭐

**⭐ Solution overview.** A multi-agent intent-based localization pipeline (CCKG Analyzer → Isomorphic Translator → Cultural Reviewer → Post-Editor) that maps humor/idiom/cultural mechanics to local equivalents, paired with (a) a Creative Testing Lab that quantifies resonance, (b) demand/ROI forecasting via a Cultural Distance Index, and (c) an ALPO human-in-the-loop loop that continuously improves the CCKG. Hyper-personalization (Localization Fingerprint) is a V2-leaning capability gated behind privacy review.

**Alternatives considered.**
- *Fine-tune one large model end-to-end* → rejected: less interpretable, harder to gate culturally, no per-stage quality control.
- *Human-only premium localization at scale* → rejected: cost/throughput impossible across the long tail.
- *Buy a third-party transcreation API* → rejected: no resonance measurement, no proprietary CCKG/ALPO moat.

**⭐ Functional requirements (MoSCoW).**

*Must*
- Multi-agent pipeline producing timed, line-broken subtitles with idiom/humor mapping.
- Cultural Reviewer agent + **human reviewer gate** for high-visibility titles.
- Creative Testing Lab: variant A/B (Literal vs. Idiomatic vs. Slang-heavy) with a resonance score.
- ALPO feedback capture → CCKG refinement pipeline.
- Per-title cost + throughput reporting.

*Should*
- Demand Forecasting: Cultural Distance Index + ROI projection per market.
- Reviewer workbench (accept/override/annotate) feeding ALPO.
- Glossary / do-not-translate / brand-term controls.

*Could*
- Hyper-personalization (Localization Fingerprint, cognitive-load adaptation) — privacy-gated.
- Biometric *simulation* (fixation/eye-tracking proxy) in the Testing Lab.

*Won't (V1)*
- Real viewer biometric capture; dubbing; live subtitling; external self-serve.

**⭐ User stories (samples w/ acceptance criteria).**
- *As a Localization Ops Manager, I want to run a title through the pipeline and get three styled variants, so that I can pick the best-resonating one.*
  - AC: variants generated within SLA; each shows a resonance score, timing-validity check, and reviewer-flag count; export to standard subtitle format (SRT/TTML).
- *As a Cultural Reviewer, I want to override a mapping and log why, so that ALPO learns from it.*
  - AC: override captured with reason code; change reflected in output; event written to ALPO training store.
- *As an Internationalization PM, I want a CDI + ROI forecast for a market, so that I can prioritize spend.*
  - AC: forecast returns CDI, projected resonance lift, and projected financial uplift vs. MT baseline, with a confidence band.

**User flows (happy path).** Ingest script/subtitle file → pipeline runs → variants + scores produced → human reviewer gate (for flagged/high-visibility content) → approve → export → (post-launch) viewer signal + reports flow back into ALPO.

**Edge/empty/error states.** Untranslatable pun with no isomorph (graceful fallback + reviewer flag); source transcript errors; unsupported language pair; reviewer disagreement deadlock; empty Testing Lab (no variants yet); pipeline agent timeout/partial failure.

**Non-functional requirements.** Throughput SLA per title-hour; subtitle timing/readability standards; reviewer-tool accessibility (WCAG 2.1 AA); data residency for client scripts; no leakage of pre-release content (NDA-grade security); localization of the *tool UI* for international ops teams.

**Data requirements.** Source scripts/subtitles (often pre-release, confidential), CCKG, reviewer feedback, resonance test results, viewer-level signal *only if* personalization is enabled (PII/consent gating). Retention and deletion aligned to client content agreements.

**Dependencies.** Internal: subtitle delivery pipeline, content catalog, viewer analytics. External: Gemini-powered agents, any third-party linguistic data sources, design-partner streamer's content access.

---

## 5. Scope & Prioritization ⭐

**⭐ In / out of scope.**
- *In:* multi-agent subtitle localization, Cultural Reviewer + human gate, Creative Testing Lab resonance scoring, ALPO loop, CDI/ROI forecasting, ops reporting.
- *Out:* dubbing, on-screen graphics, live subtitling, external self-serve, real biometrics, viewer personalization at GA (beta-flagged only).

**⭐ MVP / V1 definition.** Smallest version that tests the core hypothesis — *"intent-based localization measurably out-resonates MT"*: pipeline + Testing Lab resonance score + human reviewer gate + ALPO capture, on the launch language set, for a single design-partner streamer's slate.

**Prioritization rationale (RICE-style).**
- Pipeline + Reviewer gate: high reach (all titles), high impact (core value), high confidence, medium effort → **build first.**
- Testing Lab: high impact (it *is* the proof metric), medium effort → **build first.**
- CDI/ROI forecasting: medium reach (PMs), high strategic impact, lower confidence → **fast-follow.**
- Personalization: high impact but high effort + privacy risk + low confidence → **defer to V2.**

**Phasing.** V1: prove resonance lift on one partner slate. V1.5: CDI/ROI forecasting + reviewer workbench polish. V2: personalization (privacy-gated), broader language pairs, agency self-serve.

**Cut lines (drop first if timeline tightens).** Biometric simulation → CDI/ROI forecasting UI (keep the model, cut the polished UI) → multi-variant count reduced to two (Literal vs. Idiomatic).

---

## 6. Design & UX

- Wireframes/prototype: live app `[link]`; high-contrast dark "Control Center" aesthetic, data-dense.
- Design reviewed with design team `[schedule]`.
- Accessibility: WCAG 2.1 AA for the reviewer/ops tooling — contrast (already dark-theme-sensitive), keyboard nav, screen-reader labels on data tables and the variant comparison view.
- Content/microcopy: reason-code labels for overrides, clear "why this mapping" explanations, confidence-band wording for forecasts.
- Responsive/cross-platform: web-first (desktop ops workflow); read-only mobile review acceptable later.
- Design system reuse: shared component library for tables, score chips, approval cards.

---

## 7. Technical Considerations

- **⭐ Feasibility:** prototype exists and runs on Cloud Run; multi-agent orchestration + Gemini validated at prototype scale. Confirm production throughput/cost with eng.
- **Architecture:** agent orchestration layer (CCKG Analyzer, Isomorphic Translator, Cultural Reviewer, Post-Editor) → CCKG store → resonance scoring service → ALPO training pipeline. Link tech spec `[link]`.
- **API/integration:** subtitle ingest/export (SRT/TTML), content-catalog API, analytics events.
- **Data model / migration:** CCKG schema, reviewer-feedback store, resonance results store, ALPO training corpus.
- **Privacy/security review:** triggered — pre-release content is highly sensitive; viewer-level personalization data is PII and must be consent-gated.
- **Scalability/performance budget:** per-title-hour latency target for batch localization; near-real-time only if/when personalization ships.
- **Risks/unknowns (spikes):** isomorph coverage for distant language pairs; resonance-score validity vs. real engagement; LLM cost at slate scale.
- **Build vs. buy:** build CCKG + ALPO (the moat); buy/leverage foundation models (Gemini) for agent reasoning.

---

## 8. Risks, Assumptions & Open Questions ⭐

**⭐ Key assumptions (riskiest first).**
1. Resonance score correlates with real engagement/completion. *(Riskiest — validate against live A/B before scaling spend.)*
2. Isomorph mapping has adequate coverage across launch language pairs.
3. Design-partner streamer will share enough pre-release content + outcome data to train ALPO.

**⭐ Risks (likelihood × impact × mitigation).**
| Risk | L | I | Mitigation |
|---|---|---|---|
| Cultural misstep damages brand at scale | Med | **High** | Cultural Reviewer agent + mandatory human gate on high-visibility titles; reason-coded overrides |
| Resonance score doesn't predict engagement | Med | High | Validate against live A/B on a subset before claiming ROI |
| LLM/orchestration cost erodes the −50% cost target | Med | Med | Caching, batch processing, model-tier routing per stage |
| Pre-release content leak | Low | **High** | NDA-grade security, residency controls, access logging |

**Dependencies that could slip.** Foundation-model availability/cost; design-partner content access; analytics instrumentation readiness.

**⭐ Open questions (owner / due).**
- What's the validated resonance→engagement correlation? (Data Sci / before GA)
- Which language pairs make the V1 launch set? (Loc Ops / pre-build)
- Personalization consent model — opt-in default? (Legal / V2 gate)

**Ethical review.** Risk of homogenizing/altering creative intent; risk of stereotyping in "cultural equivalents." Mitigate with reviewer gating, creator-intent guardrails, and a bias review of CCKG mappings.

---

## 9. Measurement & Analytics

- **⭐ Instrumentation plan:** events for pipeline run, variant generated, resonance score, reviewer accept/override (+ reason), export, and post-launch subtitle-attributed engagement. Properties: title, language pair, variant style, score, reviewer ID, CDI.
- **Dashboards:** resonance lift vs. baseline, cost-per-localized-hour, throughput, reviewer override rate, per-market CDI/ROI — built before beta.
- **Experiment plan:** live A/B of Contexta variant vs. MT baseline on matched titles; hypothesis = +25% resonance and measurable completion lift; predefine sample size and significance threshold.
- **Feedback loops:** reviewer overrides + viewer "report inaccuracy" feed ALPO; periodic linguist QA panels.
- **"We'll know it worked" when:** treated titles show the §2 resonance lift *and* a real completion/retention delta vs. control.

---

## 10. Go-to-Market & Launch

- **⭐ Rollout:** feature-flagged, single design-partner streamer → expand title categories (start with dialogue-light, scale to comedy) → broaden language pairs.
- **Beta/pilot:** design-partner slate; entry = NDA + content access; exit = resonance + engagement targets met on ≥N titles.
- **Internal enablement:** train Localization Ops + Cultural Reviewers on the workbench; brief internationalization PMs on CDI/ROI.
- **External comms:** initially confidential (partner-only); later case study on resonance lift.
- **Support/docs:** reviewer workbench guide, override reason-code reference, forecast interpretation guide.
- **Pricing/packaging:** internal cost-recovery / per-localized-hour model for partners (decide with Finance).
- **Legal/compliance:** content-licensing + pre-release confidentiality sign-off.

---

## 11. Operational Readiness

- Monitoring/alerting on pipeline failures, agent timeouts, score anomalies.
- Rollback/kill-switch: disable auto-output and fall back to MT + human review per title.
- SLAs / on-call: localization delivery SLA owner + eng on-call for the pipeline.
- Load/failure testing at slate volume before GA.
- Backup/recovery for CCKG, reviewer corpus, and ALPO training data.

---

## 12. Stakeholders & Approvals ⭐

**⭐ RACI.**
| Activity | R | A | C | I |
|---|---|---|---|---|
| PRD & scope | Product DRI | Head of Product | Eng, Design, Loc Ops, Legal | Exec sponsor |
| Pipeline build | Eng Lead | Eng Director | Product, Data Sci | Loc Ops |
| Cultural quality gate | Loc Ops Lead | Head of Localization | Cultural Reviewers, Legal | Product |
| Launch | Product DRI | Exec sponsor | GTM, Support, Legal | Company |

- Cross-functional reviews: Eng · Design · Data · Legal · Security · Loc Ops · Finance.
- **⭐ Final sign-off:** Exec sponsor + Head of Localization + Eng Director.

---

## 13. Post-Launch

- Evaluate resonance + engagement vs. targets at **+1 week / +30 days / +90 days** per treated title.
- Retro: pipeline quality, reviewer load, cost actuals vs. −50% target, ALPO improvement rate.
- V2 backlog from real usage: personalization, more language pairs, agency self-serve, dubbing exploration.
- Decision log: double-down (expand partners) / iterate (fix coverage gaps) / sunset (if resonance lift doesn't convert to engagement).
- Update docs to reflect what actually shipped.
