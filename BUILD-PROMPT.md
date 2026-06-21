# Master Build Prompt — “TrustPass: Digital Identity & Data Vault”

> Copy everything below the line into another AI model (Claude, GPT, Gemini, etc.) to recreate the product from scratch. It is split into **Part A (Prototype)** and **Part B (Architecture Document)**. You can paste both at once, or Part A first then Part B.

---

## ROLE & GOAL

You are a senior product engineer + UX designer building a **hackathon demo** for a **Lloyds Banking Group** challenge on **Digital Identity (ID Wallets) and Tokenised Currencies/Assets**.

Produce **two deliverables**, each a **single, self-contained HTML file** (inline CSS + vanilla JS, no frameworks, no build step, no external dependencies except an optional Google Font that must gracefully fall back):

1. **`index.html`** — a mobile-first, fully interactive **prototype**.
2. **`architecture.html`** — a richly detailed **architecture document** (visual, technical, narrative).

Both must work by **double-clicking the file** (open offline in any modern browser). Embed any images as base64 data-URIs or pure CSS/SVG. Do **not** rely on local image files.

---

## THE PRODUCT (concept)

**TrustPass** is a **digital identity & data vault**. A customer verifies their identity and connects trusted data sources **once**, then holds **reusable verified proofs** (Verifiable Credentials). For any financial journey:

- An **AI assistant guides** the customer (explains the journey, requests only the minimum proofs, gives a “readiness” score and **proceed-or-wait** advice + improvement tips) — but **AI never makes the decision**.
- A **deterministic rules engine decides** eligibility (same inputs → same outcome, explainable, auditable).
- On approval, the wallet receives a **token** (proof of eligibility) **and a tokenised asset with real value** (e.g. a Green Savings Bond, a Mortgage Offer).

**Core principles to dramatise throughout the UI:** data minimisation, selective disclosure (share a *claim* like “income band £60–80k”, never the raw document or exact figure), consent, revocability, time-limited access, full auditability, and **AI-guides-but-rules-decide** separation.

**Product-agnostic & reusable:** the vault works across mortgages, loans, savings, insurance, passports/visas, etc. Crucially, **journeys are the priority and can start in two modes:**
- **In-app** (customer browses an offer inside TrustPass), and
- **Outside the app** (a third-party app requests proofs via a secure handshake; OR a brand-new customer with *no wallet* is led in via a product and the vault builds itself).

---

# PART A — THE PROTOTYPE (`index.html`)

## A1. Design language (use these EXACT values)

**Brand:** clean, premium, trustworthy fintech. Deep-green Lloyds-style palette. Mobile-first. On desktop, render inside a centered **phone frame** (max-width ~420px, rounded 44px, dark border, fixed height ~880px) on a dark green radial-gradient backdrop.

**CSS custom properties (`:root`) — copy verbatim:**
```css
:root{
  --green:#006A4D; --green2:#04875F; --greenDeep:#013528; --greenInk:#0A2C22;
  --mint:#E7F3EC; --mint2:#D6EBDF; --soft:#F4F9F6; --paper:#FFFFFF;
  --ink:#0E211C; --ink2:#2C3A35; --muted:#69756F; --faint:#9AA6A0; --line:#E4ECE7; --line2:#EFF4F1;
  --ai:#1C6EC2; --aiBg:#E9F2FC; --aiInk:#0C4C8C;        /* AI = BLUE */
  --rules:#6E4BD0; --rulesBg:#EFEAFB; --rulesInk:#3F2596; /* Rules = PURPLE */
  --token:#D9842A; --tokenBg:#FCEFDD; --tokenInk:#8A5212; /* Tokens/Assets = ORANGE */
  --assetGreen:#E3ECE4; --assetCream:#FBF1DD;            /* asset card fills */
  --danger:#C0392B; --tp:#C24E2E; --tpBg:#FBEAE3;        /* third-party = terracotta */
  --r-lg:24px; --r-md:18px; --r-sm:12px;
  --sh-sm:0 2px 8px rgba(8,46,34,.06);
  --sh-md:0 10px 30px rgba(8,46,34,.10);
  --sh-lg:0 24px 60px rgba(8,46,34,.20);
}
```
**Colour-coding is semantic and MUST be consistent:** Wallet/frontend = **green**; AI assistance = **blue**; Rules/policy = **purple**; Tokens & assets = **orange/gold**; third-party/external entry = **terracotta**.

