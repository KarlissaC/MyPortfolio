# PRD — Omni Shield Data: Synthetic EHR & Privacy Platform

> **Note on placeholders:** Owner names and doc links are `[TBD]` / `[link]`. Targets are PM-grade and tied to the product brief; calibrate to real baselines (especially TSTR AUROC and ε) once benchmarked.

---

## 0. Document Metadata

| Field | Value |
|---|---|
| **Title** | Omni Shield Data — High-Fidelity Synthetic EHR Platform |
| **Author / DRI** | [TBD — Product DRI] |
| **Status** | Draft |
| **Version / Last updated** | v0.1 — 2026-06-23 |
| **Reviewers / approvers** | Eng Lead [TBD] · Clinical Lead [TBD] · Privacy/Legal [TBD] · Security/CISO [TBD] · Compliance Officer [TBD] · Exec Sponsor [TBD] |
| **Related docs** | Designs `[link]` · Tech spec `[link]` · DP/ε methodology `[link]` · MIMIC-IV benchmark report `[link]` · Threat model `[link]` · Tickets/epic `[link]` |
| **Target release** | V1 GA — Q4 2026 (design-partner pilot Q3 2026) |
| **Live prototype** | https://omni-shield-data-982732324454.us-west1.run.app |

---

## 1. Problem & Context (Discovery) ⭐

**⭐ Problem statement.** Healthcare ML teams need realistic EHR data to build life-saving models, but accessing real patient data takes 6–9 months of privacy/compliance review and exposes hospitals to HIPAA risk — so most projects stall before they start, and existing synthetic tools produce disconnected rows that aren't clinically valid for training or audits.

**⭐ Evidence the problem is real.**
- Data-access cycles of **6–9 months** are standard for real EHR per the brief and well documented across health-system data-governance practice.
- Existing synthetic-data tools generate statistically marginal, **non-longitudinal** rows — unfit for clinical-grade ML or regulatory audit (no coherent patient journey).
- Buyer pain is concentrated and vocal: CISOs (risk), Heads of Data Science (velocity), Compliance Officers (auditability).

**Who is affected and how often.** Hospital data-science teams, health-AI startups, payer analytics groups, and clinical researchers — every time they start a new modeling project (continuous bottleneck), with severity highest where rare-disease or longitudinal modeling is required.

**Why now.** LLMs can now produce clinician-indistinguishable notes; differential privacy tooling is mature enough to give *provable* guarantees; and AI demand in healthcare is outpacing the legacy data-access cycle, making the bottleneck commercially painful.

**⭐ Strategic alignment.** Flagship enterprise-healthcare-AI bet; ladders to the company goal of *becoming the trusted data layer for regulated AI* and an OKR around enterprise healthcare revenue + compliance credibility.

**Cost of inaction.** Customers keep waiting months per project, or take on HIPAA exposure with real data; we forfeit a category-defining position in privacy-preserving healthcare data.

**Existing workarounds.** Long IRB/DUA processes for real data; de-identification (re-identification risk, utility loss); basic synthetic-row generators (low fidelity); public datasets like MIMIC (limited scope/representativeness).

---

## 2. Goals & Success Metrics ⭐

**⭐ Business goals.** Win enterprise healthcare accounts on trust + speed; establish a defensible compliance/auditability moat; collapse customers' time-to-model.

**⭐ User goals (JTBD).**
- *Data scientist:* go from "zero data" to a training-ready, clinically realistic dataset in hours, not months.
- *CISO / Compliance Officer:* prove privacy mathematically and produce an audit trail regulators accept.
- *Clinical researcher:* trust that synthetic timelines are medically consistent.

**⭐ Success metrics / KPIs (baseline → target).**

