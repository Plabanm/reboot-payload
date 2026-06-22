# Build Prompt — TrustPass Architecture Document (detailed, diagram-rich)

> **Paste everything below into your AI model** to generate a **comprehensive technical & solution architecture document**
> for **TrustPass — Digital Identity & Data Vault** (a UK / Lloyds Banking Group–style product).
> Output a single, polished **architecture document** (default: self-contained **`architecture.html`**; also acceptable as a long-form **Markdown** doc).
> It must be **diagram-rich**, **honest about trade-offs**, and consistent with the product's design system and the React/JSON prototype.

---

## 0) Context — what TrustPass is (read first)

TrustPass is a **mobile-first digital identity & data vault** that lets a customer **verify once and reuse everywhere**.
The customer holds **reusable verified proofs** (identity, address, income, affordability, residency, deposit, sanctions, age, credit, risk) sourced from trusted providers
(**Gov.UK One Login, HMRC, Open Banking, Credit Reference Agency, Royal Mail PAF, Employer/Payroll**).
When a customer starts a **journey** (mortgage, personal loan, green savings, home insurance, credit card),
an **AI coach guides and explains** (*guidance only — never the decision-maker*), the customer **consents with selective disclosure**
(*share verifiable claims, not raw documents*), a **deterministic rules engine** makes the decision (*same inputs → same outcome, fully auditable*),
and on success a **verifiable-credential token** plus a **tokenised asset** (e.g. Mortgage Offer, Green Bond) is issued back into the wallet.
Journeys can start **inside the app** or **outside the app** (a third-party app — “NorthBank” — requests a **time-limited, revocable handshake**),
and a **cold-start** flow onboards a brand-new customer and auto-populates their vault.
**TrustPass is product-agnostic** — it is the trust/identity/data layer, not a single banking product.

**Core principles to thread through the whole document:**
1. **Verify once, reuse everywhere.** 2. **Share claims, not documents** (selective / zero-knowledge-style disclosure).
3. **The AI guides; the deterministic rules engine decides** (explainability + auditability). 4. **Customer stays in control** (consent, time-limited, revocable, least-privilege). 5. **Privacy & data minimisation by design.** 6. **Honest, explainable outcomes** — including a clear, constructive **“no”**.

---

## 1) Document goals & audience

Produce an architecture document that satisfies **three audiences at once**:
- **Hackathon judges / executives** — clear value, vision, differentiators, “before vs after”.
- **Solution architects / engineers** — concrete components, data flows, sequence diagrams, APIs, data model, security model, trade-offs.
- **Risk / compliance / privacy reviewers** — trust model, consent & revocation, data minimisation, auditability, regulatory alignment.

The doc must be **detailed and specific** (no hand-waving), **diagram-led**, and **honest** about assumptions, trade-offs, risks and what is mocked in the prototype vs. real in production.

---

## 2) Required sections (use ALL of these, in this order)

> Each section lists what to cover. Be thorough; include diagrams where indicated.

### 1. Executive summary
- 4–6 sentences: the problem, the TrustPass solution, the headline outcome (**“weeks → minutes”**, paperless, reusable proofs), and why it matters to a bank.
- A compact **before vs after** table (TODAY vs TRUSTPASS) across: time, documents, data exposure, reusability, customer control, fraud surface, carbon.

### 2. Problem statement & opportunity
- Today's pain: repeated KYC, document re-uploads, weeks of back-and-forth, oversharing of sensitive data, siloed proofs per product, drop-off, fraud, paper.
- The opportunity: a reusable, customer-held identity & data layer that any product (in-house **or** third-party) can consume with consent.
- Quantify where possible (illustrative metrics are fine; label them as illustrative).

### 3. Vision & guiding principles
- Expand the six core principles (§0). For each: what it means, how the architecture enforces it, and how the UI surfaces it.

### 4. Capability / domain model
- The product as a set of capabilities: **Identity Vault, Data Vault (connected sources), Wallet (payments), Credentials/Proofs, Journeys/Orchestration, AI Guidance, Rules/Decisioning, Tokenisation/Assets, Consent & Handshake, Sustainability/Impact.**
- A **capability map diagram**.

