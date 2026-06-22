# Build Prompt — TrustPass (React, frontend-only, JSON-driven)

> **Paste everything below into your AI coding model** (Cursor, Claude Code, v0, Copilot, Bolt, etc.).
> It rebuilds the **TrustPass — Digital Identity & Data Vault** prototype as a modern **React** app,
> **frontend-only**, with **all data loaded from JSON files**. Mobile-first. Every interaction must work.
> Includes **happy *and* unhappy** user journeys.

---

## 0) What you are building (one paragraph)

TrustPass is a **mobile-first digital identity & data vault** for a UK bank (Lloyds Banking Group style).
A customer holds **reusable verified proofs** (identity, address, income, affordability, etc.) sourced from
trusted providers (Gov.UK One Login, HMRC, Open Banking, Credit Reference, Royal Mail PAF). When the customer
starts a **journey** (mortgage, loan, green savings, insurance), an **AI coach** explains and guides
(*guidance only, never decides*), the customer gives **consent with selective disclosure** (share *claims*,
not documents), a **deterministic rules engine** runs the decision (same inputs → same outcome), and on
success a **verifiable credential token** + a **tokenised asset** are issued back into the wallet. Journeys can
start **inside the app** *or* **outside the app** (a third-party app requests a time-limited handshake), and a
**cold-start** flow onboards a brand-new customer who then has their vault populated from connected sources.
The product is **product-agnostic** — it is the identity/data layer, not one bank product.

---

## 1) Tech stack & project rules

- **React 18 + Vite + TypeScript.** Plain CSS or CSS Modules (no UI kit, no Tailwind required — but Tailwind is OK if you prefer; the design tokens below must be honoured exactly either way).
- **Frontend-only. No backend, no server, no database.** All domain data lives in **JSON files in `/src/data/`** and is imported (or `fetch`ed from `/public/data/`) at runtime. Pick one approach and be consistent — **importing typed JSON from `/src/data` is preferred.**
- **Routing:** use `react-router-dom` with **hash routes** (`HashRouter`) so it works when opened from a static file/host with no server config.
- **State:** React Context + `useReducer` (a single `AppStore`). **Persist to `localStorage`** under key `trustpass.v3.state` and rehydrate on load (migrate missing fields safely).
- **No telemetry, no external API calls.** Simulate latency with an in-app **mock API** helper (see §7).
- **Accessibility:** semantic HTML, focus-visible outlines, `aria-label`s on icon buttons, respect `prefers-reduced-motion`, colour contrast AA. Provide an `.sr-only` utility.
- **Quality bar:** TypeScript types for every data model; no console errors; every button/link navigates or does something; mobile-first and fully responsive.

### Suggested structure
```
trustpass/
  index.html
  src/
    main.tsx
    App.tsx
    store/AppStore.tsx        # context + reducer + localStorage
    api/mockApi.ts            # simulated latency + api log
    data/                     # ALL JSON data sources (see §3)
      customer.json
      cards.json
      transactions.json
      idDocs.json
      addresses.json
      connectors.json
      credentials.json
      journeys.json
      assets.json
      readiness.json
      agents.json
      reuseMatrix.json
      thirdParty.json
      config.json             # brand, colours echoed for reference, ttl, etc.
    types/models.ts           # TypeScript interfaces for all data
    styles/tokens.css         # :root design tokens (see §2)
    styles/global.css
    components/                # PhoneFrame, AppBar, TabBar, Card, Pill, Coach, RuleRow, TokenCard, AssetCard, Toast...
    routes/                    # one component per screen (see §5)
    lib/                       # helpers (selective disclosure, carbon, etc.)
```

---

## 2) Design system (use these EXACT tokens)

Define in `styles/tokens.css` as CSS custom properties on `:root`. Semantic meaning matters:
**green = wallet/brand/frontend, blue = AI, purple = rules engine, orange/gold = tokens & assets, terracotta = third-party/external.**

