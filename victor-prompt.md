You are an expert React, TypeScript, UX and banking prototype engineer.

Build a complete working React + TypeScript + Vite application called:

Lloyds TrustPass — The Financial Vault

This is a hackathon prototype for a product-agnostic banking journey. The customer chooses a financial product, the AI Coach explains the journey, the customer shares only the required verified proofs from a digital wallet, a deterministic rules engine checks the product-specific eligibility rules, and the journey completes by issuing a product-specific digital token or credential.

Core principle:
AI explains. Wallet proves. Rules decide. Token records.

Important:
- Use React with TypeScript.
- Use Vite.
- Use CSS only, no Tailwind, no component libraries.
- Keep all product, customer and step data in JSON files under src/data.
- Do not hardcode product configuration inside the React component.
- Build a polished, demo-ready UI.
- Use British English.
- Use Lloyds Banking Group / Interstellar-style visual direction: dark green, Lloyds green, lime accent, gold accent, white cards, rounded panels, clean app shell.
- Use Poppins as the first font if available, with Segoe UI and Arial as fallbacks.
- Do not use the Lloyds black horse logo.
- Use the custom TrustPass Financial Vault symbol from the original HTML: a green circular vault/lock symbol with a white lock and green tick.
- The app must run with npm install and npm run dev.
- Make sure there are no TypeScript or CSS errors.
- Use correct CSS spelling: scroll-behavior, not scroll-behaviour.
- Use correct Vite index.html script tag.

Create this project structure:

lloyds-trustpass-react-app/
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts
└── src/
    ├── App.tsx
    ├── main.tsx
    ├── styles.css
    └── data/
        ├── customer.json
        ├── products.json
        └── steps.json

If the project already exists, replace the existing files with the full corrected versions.

Functional requirements:

1. Landing / app shell
- Header must show:
  Lloyds Banking Group TrustPass
  The Financial Vault · Proof. Access. Security. Settlement.
- Header must include the TrustPass Financial Vault symbol, not a horse logo.
- Header chips:
  Interstellar-style CWA shell
  Product agnostic
  AI + rules + wallet + token

2. Hero section
- Title:
  Trusted banking journeys, powered by the Financial Vault
- Supporting text:
  Select a product, let the AI Coach guide the customer, share only the verified proofs needed from the wallet, run deterministic product rules, then issue a product-specific digital token or credential.
- Show four PASS cards:
  Proof — Verified wallet credentials
  Access — Product journey unlocked
  Security — Consent, compliance, audit
  Settlement — Token issued or blocked
- Show the principle:
  AI explains · Wallet proves · Rules decide · Token records

3. Demo customer card
- Use customer data from src/data/customer.json.
- Show:
  customer name
  customer profile
  number of verified proofs
  number of products enabled
  0 full documents shared
  100% consent-led

4. Product selection
- Use product data from src/data/products.json.
- Show product cards:
  Mortgage
  Tokenised Investment
  Personal Loan
  Savings Account
- Each card must show description and the token it issues.
- Clicking a product starts the journey.

5. Journey steps
- Use step labels from src/data/steps.json.
- Steps:
  Choose product
  AI guidance
  Wallet proof
  Consent
  Rules check
  Token issuance
- Show active and completed steps visually.

6. AI Coach
- Right side panel showing AI Coach messages.
- Initial message:
  Welcome to Lloyds Banking Group TrustPass. Choose a product to start a guided Financial Vault journey.
- When product selected, show product-specific coach message from products.json.
- On each journey step, add a short AI Coach explanation.
- AI Coach should explain, not decide.

7. Wallet proof step
- Show all customer credentials from customer.json.
- Highlight credentials required for the selected product.
- Required credentials should be visually distinct using a gold border/background.
- Each credential should show label and value.

8. Consent step
- Show only required proofs for the selected product.
- Each proof should appear with a checked checkbox.
- Show this data minimisation notice:
  Data minimisation: The prototype shares proof outcomes and bands, not full documents such as full payslips, passport numbers or bank statements.
- Button:
  Give consent and run checks

9. Rules engine
- Implement deterministic rules in TypeScript.
- Rule operators:
  equals
  in