### 5. Information architecture (the app)
- The **5-tab IA**: Home, Wallet, **Identity** (with **Data** & **Address** as sub-pages, plus Doc detail), **Assets**, **Journeys** (in-app + outside-app). Agents are surfaced as **offers**, not a tab.
- An **IA / sitemap diagram** of all ~23 routes and how sub-routes map to tabs.

### 6. Logical architecture (layers)
- A **layered architecture diagram (5 layers)**:
  1. **Experience layer** — React mobile-first app (phone frame), routing, state, localStorage.
  2. **Orchestration layer** — journey engine, AI coach (guidance), consent & selective disclosure.
  3. **Decisioning layer** — **deterministic rules engine** (auditable), readiness scoring (AI estimate, non-binding).
  4. **Trust & credential layer** — issuer, verifiable credentials/tokens, presentations, tokenised assets, DID/audience, TTL & revocation.
  5. **Data & integration layer** — connectors to Gov.UK One Login, HMRC, Open Banking (multi-bank), CRA, Royal Mail PAF, Payroll.
- For each layer: responsibilities, key components, and **prototype-vs-production** notes (what is mocked now).

### 7. Component architecture
- A **component diagram** naming concrete modules: `AppStore` (state), `Router`, route screens, `mockApi`, `JourneyEngine`, `AICoach`, `RulesEngine`, `CredentialIssuer`, `PresentationBuilder`, `AssetMinter`, `ConsentManager`, `HandshakeService`, `ConnectorRegistry`, `CarbonMeter`.
- Responsibilities and collaborators for each. Note which are real in the prototype vs. service stubs.

### 8. Key data flows / sequence diagrams (the heart of the doc)
Provide **clear sequence diagrams** (Mermaid `sequenceDiagram` or equivalent) for **each** flow below. Show actors: Customer, App, AI Coach, Consent, Rules Engine, Issuer, Data Sources, Third-party.

- **8a. In-app happy journey** (e.g. Mortgage): Offer + readiness → Coach (guidance) → Consent (selective disclosure) → Rules engine (all pass) → Token + tokenised asset issued.
- **8b. Outside-app handshake** (NorthBank): third-party requests proofs → TrustPass shows scoped request → consent → **time-limited verifiable presentation** sent (audience DID, TTL) → auto-revoke on expiry.
- **8c. Cold-start onboarding**: new customer signs up (minimal data) → vault **bootstraps** by pulling verified data from connected sources → first journey enabled.
- **8d. Cross-journey reuse**: a held token (e.g. Mortgage Readiness) is reused to **fast-track** another journey — rules engine checks only the **delta**.
- **8e. UNHAPPY paths (REQUIRED):**
  - **Rules-engine DECLINE/REFER** (Credit Card): a rule (affordability) fails → final outcome DECLINE → **no asset issued**, a **decision receipt** + AI guidance on *why* and *when to re-apply* (readiness `wait` state).
  - **Consent declined** / required proof toggled off → nothing shared, no rules run.
  - **Stale / missing data source** → journey blocked until refreshed (or weaker/declined outcome if continued).
  - **Handshake deny / TTL expiry** → presentation auto-revoked, nothing shared, third-party must re-request.

### 9. Decisioning: AI vs deterministic rules (explainability)
- Crisp separation of concerns: **AI = guidance, translation, readiness estimate, agent offers** (probabilistic, non-binding); **Rules engine = the decision** (deterministic, versioned, auditable, reproducible).
- Why this matters for **regulation, fairness, contestability, and trust**.
- Describe rule structure (inputs = consented claims; output = pass/fail per rule + overall verdict), **rule versioning**, and how a decision is **explained** to the customer (the “why” for a decline).
- Note bias / model-risk considerations for the AI layer, and that the AI **cannot** override the rules engine.

