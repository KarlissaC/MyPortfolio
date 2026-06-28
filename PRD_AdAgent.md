# PRD — Ad Agent: Autonomous AI CMO for SMB Meta Ads

> **Note on placeholders:** Owner names and doc links are `[TBD]` / `[link]`. Targets are PM-grade and tied to the product brief; calibrate to real baselines once instrumented.

---

## 0. Document Metadata

| Field | Value |
|---|---|
| **Title** | Ad Agent — Autonomous AI CMO for SMB Meta Advertising |
| **Author / DRI** | [TBD — Product DRI] |
| **Status** | Draft |
| **Version / Last updated** | v0.1 — 2026-06-23 |
| **Reviewers / approvers** | Eng Lead [TBD] · Design Lead [TBD] · Data Lead [TBD] · Trust & Safety [TBD] · Legal [TBD] · Exec Sponsor [TBD] |
| **Related docs** | Designs `[link]` · Tech spec `[link]` · Agent-safety design `[link]` · Meta API integration `[link]` · Tickets/epic `[link]` · ROAS dashboard `[link]` |
| **Target release** | V1 GA — Q4 2026 (closed beta Q3 2026) |
| **Live prototype** | https://ad-agent-982732324454.us-west1.run.app |

---

## 1. Problem & Context (Discovery) ⭐

**⭐ Problem statement.** SMB owners need professional-grade Meta advertising results but lack the time to learn Ads Manager and the budget for an agency — so they default to boosting posts and leave a large share of potential return on the table.

**⭐ Evidence the problem is real.**
- SMBs typically leave an estimated **30–50% of potential ROAS** unrealized by boosting posts instead of running structured campaigns (product brief; consistent with the well-known gap between boosted posts and managed campaigns).
- Agencies are expensive and often out of reach for small service businesses; Ads Manager is widely cited as too complex for non-specialists.
- The core tension is a **time-vs-expertise gap**: owners have neither hours nor platform fluency.

**Who is affected and how often.** Millions of SMB owners (med spas, salons, contractors, local services) who advertise continuously but manage it in stolen minutes; the pain recurs every campaign cycle and every budget decision.

**Why now.** LLM reasoning + image generation can now produce and optimize campaigns conversationally; agentic orchestration makes autonomous-with-approval workflows viable; and SMBs increasingly expect "chat to get it done" UX (WhatsApp-style).

**⭐ Strategic alignment.** Ladders to the goal of *democratizing professional marketing for SMBs* and an OKR around SMB activation/retention + ad-spend-under-management; aligns with a "community + connection" platform pillar.

**Cost of inaction.** SMBs keep wasting spend or churn off advertising entirely; we miss a large, underserved segment and the recurring revenue tied to spend-under-management.

**Existing workarounds.** Boosting posts (low efficiency); hiring a freelancer/agency (costly, variable); DIY Ads Manager (steep learning curve, high error/waste); doing nothing.

---

## 2. Goals & Success Metrics ⭐

**⭐ Business goals.** Acquire and retain SMB advertisers; grow ad-spend-under-management; build trust that justifies increasing autonomy over time.

**⭐ User goals (JTBD).**
- *SMB owner:* get agency-level ad results without learning the platform or spending hours.
- *Owner (trust):* be confident the AI won't waste money before handing it control.

**⭐ Success metrics / KPIs (baseline → target).**

| Metric | Baseline | V1 Target |
|---|---|---|
| Average ROAS | Boosted-post baseline (~2x typical) | **> 3.5x** (agency-level 3–5x band) |
| Approval rate after Shadow Mode | — | **> 70%** of recommendations approved |
| Time spent managing ads | Hours/week | **< 10 min/day** (≈ 5+ hrs/week saved) |
| Activation (first live campaign launched) | — | **[set after beta]** |
| Retention (still active at 90 days) | — | **[set after beta]** |

**North Star.** *Profitable advertising actively managed by Ad Agent* — accounts where the agent is running campaigns above their ROAS target.

**Guardrail metrics.** Budget-overrun incidents (must be ~zero — hard caps), spend anomalies caught by circuit breakers, false-positive anomaly alerts, creative-policy rejections by Meta, support contacts per active account.

**Counter-metrics.** ROAS achieved by under-spending (vanity efficiency, no volume), high approval rate driven by users rubber-stamping without understanding, "set-and-forget" accounts quietly losing money.

**⭐ Non-goals (V1).** Channels beyond Meta (Google, TikTok, etc.); fully autonomous spend with no approval (trust ladder starts with approvals); white-label/agency reseller tooling (later); e-commerce catalog/DPA complexity at GA; non-supported business categories.

---

## 3. Users & Market