**Typography:** Inter (Google Fonts) with system fallback `system-ui, Segoe UI, Roboto, Arial, sans-serif`. Headings 800–900 weight, tight letter-spacing. Body 12.5–14px. Tiny labels ≥10px (don’t go smaller for accessibility).

**Components:** rounded cards (`--r-md`) with soft shadows; pill badges (variants for ai/rules/token/tp); gradient primary buttons (green), plus `.ghost`, `.ai`, `.tp`, `.dark` variants; list “tiles” with an icon chip + title + subtitle + chevron; a fixed bottom **tab bar**; a toast; spinners; a confetti burst on success. Add subtle fade-in on route change and a card-carousel for payment cards & tokens.

**Logo:** a simple Lloyds-style black horse glyph is fine — use the unicode `♞` (`\u265E`) in a white rounded tile, or an inline SVG. Logo is **not** important; keep it generic.

## A2. Information architecture — **journeys first**, 5-tab bottom nav

The nav must make **journeys the priority** while the vault *supports* them. Five tabs:

1. **Home (⌂)** — *Journeys-first dashboard.* Greeting hero with vault stats (Identity verified ✓, N verified proofs, N digital assets). Then **“Journeys for you”** = AI-guided offer cards (see A4). Then **“Start from outside the app”** = third-party request banner + new-customer onboarding banner. Then a 2×2 **vault glance** grid (Wallet / Identity / Assets / Why TrustPass) and a sustainability snapshot.
2. **Wallet (▦)** — *Daily money only.* A horizontally-swipable carousel of payment cards (Debit, Credit) with card art, balance, tap-to-pay + freeze/unfreeze. A recent-transactions list. **No identity content here.**
3. **Identity (⛉)** — Verified-identity header card (name, DID), **ID documents** grid (passport, driving licence, NI, proof of age — each opens a doc detail page), **verified credentials** list. A **“Manage”** section with two **drill-in sub-pages**: **“Connected data sources”** and **“Address history”** (these are NOT on the surface — address history and the data list live *inside* Identity).
4. **Assets (◈)** — *Tokenised assets.* A portfolio header showing total **£ value**. A list of tokenised **assets** (Green Savings Bond £10,000; Mortgage Offer £200,000; etc.) on coloured asset cards. Below, a strip of **access/readiness tokens** (the VCs) and a “reuse a token” action.
5. **Journeys (❖)** — The priority screen. A short intro line, then **“Start in the app”** = four product offer cards (Mortgage, Personal Loan, Green Savings, Home Insurance). Then **“Start outside the app”** = third-party (NorthBank) handshake entry + cold-start onboarding entry. Then **“Fast-track with reuse”** if tokens exist.

> **AI agents are NOT a tab.** They surface as **guided offer cards** on Home/Journeys (badge: “Offers agent / Eligibility agent / Affordability agent · guidance only”).

App chrome: a top app-bar (logo, “TrustPass / Digital Identity & Data Vault”, ⚙︎ button → opens `architecture.html`, ↻ reset button) and a fake iOS status bar. Hide tab bar on the splash screen only.

## A3. Data model (seed data — recreate faithfully)

- **Customer:** `Maya Johnson`, Verified · UK, `did:trustpass:8f2a…c91`.
- **Payment cards:** Everyday Debit (VISA, ••4821, exp 09/29, £3,184.20); Avios Rewards (Mastercard, ••7715, exp 04/28, £612.40 owed).
- **Transactions:** Waitrose −£42.18 (Groceries, Today); Caffè Nero −£3.65 (Eating out, Today); Salary Acme Ltd +£3,250.00 (Income, Yesterday); GWR Trains −£28.40 (Transport, Mon).
- **ID documents:** UK Passport (exp 2031, HM Passport Office); Driving Licence (Full·clean, DVLA); National Insurance (QQ 12 34 56 C, HMRC); Proof of Age (Over 18, Gov.UK One Login). All “Verified”.
- **Address history:** 14 Maple Court, Bristol BS1 4XY (Jul 2023–present, 35 months, current); 8 Riverside Walk, Bath BA2 6PQ (Mar 2019–Jul 2023, 52 months); 22 College Green, Bristol BS1 5TB (Sep 2016–Mar 2019, 30 months).
- **Connected data sources (8):** Gov.UK One Login (active), HMRC (active), Open Banking·Lloyds (active), Open Banking·NorthBank (active), Open Banking·Starling (stale, 18 days), Credit Reference Agency (active), Royal Mail PAF (active), Employer/Payroll (off → has a “Link” button that simulates linking).
- **Verified credentials (10):** identity verified (Gov.UK One Login), address verified (Royal Mail PAF), UK resident (HMRC), income verified (HMRC payroll), affordability signal ACCEPTABLE (Open Banking), credit file signal (CRA), sanctions check clear (Compliance), age over 18 (Gov.UK One Login), deposit verified £42,000 (Bank of record), risk profile medium (self-attested).
- **Journeys (4):** each has emoji, name, tagline, an `offer` line, an AI `coach` paragraph, a minimum `proofs` list, a `selective` disclosure note, and a `token` definition:
  - **Mortgage** 🏠 — proofs: id, income, afford, deposit, res, sanc → **Mortgage Readiness Token (MRT)**. Selective: “assessor sees income band £60–80k, not exact salary”.
  - **Personal Loan** 💷 — proofs: id, income, afford, credit, sanc → **Loan Eligibility Token (LET)**.
  - **Green Savings** 🌱 — proofs: res, id, age, risk, sanc → **Investment Access Token (IAT)**.
  - **Home Insurance** 🛡️ — proofs: id, addr, res, sanc → **Insurance Evidence Token (IET)**.