- Rules are read from products.json.
- The rules engine checks selected product rules against customer credentials.
- Show each rule result as pass or fail.
- Display:
  field name
  reason
  wallet value
  rule condition
- Button:
  Issue selected token
- Button:
  Simulate blocked journey
- Issue token button must be disabled if any rule fails.

10. Blocked journey simulation
- When clicked, change kycVerified to false if selected product requires kycVerified.
- Otherwise change sanctionsClear to false.
- Re-run rules.
- Show failed rule.
- AI Coach should explain:
  A required proof has failed. The journey is blocked and no token is issued. The customer can be referred to human review if appropriate.

11. Token issuance
- If all rules pass and user clicks Issue Token:
  show Token issued
  show token name
  show token ID
  show owner
  show status Active
- Token details come from products.json.
- Show:
  The [token name] has been recorded in the customer Financial Vault.

12. Token reuse / transfer simulation
- Button:
  Demo transfer / reuse
- When clicked, show:
  Reuse / transfer simulation: The customer tries to reuse the [token name] in another journey. TrustPass checks token status, owner and product rules before allowing reuse.
- Show three rule-style rows:
  Token ownership — pass
  Token status — pass
  Human oversight — warning
- Human oversight text:
  Regulated journeys may still require review or fulfilment outside the prototype.

13. Audit trail
- Right side panel below AI Coach.
- Log:
  prototype loaded
  product selected
  journey advanced
  consent captured
  rules evaluated
  token issued
  blocked scenario simulated
  token reuse checked
  returned to product selection
  demo reset
- Each audit item should show time, title and detail.

14. Reset and change product
- Buttons:
  Reset demo
  Change product
- Reset demo restores customer credentials and returns to product selection.
- Change product returns to product selection but keeps the app running.

15. Guardrail wording
- Show this notice in the journey sidebar:
  Demo guardrail: Mortgage and investment screens provide guidance only. Decisions are simulated and are not financial advice or binding offers.
- The mortgage AI coach message must say:
  This is not financial advice and not a binding mortgage offer.
- The investment AI coach message must say:
  This is education only and not regulated financial advice.

Data files:

Create src/data/customer.json with:

{
  "name": "Maya Patel",
  "profile": "Existing Lloyds customer · Digital wallet active",
  "credentials": {
    "identityVerified": true,
    "addressVerified": true,
    "ageOver18": true,
    "ukResident": true,
    "kycVerified": true,
    "sanctionsClear": true,
    "employmentStatus": "Permanent",
    "incomeBand": "£50k-£60k",
    "depositProof": true,
    "creditEligibilityBand": "A",
    "affordabilityBand": "Good",
    "riskProfile": "Medium",
    "existingCustomer": true,
    "businessVerified": true,
    "directorAuthority": true
  }
}

Create src/data/steps.json with:

[
  "Choose product",
  "AI guidance",
  "Wallet proof",
  "Consent",
  "Rules check",
  "Token issuance"
]

Create src/data/products.json with this exact data:

[
  {
    "id": "mortgage",
    "name": "Mortgage",
    "description": "Check mortgage readiness using wallet proofs and issue a Mortgage Readiness Token.",
    "token": "Mortgage Readiness Token",
    "tokenId": "MORT-READY-001",
    "coach": "I can help check mortgage readiness. I will ask for the minimum proofs needed from the Financial Vault. This is not financial advice and not a binding mortgage offer.",
    "requiredProofs": [
      "identityVerified",
      "addressVerified",
      "ageOver18",
      "ukResident",
      "kycVerified",
      "sanctionsClear",
      "employmentStatus",
      "incomeBand",
      "depositProof",
      "creditEligibilityBand",
      "affordabilityBand"
    ],
    "rules": [
      {
        "field": "identityVerified",
        "operator": "equals",
        "value": true,
        "reason": "Confirm applicant identity"
      },
      {
        "field": "addressVerified",
        "operator": "equals",
        "value": true,
        "reason": "Confirm verified address"
      },
      {
        "field": "ageOver18",
        "operator": "equals",
        "value": true,
        "reason": "Confirm customer is over 18"
      },
      {
        "field": "ukResident",
        "operator": "equals",
        "value": true,
        "reason": "Confirm UK residency"
      },
      {
        "field": "kycVerified",
        "operator": "equals",
        "value": true,
        "reason": "Reusable KYC check"
      },
      {
        "field": "sanctionsClear",
        "operator": "equals",
        "value": true,
        "reason": "Financial crime screening clear"
      },
      {
        "field": "depositProof",
        "operator": "equals",
        "value": true,
        "reason": "Deposit proof available"
      },
      {
        "field": "creditEligibilityBand",
        "operator": "in",
        "value": ["A", "B"],
        "reason": "Initial credit eligibility band accepted"
      },
      {
        "field": "affordabilityBand",
        "operator": "in",
        "value": ["Good", "Strong"],
        "reason": "Affordability band is acceptable"
      }
    ]
  },
  {
    "id": "investment",
    "name": "Tokenised Investment",
    "description": "Use eligibility proofs to access a simulated tokenised investment product.",
    "token": "Investment Access Token",
    "tokenId": "INV-ACCESS-001",
    "coach": "I can explain this investment access journey and check eligibility using verified proofs. This is education only and not regulated financial advice.",
    "requiredProofs": [
      "identityVerified",
      "kycVerified",
      "ukResident",
      "sanctionsClear",
      "riskProfile"
    ],
    "rules": [
      {
        "field": "identityVerified",
        "operator": "equals",
        "value": true,
        "reason": "Confirm customer identity"
      },
      {
        "field": "kycVerified",
        "operator": "equals",
        "value": true,
        "reason": "KYC is required for access"
      },
      {
        "field": "ukResident",
        "operator": "equals",
        "value": true,
        "reason": "Product is available to UK residents in this demo"
      },
      {
        "field": "sanctionsClear",
        "operator": "equals",
        "value": true,
        "reason": "Sanctions screening clear"
      },
      {
        "field": "riskProfile",
        "operator": "in",
        "value": ["Medium", "High"],
        "reason": "Risk profile is compatible with this simulated product"
      }
    ]
  },
  {
    "id": "loan",
    "name": "Personal Loan",
    "description": "Check loan eligibility using income, affordability and credit band proofs.",
    "token": "Loan Eligibility Token",
    "tokenId": "LOAN-ELIG-001",
    "coach": "I can guide an initial personal loan eligibility check using wallet proofs. The outcome is simulated and not a lending decision.",
    "requiredProofs": [
      "identityVerified",
      "incomeBand",
      "affordabilityBand",
      "creditEligibilityBand",
      "sanctionsClear"
    ],
    "rules": [
      {
        "field": "identityVerified",
        "operator": "equals",
        "value": true,
        "reason": "Confirm borrower identity"
      },
      {
        "field": "sanctionsClear",
        "operator": "equals",
        "value": true,
        "reason": "Financial crime screening clear"
      },
      {
        "field": "creditEligibilityBand",
        "operator": "in",
        "value": ["A", "B", "C"],
        "reason": "Credit eligibility band accepted"
      },
      {
        "field": "affordabilityBand",
        "operator": "in",
        "value": ["Good", "Strong"],
        "reason": "Affordability check accepted"
      }
    ]
  },
  {
    "id": "savings",
    "name": "Savings Account",
    "description": "Open a savings journey using reusable identity, address and KYC proofs.",
    "token": "Account Opening Token",
    "tokenId": "SAVE-OPEN-001",
    "coach": "I can help open a savings account journey by requesting only the identity and compliance proofs needed.",
    "requiredProofs": [
      "identityVerified",
      "addressVerified",
      "ageOver18",
      "kycVerified",
      "sanctionsClear"
    ],
    "rules": [
      {
        "field": "identityVerified",
        "operator": "equals",
        "value": true,
        "reason": "Confirm customer identity"
      },
      {
        "field": "addressVerified",
        "operator": "equals",
        "value": true,
        "reason": "Address proof is verified"
      },
      {
        "field": "ageOver18",
        "operator": "equals",
        "value": true,
        "reason": "Customer meets age requirement"
      },
      {
        "field": "kycVerified",
        "operator": "equals",
        "value": true,
        "reason": "KYC proof is reusable"
      },
      {
        "field": "sanctionsClear",
        "operator": "equals",
        "value": true,
        "reason": "Sanctions screening clear"
      }
    ]
  }
]