**⭐ Personas.**
- **Service-business owner (primary):** med spa, salon, contractor — time-poor, non-technical, results-focused.
- **In-house SMB marketing manager (secondary):** some skill, wants leverage + speed.
- **Agency (white-label, later):** wants to automate execution across clients.

**Jobs-To-Be-Done.**
- "When I want more bookings this month, I want ads that just work, so I can grow without becoming a media buyer."
- "When the AI proposes spending my money, I want to see why and the expected impact, so I can approve with confidence."

**Segment prioritization.** Primary: local service businesses (clear ROAS story, simple offers). Secondary: in-house SMB managers. Later: agency white-label.

**Opportunity sizing.** SOM = beta-eligible SMB segment in launch geographies. SAM = SMBs running Meta ads who'd pay for automation. TAM = global SMB digital-ad management market. Size V1 business case to beta cohort + early conversions.

**Competitive landscape.** Meta Ads Manager + Advantage+ (powerful but complex/native), boosting (simple but weak), agencies/freelancers (effective but costly), other SMB ad tools (often dashboards, not autonomous agents). Differentiation: **autonomous AI CMO with approval workflow + Shadow-Mode trust-building + community benchmarking + conversational (WhatsApp-style) control.**

---

## 4. Solution & Requirements ⭐

**⭐ Solution overview.** An AI agent that plans, creates, and optimizes Meta campaigns; surfaces a daily CMO briefing; takes action only through an approval workflow (rationale + expected impact + Yes/No); generates ad copy + images; benchmarks against a community truth layer; and protects budget via Shadow Mode, circuit breakers, and hard caps.

**Alternatives considered.**
- *Full autonomy from day one* → rejected: trust + risk; users won't hand over spend blind. Approval workflow + Shadow Mode instead.
- *Dashboard-only "recommendations" tool* → rejected: doesn't close the time gap; users still must execute.
- *Multi-channel at launch* → rejected: depth on Meta first beats shallow everywhere.

**⭐ Functional requirements (MoSCoW).**

*Must*
- Meta campaign create/manage via Marketing API.
- Approval workflow cards (rationale / impact / Yes-No).
- Daily CMO Briefing (spend, conversions, target CPA, recommendations).
- Creative Studio: ad-copy variants (Gemini) + image generation (Imagen 3).
- Safeguards: Shadow Mode, circuit breakers, non-bypassable hard-cap daily budget.

*Should*
- WhatsApp-style chat command center with chat search.
- Voice control (STT) + briefing narration (TTS).
- Image review/feedback on uploaded creative.
- Community Insights: winning-ads gallery + benchmarking vs. community average.

*Could*
- Auto-scaling recommendations for winning campaigns.
- Creative-fatigue detection + refresh prompts.

*Won't (V1)*
- Non-Meta channels; spend without approval; agency white-label; complex e-commerce catalogs.

**⭐ User stories (samples w/ acceptance criteria).**
- *As an owner, I want a morning briefing I can listen to, so I can review performance while driving.*
  - AC: briefing shows spend/conversions/CPA + recommendations; TTS plays accurately; loads within SLA.
- *As an owner, I want to approve a scaling action with full rationale, so I stay in control.*
  - AC: card shows why + expected impact + Yes/No; on "Yes," change executes via Meta API and is logged; on "No," nothing spends.
- *As a cautious new user, I want Shadow Mode, so I can verify the AI before real spend.*
  - AC: agent shows simulated actions/outcomes with zero real spend; user can graduate to live mode explicitly.
- *As an owner, I want a hard daily cap, so the AI can never overspend.*
  - AC: cap is non-bypassable; circuit breaker halts + alerts on cost-spike anomalies.

**User flows (happy path).** Onboard + connect Meta + set hard cap → Shadow Mode (verify logic) → graduate to live → daily briefing → agent proposes actions → approval cards → approved changes execute → results + community benchmark feed next briefing.

**Edge/empty/error states.** Meta API/auth failure; ad rejected by Meta policy; sudden cost spike ("denial of wallet"); cap reached mid-day; no conversions yet (cold start); uploaded image fails review; voice/STT misrecognition; empty community benchmark (new category).

**Non-functional requirements.** Budget safety (hard caps enforced server-side), prompt-injection resistance, low-latency chat + briefing, reliability of approved-action execution, accessibility (WCAG 2.1 AA; voice/TTS aids access), data privacy for ad/account data, ad-policy compliance for AI-generated creative.

**Data requirements.** Meta account/campaign/performance data, user-uploaded creative, generated copy/images, community-benchmark aggregates (anonymized/opt-in), approval/audit history. Protect ad-account credentials/tokens; clear consent for community data sharing.

**Dependencies.** Internal: agent orchestration, briefing engine, safeguard layer, community-benchmark service. External: Meta Marketing API, Gemini (reasoning), Imagen 3 (visuals), TTS/STT providers, Cloud Run.