```css
:root{
  /* Brand greens */
  --green:#006A4D; --green2:#04875F; --greenDeep:#013528; --greenInk:#0A2C22;
  --mint:#E7F3EC; --mint2:#D6EBDF; --soft:#F4F9F6; --paper:#FFFFFF;
  /* Ink / neutrals */
  --ink:#0E211C; --ink2:#2C3A35; --muted:#69756F; --faint:#9AA6A0;
  --line:#E4ECE7; --line2:#EFF4F1;
  /* AI = blue */
  --ai:#1C6EC2; --aiBg:#E9F2FC; --aiInk:#0C4C8C;
  /* Rules engine = purple */
  --rules:#6E4BD0; --rulesBg:#EFEAFB; --rulesInk:#3F2596;
  /* Tokens / assets = orange-gold */
  --token:#D9842A; --tokenBg:#FCEFDD; --tokenInk:#8A5212;
  --assetGreen:#E3ECE4; --assetCream:#FBF1DD;
  /* Status / third-party */
  --danger:#C0392B; --tp:#C24E2E; --tpBg:#FBEAE3;
  /* Radii / shadows / spacing */
  --r-lg:24px; --r-md:18px; --r-sm:12px;
  --sh-sm:0 2px 8px rgba(8,46,34,.06);
  --sh-md:0 10px 30px rgba(8,46,34,.10);
  --sh-lg:0 24px 60px rgba(8,46,34,.20);
  --space:16px;
}
```

- **Font:** Inter (Google Fonts, weights 400–900) with system fallback `system-ui, Segoe UI, Roboto, Arial, sans-serif`.
- **Background behind the phone:** deep-green radial gradient `radial-gradient(circle at 20% 0%,#0c4a37 0%,#013528 55%,#012a20 100%)`.
- **Theme colour / status bar:** `#013528`.
- **Overall aesthetic:** premium, calm, fintech; rounded cards, soft shadows, generous whitespace, large tap targets (min 44px), subtle micro-animations (with reduced-motion fallback).

### Logo
- The logo is **not critical** — use a generic **Lloyds-style mark**: a simple **prancing/black horse glyph** OR the unicode knight **♞** inside a **white rounded square** (`38px`, radius `11px`).
- App title text: **“TrustPass”** with subtitle **“Digital Identity & Data Vault”**.
- Store logo as an SVG component or a small asset in `/src/assets/` with a text/emoji fallback so the app is fully self-contained. Do **not** depend on any external image URL.

### Phone frame (mobile-first shell)
- Centre a **phone frame**: `max-width:420px`, full viewport height (`max-height ~920px`), `background:var(--soft)`, column flex, hidden overflow, big shadow.
- On screens `≥480px`, add `border-radius:44px` + `10px solid #06231a` bezel to look like a device. On phones it fills the screen.
- Inside top-to-bottom: **status bar** (deep green, time + dots) → **app bar** (logo, brand, icon buttons: reset/about) → **scrollable view** → **bottom tab bar** (5 tabs).

---

## 3) Data model — put ALL of this in JSON files (`/src/data/`)

Create matching **TypeScript interfaces** in `types/models.ts`. The values below are the **seed data** — reproduce them faithfully.

### `customer.json`
```json
{ "id":"customer-001", "name":"Maya Johnson", "sub":"Verified · UK", "did":"did:trustpass:8f2a…c91" }
```

### `cards.json` (payment cards)
```json
[
  { "id":"debit","type":"debit","brand":"VISA","name":"Everyday Debit","num":"4821","exp":"09/29","bal":"£3,184.20","tone":"green" },
  { "id":"credit","type":"credit","brand":"MC","name":"Avios Rewards","num":"7715","exp":"04/28","bal":"£612.40 owed","tone":"dark" }
]
```