- **Tokenised assets (issued on journey completion):** Green Savings Bond 🌱 £10,000 (3.95% AER, 3-yr fixed, green card); Mortgage Offer 🏠 £200,000 (4.21%, 25-yr DIP, cream card); Loan Offer 💷 £15,000 (6.4% APR, cream); Home Insurance Cover 🛡️ £250/yr (green). Each gets a serial like `GSB-2026-000987`.
- **AI readiness (per journey):** a score (e.g. Mortgage 82 “Strong”, Loan 88, Investment 79, Insurance 84), a verdict (proceed/wait), a headline, and 2–3 **improvement tips** (e.g. “Adding £6,000 deposit reaches 20% for better rates”). Render as a **conic-gradient ring** + verdict + tip list. Label clearly: *AI estimate — guidance, not a decision.*
- **AI guided offers (3, surfaced as cards):** Offers agent 🎯 → Insurance (“save ~£420/yr”); Eligibility agent 📋 → Loan (“you’d likely pass”); Affordability agent 📊 → Mortgage (“ACCEPTABLE, healthy margin”).
- **Reuse matrix:** e.g. an existing Mortgage Readiness Token lets Insurance inherit id/addr/res/sanc (saves 90%); show cross-journey reuse options with a “−90%” style badge.
- **Third party:** **NorthBank** 🏦, journey “Mortgage”, wants id/income/deposit/res, reason “Mortgage affordability assessment”, **TTL 15 min**.
- **Sustainability counters:** per completed journey increment `sheets += 12, trips += 1, checks += 4` (paper sheets saved, branch trips removed, duplicate checks avoided).

## A4. Screens & flows to implement (hash-router, ~26 routes)

Use a hash router (`#/route?key=value`) and persist state in `localStorage`. **Put all JS in a single `<script>` block** so functions are available before routing (avoid multi-block ordering bugs). Maintain a **mock API layer** that simulates latency and streams every call (METHOD path status ms) into a live, dark-terminal **API log** shown on relevant screens.

**Routes / screens:**
- `#/splash` — brand splash: logo, name, one-line value prop, 3 feature bullets, “Enter my vault” + “I don’t have a wallet yet”.
- `#/home`, `#/wallet`, `#/identity`, `#/assets`, `#/journeys` — the five tabs (A2).
- `#/data`, `#/address`, `#/doc?id=` — Identity sub-pages (connected data list with link action; address timeline; document detail explaining “derived claims, document never shared directly”).
- `#/offer?key=` — AI-guided journey offer: coach bubble + **readiness ring** + “Begin — share minimum proofs”.
- **Core journey (4 steps with a progress bar):**
  - `#/coach?key=` — Step 1: AI coach explains, reiterates “I will NOT make the decision”.
  - `#/consent` — Step 2: checkbox list of **minimum proofs** (pre-ticked), a **selective-disclosure note**, “Share minimum proofs”. Each row labelled “claim, not document”.
  - `#/rules` — Step 3: animated **deterministic rules** checklist; each rule flips pending→pass with a tick; final row “APPROVED”. Below: live API log.
  - `#/token` — Step 4: confetti; big **Verifiable Credential** token card (code, id, claims chips, issuer DID, issued/expires); **plus the tokenised ASSET card** issued by this journey; “View in my assets / Reuse / Back to journeys”.