### 10. Trust, credentials & tokenisation model
- **DID / issuer model** (`did:trustpass:lbg`), verifiable credentials vs verifiable presentations.
- **Selective disclosure** — sharing a *claim* (“income band £60k–£80k”, “age ≥ 18”) rather than the underlying document/value; mention ZKP-style disclosure as the production direction.
- **Tokens** (e.g. Mortgage Readiness Token / MRT) vs **tokenised assets** (Mortgage Offer, Green Savings Bond) — what each represents, lifecycle, serial/time-stamp, revocability, transferability.
- **TTL & revocation** — time-limited presentations, audience binding, auto-revoke, audit trail.
- A **token/asset lifecycle diagram** (issue → hold → present → reuse → expire/revoke).

### 11. Data architecture & integration
- The **verified-proof data model** (the 10 credentials, issuers, freshness) and the **connector catalogue** (Gov.UK One Login, HMRC, Open Banking ×3 banks, CRA, Royal Mail PAF, Payroll) with `active/stale/off` states and refresh semantics.
- **Prototype reality:** all data is **static JSON** loaded client-side; document the mapping to real **APIs / standards** in production (Open Banking / OBIE, Gov.UK One Login, HMRC APIs, OpenID for Verifiable Credentials, W3C VC/DID, FAPI).
- **Data minimisation, retention, residency** notes. Where data lives (customer-held vault vs. provider source-of-truth).
- A **data-flow / integration diagram**.

### 12. Security & privacy architecture
- **Threat model** (STRIDE-style summary): impersonation, replay, oversharing, token theft, consent forgery, source spoofing — and the mitigations (DID binding, TTL, audience scoping, least privilege, signed presentations).
- **Consent model** — granular, revocable, logged; least-privilege scopes.
- **Auditability** — every share/decision produces an event in the log; deterministic decisions are reproducible.
- **AuthN/AuthZ**, key management, and what changes from prototype → production.
- **Privacy-by-design** mapping (data minimisation, purpose limitation, customer control) — align to **GDPR / UK GDPR** principles.

### 13. Non-functional requirements & quality attributes
- Performance, scalability, availability, resilience, accessibility (WCAG AA — the app already targets this), observability, maintainability, internationalisation. State concrete targets where reasonable.

### 14. Technology choices & rationale
- Prototype: **React 18 + Vite + TypeScript, frontend-only, JSON data, HashRouter, Context+reducer, localStorage** — and **why** (fast, shareable, no backend needed for a demo).
- Production direction: API gateway, credential/issuer service, rules service, connector adapters, event store/audit log, consent service, mobile apps — with a short justification each.
- A clear **“prototype vs production” comparison table**.

### 15. Deployment & runtime view
- Prototype: static hosting (any static host / file), no server.
- Production (conceptual): a deployment diagram with mobile clients, API/edge, microservices (issuer, rules, consent, connectors), data sources, audit store. Note multi-region/HA conceptually.

### 16. Sustainability & business impact
- Paperless / fewer branch trips / fewer manual checks → the **carbon meter** concept; reusability reduces duplicated verification cost.
- Business value: faster onboarding, lower drop-off, lower fraud, reusable infrastructure across products and third parties, new B2B2C revenue (TrustPass as a trust layer others consume).

### 17. Risks, assumptions, open questions & trade-offs
- Be candid: what is **mocked** vs real, regulatory dependencies, adoption/standards risk, liability for shared claims, third-party trust onboarding, model risk for AI guidance.
- A **trade-offs table** (e.g. customer-held vault vs central store; deterministic rules vs ML decisioning; selective disclosure complexity vs privacy gain).

### 18. Roadmap / maturity model
- Phased plan: **Phase 1** prototype (now) → **Phase 2** real connectors + issuer + rules service → **Phase 3** third-party ecosystem + standards (OpenID4VC) + cross-org reuse → **Phase 4** scaled trust network. A simple **roadmap diagram/timeline**.

### 19. Glossary
- Define: DID, VC, VP, selective disclosure, ZKP, TTL, claim vs document, rules engine, readiness score, tokenised asset, handshake, cold-start, connector, Open Banking, One Login, FAPI, OBIE.

---

## 3) Diagram requirements