### `transactions.json`
```json
[
  { "icon":"🛒","name":"Waitrose","cat":"Groceries","amt":"−£42.18","when":"Today" },
  { "icon":"☕","name":"Caffè Nero","cat":"Eating out","amt":"−£3.65","when":"Today" },
  { "icon":"💷","name":"Salary — Acme Ltd","cat":"Income","amt":"+£3,250.00","when":"Yesterday","in":true },
  { "icon":"🚆","name":"GWR Trains","cat":"Transport","amt":"−£28.40","when":"Mon" }
]
```

### `idDocs.json`
```json
[
  { "id":"passport","icon":"🛂","name":"UK Passport","sub":"Expires 2031","issuer":"HM Passport Office","status":"Verified" },
  { "id":"dl","icon":"🚗","name":"Driving Licence","sub":"Full · clean","issuer":"DVLA","status":"Verified" },
  { "id":"nin","icon":"🆔","name":"National Insurance","sub":"QQ 12 34 56 C","issuer":"HMRC","status":"Verified" },
  { "id":"age","icon":"📜","name":"Proof of Age","sub":"Over 18","issuer":"Gov.UK One Login","status":"Verified" }
]
```

### `addresses.json` (history timeline)
```json
[
  { "current":true,  "line":"14 Maple Court, Bristol BS1 4XY","period":"Jul 2023 – present","dur":"35 months" },
  { "current":false, "line":"8 Riverside Walk, Bath BA2 6PQ","period":"Mar 2019 – Jul 2023","dur":"52 months" },
  { "current":false, "line":"22 College Green, Bristol BS1 5TB","period":"Sep 2016 – Mar 2019","dur":"30 months" }
]
```

### `connectors.json` (connected data sources)
```json
[
  { "id":"govuk","icon":"🪪","name":"Gov.UK One Login","provides":"Identity · Passport · Licence","status":"active","refresh":"today" },
  { "id":"hmrc","icon":"🏛️","name":"HMRC","provides":"PAYE income · Tax position","status":"active","refresh":"2 days ago" },
  { "id":"ob_lloyds","icon":"🟢","name":"Open Banking · Lloyds","provides":"Balance · Income · Spend","status":"active","refresh":"today" },
  { "id":"ob_north","icon":"🔵","name":"Open Banking · NorthBank","provides":"Cross-bank affordability","status":"active","refresh":"today" },
  { "id":"ob_star","icon":"🟣","name":"Open Banking · Starling","provides":"Savings · Spend","status":"stale","refresh":"18 days ago" },
  { "id":"cra","icon":"📊","name":"Credit Reference Agency","provides":"Credit file signal","status":"active","refresh":"this week" },
  { "id":"paf","icon":"📮","name":"Royal Mail PAF","provides":"Address history","status":"active","refresh":"on change" },
  { "id":"payroll","icon":"💼","name":"Employer / Payroll","provides":"Employment · Salary band","status":"off","refresh":"not linked" }
]
```
`status` ∈ `active | stale | off` — render with distinct chips (green / amber / grey). **Stale & off connectors matter for the unhappy path** (§6).

### `credentials.json` (the reusable verified proofs)
```json
[
  { "id":"id","label":"Identity verified","issuer":"Gov.UK One Login","sub":"Passport · biometric match" },
  { "id":"addr","label":"Address verified","issuer":"Royal Mail PAF","sub":"Active 35 months" },
  { "id":"res","label":"UK resident","issuer":"HMRC","sub":"Resident 5+ yrs" },
  { "id":"income","label":"Income verified","issuer":"HMRC payroll","sub":"PAYE · last 6 months" },
  { "id":"afford","label":"Affordability signal","issuer":"Open Banking","sub":"Score: ACCEPTABLE" },
  { "id":"credit","label":"Credit file signal","issuer":"Credit reference","sub":"Available" },
  { "id":"sanc","label":"Sanctions check clear","issuer":"Compliance","sub":"Refreshed today" },
  { "id":"age","label":"Age over 18","issuer":"Gov.UK One Login","sub":"DoB verified" },
  { "id":"deposit","label":"Deposit verified","issuer":"Bank of record","sub":"£42,000 available" },
  { "id":"risk","label":"Risk profile: medium","issuer":"Self-attested","sub":"Confirmed today" }
]
```