- `#/reuse` — cross-journey token reuse (inherit claims, rules checks only the delta).
- **Outside-app — third party:** `#/thirdparty` (NorthBank wants proofs) → `#/handshake` (request summary: who/why/returns a VP/raw data shared = none; a **live 15:00 countdown**; per-claim consent checkboxes; “Grant & send”) → `#/vp` (a **Verifiable Presentation** card “Sent to NorthBank”, audience-scoped, TTL, **auto-revoked**; “Revoke access now” button; audit log).
- **Outside-app — cold start:** `#/onboard` (apply for a Personal Current Account with no wallet) → `#/signup` (minimum fields: name, DoB, email, mobile + consent) → `#/bootstrap` (animated sequence pulling Gov.UK, HMRC, Open Banking, Credit reference, then a rules check; ends with “Account opened ✓ / vault is born”, confetti, “The loop is closed”).
- `#/about` — **Why TrustPass**: Customer Benefits (Minutes not weeks; Privacy-first; Secure & compliant; Better offers faster; Less paper lower carbon), Trust Principles (You own your data; You choose what to share; Used only for verification; Decisions are explainable; Compliant by design), a sustainability mini-dashboard, and a footer bar “Built on trust. Powered by digital identity · Secure by design · Customer in control · Compliant & auditable · Sustainable by choice”.
- `#/log` — full mock API log.

**Interactions that must actually work:** tab switching with active highlight; card freeze/unfreeze + tap-to-pay toast; connector linking; consent → rules animation → token + asset issuance + carbon increment; reuse; third-party grant → VP → revoke; cold-start bootstrap; reset button (clears state). All async actions write to the API log.

## A5. Accessibility (required — design is judged on inclusivity)

`lang="en-GB"`; visible `:focus-visible` outlines; clickable non-button elements get `role="button"` + `tabindex="0"` + Enter/Space handlers; `aria-live="polite"` on the toast; `aria-label`s on icon-only buttons; respect `prefers-reduced-motion` (disable animations); status never conveyed by colour alone (always pair with a ✓/word); minimum tap targets and ≥10px text.

## A6. Quality bar

Polished, demo-ready, no console errors, no external file dependencies, smooth animations, realistic copy. Include a small disclaimer “Concept prototype · synthetic data · mock APIs · simulated tokens”. The whole thing should feel like a real banking app, not a wireframe.

---

# PART B — THE ARCHITECTURE DOCUMENT (`architecture.html`)

Build a **standalone, scrollable, beautifully designed** architecture document (same colour system & fonts as the app; max-width ~1080px; white panels on the dark-green gradient). It must be **more detailed and more technical than a typical one-pager** — treat it as the doc a Lloyds engineering panel and product owners would scrutinise. Use **hand-built inline SVG diagrams** (no image files), tables, code blocks, and clear narrative. Include every section below.

1. **Title & executive summary** — what TrustPass is, the problem it solves (repeated KYC friction, document re-submission, slow eligibility, privacy loss), the one-line vision, and the dual challenge framing (ID wallets **and** tokenised assets). Include a row of summary “tag” chips.

2. **Information architecture diagram** — an SVG showing the **5-tab nav** and what each tab contains, with **Identity’s drill-ins (Connected data, Address history)** as nested boxes, and arrows from **outside-app entry points** (third-party app, new customer) flowing into the journeys. Make “Journeys” visually the priority. Add a colour legend.

3. **Five-layer (plus mock backend) architecture** — both a card grid and an SVG block diagram:
   - ① Experience/UI · ② Identity & Data vault (VC store, DID, connectors) · ③ **AI assistants (read-only, guidance)** · ④ **Policy/Rules engine (deterministic, the only decider)** · ⑤ Tokens & Assets (VC/VP/tokenised assets) · ⑥ Mock API surface.
   - Explicitly show the **AI ↔ Rules boundary**: AI can read signals and suggest; only the rules engine decides; draw it so the separation is obvious.

4. **The AI–Rules separation (deep dive)** — a dedicated section. Explain *why* (regulatory explainability, fairness, auditability), *how* (AI outputs are advisory: readiness score, proceed/wait, tips; the rules engine consumes only consented claims and returns a deterministic, reproducible decision with a reason code). Include a small decision-trace example.

5. **Identity & trust model** — DIDs, Verifiable Credentials, Verifiable Presentations, selective disclosure / data minimisation, consent & revocation, time-limited tokens. A table mapping **artefact → type (proof vs value) → example → properties** (VC, VP, tokenised asset ×2). Note alignment to **W3C VC/DID, OpenID4VP, eIDAS 2.0 / EUDI Wallet** direction.