---

## 5. Scope & Prioritization ⭐

**⭐ In / out of scope.**
- *In:* Meta campaign management, approval workflow, daily briefing, Creative Studio, safeguards (Shadow Mode / circuit breakers / hard caps), chat command center, community benchmarking.
- *Out:* non-Meta channels, no-approval autonomy, white-label, complex commerce, unsupported categories (V1).

**⭐ MVP / V1 definition.** Smallest version that proves the core hypothesis — *"an approval-gated AI agent can deliver agency-level ROAS for SMBs in <10 min/day, and Shadow Mode earns enough trust to approve"*: Meta create/optimize + approval cards + daily briefing + Creative Studio + Shadow Mode/circuit breaker/hard cap, on supported service-business categories.

**Prioritization rationale (RICE-style).**
- Approval workflow + safeguards: high impact (trust + risk = the whole bet), must-have → **build first.**
- Meta campaign management + briefing: core value, high reach → **build first.**
- Creative Studio: high impact (removes a real bottleneck), medium effort → **build first/fast-follow.**
- Community Insights + voice/TTS: high delight, medium confidence → **fast-follow.**
- Auto-scaling/fatigue detection: high value, build *after* trust is established → **V1.5/V2.**

**Phasing.** V1: trustworthy approval-gated optimization + creative + safeguards. V1.5: community benchmarking, voice/TTS polish, fatigue detection. V2: graduated higher-autonomy modes, additional channels, white-label.

**Cut lines (drop first).** Community gallery → benchmark numbers only; voice/TTS → text-only briefing; image generation → copy-only Studio; multiple copy variants → single best variant.

---

## 6. Design & UX

- Wireframes/prototype: live app `[link]`; familiar WhatsApp-style chat as the primary surface to lower the learning curve.
- Design reviewed with design team `[schedule]`.
- Accessibility: WCAG 2.1 AA — voice + TTS already aid access; ensure approval cards are screen-reader friendly and Yes/No is keyboard-operable; captions for any audio.
- Content/microcopy: approval cards must make "why / what happens / cost" instantly legible; anomaly/circuit-breaker alerts must be calm and clear, not alarming-but-vague.
- Responsive/cross-platform: mobile-first (owners on the go); chat + briefing must work one-handed.
- Design system reuse: shared chat bubbles, approval cards, status chips, briefing layout.

---

## 7. Technical Considerations

- **⭐ Feasibility:** prototype live on Cloud Run; agent orchestration + Gemini + Imagen 3 + Meta API integration demonstrated. Confirm Meta API rate limits, token security, and safeguard enforcement with eng.
- **Architecture:** agent orchestration (reasoning + tool use) → Meta Marketing API abstraction layer → safeguard layer (hard caps, circuit breakers, Shadow Mode sandbox) → Creative Studio (Gemini + Imagen 3) → briefing/TTS + chat/STT → community-benchmark service. Tech spec `[link]`.
- **API/integration:** Meta Marketing API (campaigns, insights, creative), TTS/STT, image generation.
- **Data model:** account/campaign/performance store, approval/audit log, creative assets, community aggregates.
- **Privacy/security review:** triggered — ad-account tokens, spend authority, AI agent acting on user money; **prompt-injection and "denial of wallet" are first-class threats.**
- **Scalability/performance budget:** briefing generation latency, chat responsiveness, API call volume per account.
- **Risks/unknowns (spikes):** prompt-injection containment; Meta API edge cases/policy rejections; circuit-breaker tuning (sensitivity vs. false positives).
- **Build vs. buy:** build the agent orchestration + safeguard layer + Meta abstraction (the differentiator + risk surface); leverage Gemini/Imagen 3/TTS/STT.

---

## 8. Risks, Assumptions & Open Questions ⭐

**⭐ Key assumptions (riskiest first).**
1. Shadow Mode earns enough trust that >70% of users graduate and approve. *(Riskiest — the adoption hinge; test in beta.)*
2. The agent can actually hit 3–5x ROAS across varied SMB categories.
3. Owners will engage daily for <10 min rather than disengaging entirely.

**⭐ Risks (likelihood × impact × mitigation).**
| Risk | L | I | Mitigation |
|---|---|---|---|
| Prompt injection / buggy logic drains budget | Med | **High** | Hard-cap ceilings (server-enforced), circuit breakers, Meta API abstraction, injection-resistant prompting, approval gating |
| AI underperforms ROAS target → churn | Med | High | Conservative defaults, Shadow Mode proof, transparent reporting, human override |
| Meta API policy rejection of AI creative | Med | Med | Pre-flight policy checks, creative guidelines in generation, graceful resubmit |
| Over-trust → users rubber-stamp losses | Med | Med | Surface clear impact + periodic "are you still profitable?" nudges; counter-metric tracking |
| Circuit breaker false positives annoy users | Med | Low | Tune thresholds; explain alerts; easy resume |