Include **at least these diagrams**, rendered (prefer **Mermaid** so they render in Markdown/HTML, or clean inline **SVG** if output is HTML):
1. Capability map (§4)
2. IA / sitemap (§5)
3. 5-layer logical architecture (§6)
4. Component diagram (§7)
5. **Sequence diagrams** for 8a–8e (happy, handshake, cold-start, reuse, **unhappy decline**) (§8)
6. Token/asset lifecycle (§10)
7. Data-flow / integration (§11)
8. Threat-model / security overview (§12)
9. Deployment view (§15)
10. Roadmap timeline (§18)

Diagrams must be **labelled, legible, and consistent** with the colour semantics below.

---

## 4) Visual / design system (match the product)

Apply these **exact** tokens and **semantic colour meanings** to the document (headers, callouts, diagram nodes, legends):

```
Brand greens:  --green:#006A4D  --green2:#04875F  --greenDeep:#013528  --greenInk:#0A2C22
Surfaces:      --mint:#E7F3EC   --soft:#F4F9F6    --paper:#FFFFFF
Ink/neutral:   --ink:#0E211C    --muted:#69756F   --line:#E4ECE7
AI = blue:     --ai:#1C6EC2     --aiBg:#E9F2FC
Rules = purple:--rules:#6E4BD0  --rulesBg:#EFEAFB
Tokens/assets = orange-gold: --token:#D9842A  --tokenBg:#FCEFDD  --assetCream:#FBF1DD
Third-party = terracotta: --tp:#C24E2E  --tpBg:#FBEAE3
Danger:        --danger:#C0392B
```
**Semantic mapping (use consistently in every diagram & callout):**
**green = wallet / brand / frontend**, **blue = AI guidance**, **purple = rules engine / decisioning**, **orange-gold = tokens & tokenised assets**, **terracotta = third-party / outside-the-app**, **red = decline / risk**.

- **Font:** Inter (with system fallback). Clean, generous spacing, rounded cards, soft shadows — premium fintech feel.
- **Logo:** generic Lloyds-style **prancing-horse** mark (or unicode **♞**) in a white rounded square; title **“TrustPass”**, subtitle **“Digital Identity & Data Vault”**. Keep it **self-contained** (inline SVG / emoji fallback) — no external image dependencies.
- If output is HTML: a sticky/section nav, a legend for the colour semantics, and a print-friendly layout.

---

## 5) Style, depth & format rules

- **Be detailed and concrete** — name components, claims, connectors, routes, and decisions; avoid generic filler.
- **Diagram-led** — every major section should have or reference a diagram.
- **Honest** — clearly separate *prototype (mocked)* from *production (proposed)*; include trade-offs, risks and assumptions.
- **Consistent** — colour semantics, terminology and the six principles thread throughout.
- Use tables for comparisons (before/after, prototype/production, trade-offs).
- Keep the **AI-guides / rules-decide** separation crisp wherever decisioning appears.
- **Output:** one self-contained **`architecture.html`** (preferred) *or* a single long-form **Markdown** file. No external runtime dependencies; if using Mermaid in HTML, inline it or note the CDN.

---

## 6) Acceptance criteria (the doc is done when…)

- [ ] All **19 sections** (§2) are present, in order, with real depth.
- [ ] All **10 diagram types** (§3) are included and legible, incl. **sequence diagrams for happy, handshake, cold-start, reuse, and the unhappy decline**.
- [ ] **AI-vs-rules** decisioning is clearly separated and justified for explainability/regulation.
- [ ] **Trust/credential/tokenisation** model (DID, VC/VP, selective disclosure, TTL, revocation, tokenised assets) is fully described with a lifecycle diagram.
- [ ] **Security & privacy** section includes a threat model and consent/revocation/auditability.
- [ ] **Prototype-vs-production** is explicit throughout, with a comparison table and honest trade-offs/risks.
- [ ] Colour **semantics and tokens** from §4 are applied consistently; document is **self-contained**.
- [ ] Tone works for **judges, engineers, and risk/compliance** simultaneously.

---

**Deliver the complete architecture document now** — detailed, diagram-rich, honest, and visually consistent with the TrustPass design system.