Vault symbol:

Create a React component called VaultMark inside App.tsx.

Use this exact SVG:

<svg
  viewBox="0 0 64 64"
  role="img"
  aria-label="TrustPass Financial Vault symbol"
  xmlns="http://www.w3.org/2000/svg"
>
  <path
    d="M15 29V19a17 17 0 1 1 34 0v10"
    stroke="white"
    strokeWidth="6"
    strokeLinecap="round"
    fill="none"
  />
  <rect
    x="10"
    y="27"
    width="44"
    height="31"
    rx="8"
    fill="white"
    opacity="0.96"
  />
  <path
    d="M25 43l5 5 11-13"
    stroke="#006A4D"
    strokeWidth="5"
    strokeLinecap="round"
    strokeLinejoin="round"
    fill="none"
  />
</svg>

CSS requirements:

Create src/styles.css with a complete polished layout.

Use these CSS variables:

--lbg-forest: #003c2f
--lbg-green: #006a4d
--lbg-lime: #7ac143
--lbg-mint: #e8f6ef
--lbg-gold: #c8a028
--text: #172b24
--muted: #60756f
--line: #d7e5de

Use this body font:

font-family: "Poppins", "Segoe UI", Arial, sans-serif;

Use this vault mark styling:

.vault-mark {
  width: 82px;
  height: 82px;
  border-radius: 50%;
  background: radial-gradient(circle at 40% 35%, #0aa35d, #003c2f 62%);
  display: grid;
  place-items: center;
  color: #ffffff;
  box-shadow: 0 14px 36px rgba(0, 0, 0, 0.18);
  border: 4px solid rgba(201, 162, 39, 0.35);
  flex: none;
}

.vault-mark svg {
  width: 50px;
  height: 50px;
  display: block;
}

The CSS must include:
- responsive layout
- header
- hero
- panels
- steps
- product cards
- AI coach chat bubbles
- wallet credential cards
- consent cards
- rules pass/fail/warning cards
- token card
- audit trail
- footer

Use media queries:
- below 1100px, layout becomes single column
- below 680px, product grid, wallet grid, pass grid and metric grid become single column

Files to create:

package.json:
- include scripts:
  dev
  build
  preview
- include dependencies:
  react
  react-dom
  vite
  typescript
  @vitejs/plugin-react
- include devDependencies:
  @types/react
  @types/react-dom
- include "type": "module"

index.html:
Must use this exact script tag:
<script type="module" src="/src/main.tsx"></script>

main.tsx:
- import React
- import ReactDOM
- import App
- import styles.css
- render App inside React.StrictMode

tsconfig.json:
- target ES2020
- jsx react-jsx
- resolveJsonModule true
- strict true
- module ESNext
- moduleResolution Node
- noEmit true

vite.config.ts:
- import defineConfig from vite
- import react from @vitejs/plugin-react
- export default defineConfig with react plugin

TypeScript expectations:
- Define types:
  CredentialValue
  Credentials
  RuleOperator
  Rule
  Product
  RuleResult
  AuditItem
- Import JSON data with resolveJsonModule.
- Cast productsData to Product[].
- Cast customerData to expected customer type.
- Cast stepLabelsData to string[].
- Avoid implicit any.
- Ensure no TypeScript errors.

App behaviour details:
- Product selection sets currentStep to 1.
- Wallet proof is currentStep 2.
- Consent is currentStep 3.
- Rules check is currentStep 4.
- Token issuance is currentStep 5.
- Step labels display the current active step.
- Completed steps show a tick.
- Rule results use green for pass, red for fail and amber for warning.
- Simulate blocked journey mutates a local credential state only, not the JSON file.
- Reset demo restores original credentials from customer.json.
- Token issuance only available when all rules pass.
- Token reuse simulation only appears after user clicks Demo transfer / reuse.

Acceptance criteria:
- npm install works.
- npm run dev works.
- No TypeScript errors.
- No CSS unknown property warnings.
- No references to HorseMark or horse-mark.
- The vault symbol displays in the header.
- The app is visually polished and demo-ready.
- All required data lives in JSON files.
- The UI clearly demonstrates:
  AI explains.
  Wallet proves.
  Rules decide.
  Token records.