**Dependencies that could slip.** Meta API access/approval; Imagen 3 / Gemini availability + cost; TTS/STT quality.

**⭐ Open questions (owner / due).**
- What ROAS baseline do we measure lift against per category? (Data / pre-beta)
- What's the Shadow-Mode → live graduation UX + criteria? (Product/Design / pre-build)
- How is community benchmark data sourced + anonymized + consented? (Legal/Data / pre-build)

**Ethical/unintended-consequence review.** Spending real SMB money autonomously is high-stakes; risks include misleading AI-generated claims, over-spend, and community-data privacy. Mitigate with approval gating, hard caps, ad-policy-aware generation, and opt-in anonymized benchmarking.

---

## 9. Measurement & Analytics

- **⭐ Instrumentation plan:** events for briefing view/listen, recommendation shown/approved/rejected, Shadow-Mode action, live graduation, campaign created/optimized, circuit-breaker trip, hard-cap hit, creative generated, community benchmark viewed. Properties: account, category, ROAS, CPA, spend, approval decision.
- **Dashboards:** ROAS distribution, approval rate post-Shadow-Mode, time-in-app, overrun/anomaly incidents, activation/retention — before beta.
- **Experiment plan:** A/B Shadow-Mode framings + approval-card designs; hypothesis = better trust framing lifts approval rate ≥70%; predefine sample + threshold.
- **Feedback loops:** in-chat feedback, approval/rejection reasons, support contacts, community signals.
- **"We'll know it worked" when:** active accounts exceed 3.5x ROAS, >70% post-Shadow-Mode approval, and time-in-app under 10 min/day with retained accounts.

---

## 10. Go-to-Market & Launch

- **⭐ Rollout:** closed beta (curated SMB categories) → feature-flagged GA → expand categories/geographies. Shadow Mode is the default entry state for every new account.
- **Beta/pilot:** entry = supported category + Meta account; exit = ROAS + approval-rate + retention targets on the cohort.
- **Internal enablement:** support playbook for billing/spend/anomaly questions; sales/marketing on the trust + ROAS story.
- **External comms:** changelog, in-app onboarding, marketing around "AI CMO that earns your trust before spending."
- **Support/docs:** setup + Meta-connect guide, Shadow-Mode explainer, safeguards/limits FAQ, creative-policy help, billing.
- **Pricing/packaging:** subscription and/or % of spend-under-management; decide with Finance (and ensure incentive alignment with the user's ROAS).
- **Legal/compliance:** Meta platform terms, AI-generated-content disclosure, ad-policy compliance, payment/authority terms.

---

## 11. Operational Readiness

- Monitoring/alerting: spend anomalies, circuit-breaker trips, Meta API errors/policy rejections, briefing/agent failures.
- Rollback/kill-switch: global + per-account pause of all agent spend; revert to read-only.
- SLAs / on-call: spend-safety incidents are P0; on-call owns budget-protection issues.
- Load/failure testing: Meta API failure modes, cap enforcement under load, circuit-breaker behavior.
- Backup/recovery: approval/audit log durability, token/credential security + rotation.

---

## 12. Stakeholders & Approvals ⭐

**⭐ RACI.**
| Activity | R | A | C | I |
|---|---|---|---|---|
| PRD & scope | Product DRI | Head of Product | Eng, Design, Data, Trust & Safety, Legal | Exec sponsor |
| Agent + safeguards | Eng Lead | Eng Director | Product, Trust & Safety | Support |
| Spend-safety / safeguard design | Trust & Safety Lead | Eng Director | Product, Legal | Exec |
| Creative generation policy | Product DRI | Legal | Design, Eng | Support |
| Launch | Product DRI | Exec sponsor | GTM, Support, Legal | Company |

- Cross-functional reviews: Eng · Design · Data · Trust & Safety · Legal · Support · Finance.
- **⭐ Final sign-off:** Exec sponsor + Eng Director + Trust & Safety (budget-safety is gating).

---

## 13. Post-Launch

- Evaluate vs. targets at **+1 week / +30 days / +90 days**: ROAS, approval rate, time-in-app, overrun incidents (target zero), retention.
- Retro: Shadow-Mode → live conversion, safeguard performance, false-positive rate, category-by-category ROAS.
- V2 backlog from real usage: higher-autonomy graduated modes, additional channels, white-label, fatigue/auto-scaling.
- Decision log: double-down (expand categories/channels) / iterate (improve ROAS or trust) / sunset (if ROAS or safety can't be met).
- Update docs to reflect what actually shipped.