| Metric | Baseline | V1 Target |
|---|---|---|
| Time-to-data | 6–9 months | **< 48 hours** |
| Utility (TSTR AUROC vs. real-data baseline) | real-data model AUROC | **within 3%** of baseline |
| Privacy budget (ε) | — | **ε ≤ 1.0** at default |
| Clinical Turing Test (clinician can't distinguish) | — | **≥ 50% indistinguishable** in double-blind |
| Time-to-first-export (developer onboarding) | — | **< 1 day** |

**North Star.** *Validated synthetic datasets in production* — datasets that pass both the utility (TSTR) and privacy (ε) bars and are actually exported into a model pipeline.

**Guardrail metrics.** Re-identification risk (must stay below threshold), generation latency, export reliability, audit-log completeness, distribution-drift alerts (must not silently degrade).

**Counter-metrics.** Utility achieved by *quietly raising ε* (privacy traded away), notes that pass Turing tests but contain clinical contradictions, datasets that benchmark well but fail on rare-disease subgroups.

**⭐ Non-goals (V1).** Generating real/de-identified data (we generate synthetic only); imaging/genomics/waveform modalities; being a model-training platform (we feed PyTorch/TF/HF, we don't train); clinical decision support; non-US regulatory regimes beyond HIPAA/SOC2 at GA.

---

## 3. Users & Market

**⭐ Personas.**
- **Head of Data Science (primary economic + usage buyer):** wants velocity + utility.
- **CISO / Security (primary blocker-turned-champion):** wants provable privacy + Zero Trust.
- **Compliance Officer (primary approver):** wants reproducible, auditable datasets.
- **ML Engineer / Data Scientist (primary daily user):** wants API, exports, versioning.
- **Clinical researcher (validating user):** judges clinical realism.

**Jobs-To-Be-Done.**
- "When I start a new clinical-ML project, I want a realistic dataset now, so I can prototype without a 9-month data request."
- "When auditors review our model, I want a reproducible dataset with a cryptographic lineage, so I can prove provenance."

**Segment prioritization.** Primary: health-AI startups + hospital data-science teams (fastest adoption, clear pain). Secondary: payer analytics. Later: pharma/CROs.

**Opportunity sizing.** SOM = design-partner + early enterprise health-AI accounts. SAM = US healthcare ML data tooling spend. TAM = global regulated-industry synthetic-data market. Size the V1 business case to signed/pipeline enterprise accounts.

**Competitive landscape.** Manual de-identification (slow, risky), open datasets like MIMIC (narrow), generic synthetic-data vendors (not healthcare-longitudinal, weak compliance story). Differentiation: **longitudinal clinical realism + provable DP + audit-grade reproducibility (TSTR + ε + immutable ledger) in one platform.**

---

## 4. Solution & Requirements ⭐

**⭐ Solution overview.** A platform that generates longitudinal, causally-consistent synthetic EHRs (timelines + SOAP notes) under a tunable differential-privacy budget, validates them against real data (TSTR / MIMIC-IV benchmarking + clinician Turing tests), and exports them into ML workflows with cryptographic versioning and a HIPAA/SOC2 audit trail, all on Zero Trust infrastructure.

**Alternatives considered.**
- *Row-level synthetic generation* → rejected: no clinical coherence, fails longitudinal use cases.
- *De-identification of real data* → rejected: re-identification risk + slow governance, the exact problem we're solving.
- *Pure rules-based simulation (e.g., Synthea-style only)* → rejected alone: limited realism for notes/edge cases; we combine causal simulation + constitutional LLM notes + DP.

**⭐ Functional requirements (MoSCoW).**

*Must*
- Longitudinal Health Engine: causal timelines (Dx → labs → meds → procedures) that are medically consistent.
- Constitutional-LLM SOAP notes with guardrails (no hallucinated meds/contraindications).
- Differential Privacy layer with interactive **ε slider**, Laplace/Gaussian noise, outlier scrubbing.
- TSTR utility validation + MIMIC-IV benchmarking.
- Immutable, SHA-256-hashed dataset versioning + full audit log.
- Zero Trust security (mTLS, RBAC).

*Should*
- API playground + JSON schema explorer + batch simulation.
- One-click export to PyTorch / TensorFlow / Hugging Face.
- Clinical Turing Test workflow (clinician validation).
- Continuous safety monitoring (data poisoning, distribution drift).

*Could*
- Real-time TSTR feedback as ε changes.
- Configurable cohort/condition targeting.

*Won't (V1)*
- Imaging/genomics modalities; model training; non-HIPAA regimes; real-patient data handling.

**⭐ User stories (samples w/ acceptance criteria).**
- *As a data scientist, I want to generate a diabetic-cohort dataset and export to PyTorch, so I can train a model today.*
  - AC: timelines are clinically consistent (A1C trends → med adjustments); export completes; dataset carries a SHA-256 hash + version.
- *As a CISO, I want to set ε and see the privacy/utility tradeoff, so I can approve a budget.*
  - AC: ε slider adjusts noise; TSTR utility + privacy indicators update; the ε setting is logged immutably.
- *As a compliance officer, I want a reproducible dataset with lineage, so I can satisfy an audit.*
  - AC: every generation + export + ε change is in an immutable log; dataset can be reproduced from its hash.

**User flows (happy path).** Define cohort → set ε → generate timelines + notes → DP noise + outlier scrubbing → TSTR/benchmark validation → (optional) clinician Turing review → version + hash → export to ML framework → all actions logged.

**Edge/empty/error states.** Rare disease combo flagged for outlier scrubbing; ε set so low utility collapses (warn user); generation produces a clinical contradiction (guardrail catch + regen); benchmark fails threshold; export target unavailable; empty cohort definition.

**Non-functional requirements.** Provable privacy (DP guarantees), generation throughput/latency budgets, audit-log immutability + completeness, RBAC granularity, encryption in transit (mTLS) and at rest, HIPAA/SOC2 control coverage, API reliability, accessibility (WCAG 2.1 AA) for the console.

**Data requirements.** *No real PHI stored or output.* Inputs are configuration + (for benchmarking) approved reference datasets like MIMIC-IV under their data-use terms. Outputs are synthetic + hashed + logged. Retention/lineage governed by the immutable ledger.

**Dependencies.** Internal: generation engine, DP library, benchmark harness, ledger, RBAC/identity. External: foundation LLMs (constitutional fine-tune), MIMIC-IV access terms, cloud (Cloud Run), ML framework export targets (PyTorch/TF/HF).

---

## 5. Scope & Prioritization ⭐

**⭐ In / out of scope.**
- *In:* longitudinal generation, SOAP notes, DP/ε layer, TSTR + MIMIC-IV validation, versioning/ledger, Zero Trust, API + exports, safety monitoring.
- *Out:* imaging/genomics, model training, real-patient data, clinical decision support, non-HIPAA regimes (V1).

**⭐ MVP / V1 definition.** Smallest version that proves the core hypothesis — *"synthetic data can be both clinically useful (TSTR within 3%) and provably private (ε ≤ 1.0), produced in < 48h, with audit-grade lineage"*: Longitudinal Engine + SOAP notes + ε slider + TSTR/MIMIC validation + hashed versioning/audit log + one export path (e.g., Hugging Face), on a focused condition set.

**Prioritization rationale (RICE-style).**
- Longitudinal Engine + DP layer: core value, high impact, high effort → **build first** (it *is* the product).
- TSTR + ledger: high impact (these create trust + auditability), medium effort → **build first.**
- API playground + multi-framework export: high reach (developers), medium effort → **fast-follow** (start with one framework).
- Real-time TSTR-on-ε + cohort targeting: high delight, medium confidence → **V1.5/V2.**

**Phasing.** V1: prove utility + privacy + auditability on a focused condition set, single export path. V1.5: full API playground, three export targets, Turing-test workflow, drift/poisoning monitoring. V2: cohort targeting, broader conditions, additional compliance regimes.

**Cut lines (drop first).** Multi-framework export → single framework; biometric/Turing UI polish → CSV-based clinician review; real-time ε feedback → on-demand recompute.

---

## 6. Design & UX

- Wireframes/prototype: live app `[link]`; enterprise console aesthetic, data + control dense.
- Design reviewed with design team `[schedule]`.
- Accessibility: WCAG 2.1 AA — the ε slider needs keyboard operability + clear numeric readout + screen-reader labeling of the privacy/utility tradeoff.
- Content/microcopy: plain-language explanation of ε ("lower = more private, more noise"), audit/lineage language regulators trust, guardrail-catch messaging.
- Responsive/cross-platform: web console first; API is the primary "interface" for daily users.
- Design system reuse: shared components for tables, control panels, status/validation chips.

---

## 7. Technical Considerations

- **⭐ Feasibility:** prototype live on Cloud Run; causal generation + DP + benchmarking demonstrated. Confirm production-scale generation cost/latency + DP correctness with eng + a privacy expert.
- **Architecture:** generation engine (causal simulator + constitutional LLM) → DP/noise + outlier scrubbing → validation harness (TSTR, MIMIC-IV) → versioning/ledger (SHA-256, immutable) → export adapters → audit/logging. Tech spec `[link]`.
- **API/integration:** generation + export endpoints, batch jobs, JSON schemas; PyTorch/TF/HF adapters.
- **Data model / migration:** synthetic-record schema, dataset-version registry, immutable audit ledger, benchmark store.
- **Privacy/security review:** **mandatory** — formal DP guarantee review, re-identification testing, threat model, RBAC design, mTLS.
- **Scalability/performance budget:** batch-generation throughput targets; export streaming reliability.
- **Risks/unknowns (spikes):** DP guarantee correctness under composition (repeated queries); utility on rare-disease subgroups; clinical-consistency validation at scale.
- **Build vs. buy:** build the generation engine + ledger + compliance harness (the moat); leverage foundation LLMs (constitutional fine-tune) + established DP libraries.

---

## 8. Risks, Assumptions & Open Questions ⭐

**⭐ Key assumptions (riskiest first).**
1. We can hit TSTR within 3% *while* holding ε ≤ 1.0. *(Riskiest — the central utility/privacy tension; validate early on real benchmarks.)*
2. Clinicians will accept the notes as indistinguishable in double-blind tests.
3. Buyers (CISO/Compliance) will accept our audit trail as regulator-grade.

**⭐ Risks (likelihood × impact × mitigation).**
| Risk | L | I | Mitigation |
|---|---|---|---|
| Clinical accuracy drift on rare-disease combos | Med | **High** | Outlier scrubbing + clinician-in-the-loop review; subgroup TSTR reporting |
| Privacy guarantee challenged / re-identification | Low | **High** | Formal DP proof, re-id testing, conservative default ε, external audit |
| Utility/privacy tradeoff can't meet both targets | Med | High | Tune generation, document the frontier, let buyers set ε with clear tradeoff |
| Compliance positioning misjudged (HIPAA/SOC2) | Med | High | Early legal + auditor engagement; SOC2 readiness program |
| Distribution drift makes data stale vs. real practice | Med | Med | Continuous drift monitoring + re-benchmark cadence |

**Dependencies that could slip.** SOC2 audit timeline; MIMIC-IV access terms; foundation-model availability; clinician reviewer availability.

**⭐ Open questions (owner / due).**
- What default ε balances utility + privacy for the launch condition set? (Privacy lead / pre-build)
- Which conditions are in the V1 set? (Clinical lead / pre-build)
- What audit artifacts do target customers' auditors actually require? (Compliance / pre-pilot)

**Ethical review.** Risk of synthetic data encoding/amplifying real-world clinical bias; risk of misuse (passing synthetic off as real). Mitigate with subgroup fairness reporting, clear synthetic-data labeling, and usage terms.

---

## 9. Measurement & Analytics

- **⭐ Instrumentation plan:** events for generation request, ε setting, validation result (TSTR/benchmark), guardrail catch, export, dataset version/hash, audit-log write. Properties: cohort, ε, TSTR delta, framework, user/role.
- **Dashboards:** time-to-data, TSTR-vs-baseline, ε distribution, re-id-risk indicator, drift alerts, export success rate, audit completeness — before pilot.
- **Experiment plan:** TSTR audits (train on synthetic, test on real) per condition; double-blind clinician Turing test with predefined indistinguishability threshold.
- **Feedback loops:** clinician review results + customer model performance feed generation/guardrail tuning.
- **"We'll know it worked" when:** customers reach training-ready data in < 48h, models trained on Omni Shield hit TSTR within 3%, and compliance sign-off is achieved at default ε ≤ 1.0.

---

## 10. Go-to-Market & Launch

- **⭐ Rollout:** design-partner pilot (1–2 enterprise accounts) → controlled GA via feature flags + per-tenant RBAC → broaden condition coverage.
- **Beta/pilot:** entry = signed agreement + security review; exit = utility/privacy/compliance targets met + auditor acceptance.
- **Internal enablement:** sales/solutions on the privacy + compliance story; support on API/exports; clinical liaison for Turing-test framing.
- **External comms:** benchmark report (TSTR + ε), compliance whitepaper, developer docs; positioning as the "Privacy Firewall for healthcare."
- **Support/docs:** API reference, ε/DP explainer, export guides, audit/lineage runbook, security/Zero-Trust overview.
- **Pricing/packaging:** enterprise + usage (generated volume / seats); decide with Finance.
- **Legal/compliance:** HIPAA posture, SOC2 readiness, MIMIC-IV use terms, customer DPA/security questionnaires.

---

## 11. Operational Readiness

- Monitoring/alerting: generation failures, guardrail catches, drift/poisoning alerts, export errors, audit-log gaps.
- Rollback/kill-switch: disable generation/export per tenant; revoke a dataset version.
- SLAs / on-call: platform availability SLA; security on-call; clinical-escalation path.
- Load/failure testing: batch-generation + export at enterprise volume.
- Backup/recovery: immutable ledger durability, dataset reproducibility from hash, key management.

---

## 12. Stakeholders & Approvals ⭐

**⭐ RACI.**
| Activity | R | A | C | I |
|---|---|---|---|---|
| PRD & scope | Product DRI | Head of Product | Eng, Clinical, Privacy, Security, Compliance | Exec sponsor |
| Generation engine | Eng Lead | Eng Director | Clinical, Data Sci | Product |
| Privacy / DP guarantee | Privacy Lead | CISO | Eng, Legal, external auditor | Product |
| Compliance (HIPAA/SOC2) | Compliance Officer | CISO/Legal | Eng, Product | Exec |
| Launch | Product DRI | Exec sponsor | GTM, Support, Legal, Security | Company |

- Cross-functional reviews: Eng · Design · Data/Clinical · Legal · Security · Compliance · Finance.
- **⭐ Final sign-off:** CISO + Compliance Officer + Eng Director + Exec sponsor (privacy + compliance are gating).

---

## 13. Post-Launch

- Evaluate vs. targets at **+1 week / +30 days / +90 days**: time-to-data, TSTR delta, ε, clinician indistinguishability, export reliability.
- Retro: where utility/privacy tension bit, subgroup performance, audit acceptance, pilot conversion.
- V2 backlog from real usage: cohort targeting, more conditions/modalities, additional compliance regimes, real-time ε feedback.
- Decision log: double-down (expand accounts/conditions) / iterate (fix subgroup gaps) / sunset (if utility/privacy frontier can't satisfy buyers).
- Update docs to reflect what actually shipped.