### `journeys.json` (in-app product journeys)
```json
{
  "mortgage":{ "key":"mortgage","name":"Mortgage","emoji":"🏠","tagline":"Buy or remortgage with confidence",
    "offer":"Pre-qualified · indicative 4.21%",
    "coach":"To assess mortgage readiness I do not need full documents — only verified proofs from your wallet: identity, income, affordability, residency, deposit and compliance.",
    "proofs":["id","income","afford","deposit","res","sanc"],
    "selective":"Your assessor sees \"income band £60k–£80k\", not your exact salary.",
    "token":{ "type":"MortgageReadinessToken","name":"Mortgage Readiness Token","code":"MRT","purpose":"Ready for mortgage journey","next":"Share with a mortgage service" } },
  "loan":{ "key":"loan","name":"Personal Loan","emoji":"💷","tagline":"Indicative personal loan eligibility",
    "offer":"Likely eligible · up to £15,000",
    "coach":"To check loan eligibility I only need verified identity, income, affordability and credit eligibility signals.",
    "proofs":["id","income","afford","credit","sanc"],
    "selective":"The lender sees \"affordability ACCEPTABLE\", not the underlying transactions.",
    "token":{ "type":"LoanEligibilityToken","name":"Loan Eligibility Token","code":"LET","purpose":"Eligible for indicative loan","next":"Continue to a loan quote" } },
  "investment":{ "key":"investment","name":"Green Savings","emoji":"🌱","tagline":"Access a simulated Green Savings Bond",
    "offer":"Eligible · 3.95% AER fixed",
    "coach":"To check investment access I only need minimum wallet proofs: UK resident, KYC verified, over 18, risk profile and sanctions clear.",
    "proofs":["res","id","age","risk","sanc"],
    "selective":"The platform sees \"risk: medium\" and \"age ≥ 18\", not your DoB.",
    "token":{ "type":"InvestmentAccessToken","name":"Investment Access Token","code":"IAT","purpose":"Access to Green Savings Bond","next":"Add green bond to wallet" } },
  "insurance":{ "key":"insurance","name":"Home Insurance","emoji":"🛡️","tagline":"Start a quote or evidence a claim",
    "offer":"Save ~£420/yr vs current",
    "coach":"To start an insurance journey I only need reusable verified identity, address and evidence credentials.",
    "proofs":["id","addr","res","sanc"],
    "selective":"Your insurer sees verified identity + address claims, never the documents.",
    "token":{ "type":"InsuranceEvidenceToken","name":"Insurance Evidence Token","code":"IET","purpose":"Reusable evidence for quote or claim","next":"Share with insurance journey" } }
}
```
> Plus add a **fifth, deliberately unhappy** journey — see §6 (`creditcard`).

### `assets.json` (tokenised assets issued on success)
```json
{
  "investment":{ "id":"GSB","name":"Green Savings Bond","icon":"🌱","value":"£10,000","meta":"3.95% AER · 3-yr fixed","kind":"bond","tone":"green","note":"Tokenised investment asset held in your vault. Transfer or redeem on maturity." },
  "mortgage":{ "id":"MO","name":"Mortgage Offer","icon":"🏠","value":"£200,000","meta":"4.21% · 25-yr · DIP","kind":"offer","tone":"cream","note":"Tokenised agreement-in-principle. Time-stamped, revocable, shareable with conveyancer." },
  "loan":{ "id":"LO","name":"Loan Offer","icon":"💷","value":"£15,000","meta":"6.4% APR · 36 mo","kind":"offer","tone":"cream","note":"Tokenised personal loan offer. Accept to draw down." },
  "insurance":{ "id":"IC","name":"Home Insurance Cover","icon":"🛡️","value":"£250/yr","meta":"Buildings + contents","kind":"policy","tone":"green","note":"Tokenised policy. Evidence of cover reusable across claims." }
}
```