6. **Tokenised assets & “tokenised money/assets” framing** — explain the difference between *proof tokens* (eligibility) and *tokenised assets with value* (Green Bond, Mortgage Offer); custody in the wallet; transferability/redemption; how this answers the tokenised-currency/asset half of the challenge. Include a JSON example for `/api/token/issue` and `/api/assets/tokenise`.

7. **The three crossover flows** (the heart of the product) — for each, a labelled SVG or step-flow:
   - **In-app (wallet-first):** Journeys → AI offer → consent → rules → token + asset.
   - **Outside-app (third-party handshake):** External app (NorthBank) → handshake request → per-claim consent → time-limited Verifiable Presentation → auto-revoke. Stress: claims only, audience-scoped, 15-min TTL.
   - **Cold start (no wallet):** Apply for product → minimum signup → pull HMRC/banks/CRA → account opened + vault born → future journeys near-instant.

8. **End-to-end sequence diagram** — an SVG sequence (lifelines: Customer, Wallet UI, AI Assistant, Data Connectors, Rules Engine, Token/Asset Service, mock Ledger) for a full mortgage journey, showing consent, read-only AI guidance, deterministic decision, token + asset issuance, and the audit event.

9. **Data spine** — a table of the 8 connectors (source → data provided → claims derived → refresh cadence → consent state) and how raw data is transformed into reusable claims (with selective disclosure).

10. **Mock API contract** — a table/codeblock of the key endpoints used by the prototype: `GET /api/wallet/{id}`, `POST /api/consent`, `POST /api/rules/check-eligibility`, `POST /api/token/issue`, `PATCH /api/wallet/add-token`, `POST /api/assets/tokenise`, `POST /api/handshake/request`, `POST /api/presentation/create`, `DELETE /api/presentation/revoke`, `POST /api/onboarding/bootstrap`, `POST /api/connectors/link|refresh`. Show example request/response shapes and note that production calls would be signed, authenticated, rate-limited and audited.

11. **Security, privacy & compliance** — threat model highlights (consent forgery, replay, over-sharing, token theft), mitigations (audience-scoping, short TTL, revocation, on-device keys, minimal disclosure), GDPR/data-minimisation, auditability, and an explicit “what we’d harden for production” list (real DID/VC libraries, HSM-backed keys, OpenID4VP, real Open Banking sandbox).

12. **Sustainability impact** — quantify the carbon/paper savings model (verify-once-reuse: sheets, trips, duplicate checks avoided) and why reusable proofs are inherently lower-carbon.

13. **Accessibility & inclusive design** — what was done (keyboard, focus, reduced-motion, ARIA, contrast, status-not-by-colour) and the inclusive-design rationale.

14. **Tech choices & rationale** — why a single self-contained HTML/vanilla JS prototype (zero-dependency, instantly shareable, demo-resilient), trade-offs, and how it maps to a real React/TS + service architecture later.

15. **Roadmap / feasible iterations** — v1 (wallet-first journeys) → v2 (data spine, AI agents, third-party handshake, cold start) → v3 (journeys-first IA, tokenised assets, readiness, carbon, accessibility) → production (real standards & integrations). Note business-agility/POs-would-invest framing.

16. **Footer** — “Built on trust. Powered by digital identity · Secure by design · Customer in control · Compliant & auditable · Sustainable by choice.”

**Architecture-doc quality bar:** every diagram hand-built with inline SVG (no external images); consistent semantic colours (green/blue/purple/orange/terracotta as in the app); precise, technical, and persuasive prose; tables and code where they add rigour; scannable but deep. It should stand on its own as the canonical technical reference for the product.

---

## OUTPUT INSTRUCTIONS FOR THE AI

- Deliver **two complete files** in full: `index.html` then `architecture.html`. No placeholders, no “…”, no TODOs.
- Keep all JS in one script block; persist to `localStorage`; ensure **zero console errors**.
- Embed the logo as a unicode glyph/SVG; **no external file references**.
- Make it **mobile-first** and visually premium. Match the exact colour tokens and the **AI=blue / Rules=purple / Tokens=orange** semantics.
- The ⚙︎ button in `index.html` should open `architecture.html`.
- After generating, **self-review**: confirm all 5 tabs, all three crossover flows (in-app, third-party, cold-start), token **and** asset issuance, readiness guidance, carbon counter, Why-TrustPass screen, and accessibility features are present and working.