### `readiness.json` (AI readiness estimate — guidance only)
```json
{
  "mortgage":{ "score":82,"band":"Strong","verdict":"proceed","headline":"You look ready to proceed",
    "tips":["Deposit of £42,000 covers 15% — adding £6,000 reaches 20% for better rates.","Income verified via HMRC for 6 months — no payslips needed.","Keep current-account conduct clean for 30 days before applying."] },
  "loan":{ "score":88,"band":"Strong","verdict":"proceed","headline":"Eligible — good time to proceed",
    "tips":["Affordability signal is ACCEPTABLE with headroom.","Clearing the £600 card balance could lift your indicative limit."] },
  "investment":{ "score":79,"band":"Acceptable","verdict":"proceed","headline":"You can proceed with the Green Bond",
    "tips":["Risk profile medium suits a 3-yr fixed bond.","Consider holding 3 months' expenses as cash before investing."] },
  "insurance":{ "score":84,"band":"Strong","verdict":"proceed","headline":"Ready to quote instantly",
    "tips":["Verified address (35 months) unlocks the best buildings premium.","Add contents value to refine your quote."] }
}
```
> Plus the **unhappy** readiness entry for `creditcard` — see §6 (`verdict:"wait"`).

### `agents.json` (AI agents surfaced as offers/guidance — NOT a tab)
```json
[
  { "id":"offers","emoji":"🎯","name":"Smart offer","tag":"Offers agent","journey":"insurance","say":"Based on your verified income and spend, you could save ~£420/yr by switching home insurance." },
  { "id":"elig","emoji":"📋","name":"Pre-qualified","tag":"Eligibility agent","journey":"loan","say":"A read-only check suggests you'd likely pass a personal-loan eligibility assessment. Guidance only — not a decision." },
  { "id":"afford","emoji":"📊","name":"Ready for mortgage","tag":"Affordability agent","journey":"mortgage","say":"Your affordability is ACCEPTABLE with healthy disposable margin — a strong moment to start a mortgage." }
]
```

### `reuseMatrix.json` (cross-journey reuse — fast-track using a token you already hold)
```json
{
  "insurance":  [ { "type":"MortgageReadinessToken","inherits":["id","addr","res","sanc"],"saved":"90%" },
                  { "type":"LoanEligibilityToken","inherits":["id","sanc"],"saved":"55%" } ],
  "loan":       [ { "type":"MortgageReadinessToken","inherits":["id","income","afford","sanc"],"saved":"80%" } ],
  "investment": [ { "type":"LoanEligibilityToken","inherits":["id","sanc"],"saved":"45%" },
                  { "type":"MortgageReadinessToken","inherits":["id","sanc","res"],"saved":"60%" } ],
  "mortgage":   [ { "type":"LoanEligibilityToken","inherits":["id","income","afford","sanc"],"saved":"65%" } ]
}
```

### `thirdParty.json` (outside-the-app handshake)
```json
{ "name":"NorthBank","emoji":"🏦","journey":"Mortgage","wants":["id","income","deposit","res"],
  "why":"Mortgage affordability assessment","ttlMins":15 }
```

### `config.json`
```json
{ "brand":"TrustPass","subtitle":"Digital Identity & Data Vault","issuerDid":"did:trustpass:lbg",
  "storeKey":"trustpass.v3.state", "ttlMins":15 }
```

---

## 4) Information architecture — **5 bottom tabs** (this exact IA)

`Home` · `Wallet` · `Identity` · `Assets` · `Journeys`. Highlight the correct tab even on sub-routes.

- **Home** — greeting, vault summary stat (count of verified proofs), an AI **agent offer** card, a **third-party request** banner (“NorthBank needs your verified proofs”), quick links.
- **Wallet** — traditional wallet: **payment cards** (freeze/unfreeze toggle), recent **transactions**. This is the “digital wallet you already know”.
- **Identity** — verified **ID documents** grid; tile linking to **Connected data sources**; the verified **credentials** list. **Sub-pages:** **Data** (`#/data`, connected sources + connectors detail), **Address** (`#/address`, address-history timeline), **Doc** (`#/doc?id=…`, a single document detail). **Address history is NOT shown on the main Identity screen — it lives under Identity → Address.** **Data is a sub-page of Identity.**
- **Assets** — the **tokenised assets** the customer has been issued (starts empty; fills as journeys complete). Each asset card shows icon, value, meta, on-chain-style serial, note.
- **Journeys** — the heart of the app, split into **two groups**:
  - **Start in the app** — cards for Mortgage, Personal Loan, Green Savings, Home Insurance, **Credit Card (unhappy)**.
  - **Start outside the app** — **NorthBank third-party handshake** entry, and **Cold-start onboarding** (new customer, not yet in wallet).

> **Agents are not a tab.** Surface them as **guided offer cards** on Home and Journeys.

---

## 5) Screens / routes (hash routes)

Implement every route. Names mirror the reference build:

| Route | Screen |
|---|---|
| `#/splash` | Branded splash / enter |
| `#/home` | Home dashboard |
| `#/wallet` | Cards + transactions |
| `#/identity` | ID docs + data link + credentials |
| `#/data` | Connected data sources (sub-page of Identity) |
| `#/address` | Address-history timeline (sub-page of Identity) |
| `#/doc?id=` | Single document detail |
| `#/assets` | Tokenised assets |
| `#/journeys` | Journeys hub (in-app + outside-app) |
| `#/offer?key=` | Journey offer + **readiness** estimate |
| `#/coach?key=` | **Step 1/4** AI coach (guidance only) |
| `#/consent` | **Step 2/4** consent + selective disclosure toggles |
| `#/rules` | **Step 3/4** deterministic rules engine animation + live API log |
| `#/token` | **Step 4/4** token + tokenised asset issued (confetti) |
| `#/reuse` | Cross-journey reuse |
| `#/thirdparty` | Outside-app: NorthBank intro |
| `#/handshake` | Verification request + **TTL countdown** |
| `#/vp` | Verifiable presentation sent (claims not documents, auto-revoke) |
| `#/onboard` | Cold-start: start onboarding |
| `#/signup` | Cold-start: minimal sign-up form |
| `#/bootstrap` | Cold-start: vault auto-populates from sources |
| `#/about` | About / why TrustPass / trust principles |
| `#/log` | Live API/event log |

**Tab mapping:** `data/address/doc → Identity`; all journey/flow/onboard/handshake routes → `Journeys`; `about/log → Home`.

---

## 6) HAPPY *and* UNHAPPY journeys (must include both)

### 6a) The happy path (primary demo)
For any of mortgage/loan/investment/insurance:
`Journeys → Offer (+readiness=proceed) → Coach (Step 1) → Consent (Step 2, selective disclosure) → Rules engine (Step 3, every rule ✓, final PASS) → Token + Asset issued (Step 4, confetti)`.
Then **Reuse** another journey faster using the held token, and view the new asset under **Assets**.

### 6b) Unhappy paths (REQUIRED — design these clearly so reviewers see the product handles “no”)
Add a **Credit Card** journey that is intentionally a *decline/refer*, plus failure branches across the app:

1. **Rules-engine DECLINE / REFER (`creditcard` journey).** Add to `journeys.json`:
   ```json
   "creditcard":{ "key":"creditcard","name":"Credit Card","emoji":"💳","tagline":"Apply for a rewards credit card",
     "offer":"Subject to status","heroFrom":"#0b5a40,#013528",
     "coach":"To assess a credit-card application I check verified identity, income, affordability, credit signal and compliance.",
     "proofs":["id","income","afford","credit","sanc"],
     "selective":"The issuer sees pass/fail signals only, never your statements.",
     "token":{ "type":"CreditDeclineReceipt","name":"Decision Receipt","code":"DR","purpose":"Record of an honest decline","next":"See how to improve and re-apply later" } }
   ```
   And to `readiness.json` give it an **unhappy** verdict:
   ```json
   "creditcard":{ "score":48,"band":"Not yet","verdict":"wait","headline":"Not the right time to apply",
     "tips":["Affordability is tight this month — disposable income is below the issuer's threshold.","Clearing the £612 Avios card balance would materially improve your position.","Re-checking in ~30 days, after the balance clears, is likely to flip this to a pass."] }
   ```
   **Behaviour:** at `#/rules`, one rule (e.g. *Affordability signal*) resolves to **FAIL** (red), the final rule resolves to **DECLINE / REFER** (not PASS). **No tokenised asset is issued.** Instead show a calm **Decision screen**:
   - Clear outcome chip: **“Declined — not eligible right now”** (use `--danger`).
   - **Why** (which rule failed, in plain English) — transparency is the point.
   - **AI coach (blue) guidance** on *how to improve* and *when to re-apply* (pull from the readiness tips). Reinforce: *the AI did not make the decision — the deterministic rules engine did; the AI only explains it.*
   - A **“Decision receipt”** (a lightweight, time-stamped, **non-asset** verifiable record) the user can keep — *no money product is issued*.
   - Primary action: **“Set a reminder / re-check in 30 days”**; secondary: **“Back to journeys”**.
   - **Readiness on the offer screen** must show the `wait` state (amber/grey ring, ⏳, “Not the right time”), visually distinct from `proceed` (green ✓).

2. **Consent declined.** On `#/consent`, if the user **toggles off a required proof** (or taps a “Decline / Cancel” action), block progression: show a coach message explaining *“Without ‹proof› I can’t run this check — nothing was shared.”* No rules run, no data leaves the wallet. Returning to Journeys leaves state unchanged.

3. **Stale / missing data source.** If a journey needs a proof whose backing **connector is `stale` or `off`** (e.g. Starling stale, Payroll off), surface a **blocker** before consent: *“Your ‹source› needs refreshing to continue.”* Offer a **“Refresh now”** action that flips the connector to `active` (simulated) and unblocks — or a **“Continue without it”** path that leads to a weaker/declined outcome. This makes the data-freshness story tangible.

4. **Third-party handshake expiry / refusal.** On `#/handshake`, run a visible **TTL countdown** (from `ttlMins`, e.g. 15:00 counting down — accelerate for demo so it can hit 0 quickly via a “simulate timeout” affordance). Provide two unhappy outcomes:
   - **User taps “Deny”** → handshake cancelled, *nothing shared*, return to NorthBank with an empty-handed message.
   - **TTL reaches 0** → access **auto-revoked**, presentation invalid, show *“This request expired — for your safety nothing was shared. NorthBank must request again.”*
   This demonstrates *time-limited, revocable, least-privilege* sharing.

5. **Cold-start abandonment (optional but nice).** In `#/signup`, if required minimal fields are empty, validate and prevent continuing; allow the user to **abandon** and show that **no vault is created** until they finish.

> Keep all unhappy paths **calm, transparent and constructive** — the product’s promise is *honest, explainable decisions* and *the customer always stays in control of their data.* Never make the AI appear to “decide”; it only **guides/explains** while the **rules engine decides**.

---

## 7) Mechanics to implement

- **Mock API (`api/mockApi.ts`).** `apiCall(method, path, body)` returns a Promise that resolves after **140–340 ms** random latency, pushing an entry `{ ts, method, path, status, ms, body }` onto a capped (60) **API log** in state. Render a **live log stream** on `#/rules`, `#/handshake`, and `#/log`. Example calls: `GET /api/wallet/customer-001`, `POST /api/presentation/create`, `POST /api/handshake/request`, `POST /api/rules/evaluate`.
- **Rules-engine animation.** Sequentially resolve each consented proof row from a spinner → ✓ (pass) or ✗ (fail), ~250–400 ms apart, then the **final** row to PASS or **DECLINE/REFER**. Drive pass/fail from the journey’s readiness `verdict` (proceed → all pass; wait → designated rule fails).
- **Token issuance.** On PASS, mint a token `{ id, type, code, name, claims, issuedAt, expiresAt, serial, justIssued:true }`, store in `state.tokens`, and mint the matching **tokenised asset** from `assets.json` into `state.assets`. Show **confetti** (respect reduced-motion). On DECLINE, mint only a **Decision Receipt** record — no asset.
- **Selective disclosure.** Consent shows each proof as a **claim, not a document** (“claim / not document” label). The presentation (`#/vp`) lists shared **claims** with an **audience DID** and an **auto-revoke after `ttlMins`** note.
- **Carbon / impact counter (nice-to-have, keep).** Each completed journey increments a small **sustainability** tally (e.g. `+12 sheets of paper saved`, `+1 branch trip avoided`, `+4 manual checks removed`). Show on Home or About.
- **Card freeze toggle** in Wallet (persisted in `state.cardFrozen`).
- **Reset demo** button in the app bar → clears `localStorage`, returns to `#/splash`.
- **Toasts** for actions (“Shared from wallet”, “Connector refreshed”, “Demo reset”).

### State shape (persisted)
```ts
type AppState = {
  onboarded: boolean;
  selectedJourney: string;
  consented: string[];
  tokens: Token[];
  assets: Asset[];
  presentations: Presentation[];
  apiLog: ApiLogEntry[];
  carbon: { sheets: number; trips: number; checks: number };
  cardFrozen: Record<string, boolean>;
  signup: Record<string, string>;
};
```

---

## 8) Acceptance criteria (the build is done when…)

- [ ] App runs with `npm install && npm run dev`, opens to a **mobile-first phone frame**, **no console errors**.
- [ ] **All data comes from JSON files** in `/src/data` (no hard-coded domain data in components) and is **typed**.
- [ ] **5 tabs** work; sub-pages (**Data, Address, Doc**) live under **Identity**; **Assets** is its own tab; **agents are offers, not a tab**.
- [ ] **At least one full happy journey** issues a **token + tokenised asset** with confetti and appears under **Assets**.
- [ ] **Cross-journey reuse** fast-tracks a second journey using a held token.
- [ ] **Outside-the-app** NorthBank **handshake** works with a **TTL countdown** and **selective-disclosure presentation**.
- [ ] **Cold-start** onboarding (`signup → bootstrap`) populates the vault for a new customer.
- [ ] **Unhappy paths all work:** Credit Card **decline** (rule fails, no asset, decision receipt + guidance + readiness `wait` state), **consent declined**, **stale/off connector blocker**, **handshake deny/expiry auto-revoke**.
- [ ] State **persists** across reload via `localStorage`; **Reset demo** clears it.
- [ ] **Accessibility:** focus-visible, aria-labels, reduced-motion, AA contrast.
- [ ] Colours match the **exact tokens** in §2 with the correct **semantic mapping** (green/blue/purple/orange/terracotta).

---

## 9) Tone & copy guidance

- Plain, warm, trustworthy British-bank English. Privacy-first.
- Repeated motifs: **“Share claims, not documents.” · “The AI guides, the rules engine decides.” · “Verify once, reuse everywhere.” · “Time-limited, revocable, you stay in control.”**
- Unhappy outcomes are **honest and helpful**, never punitive: explain *why*, show *how to improve*, say *when to come back*.

---

**Deliver the complete, runnable React project** with all files above, JSON seed data populated exactly, every route implemented, and both happy and unhappy journeys fully working.
