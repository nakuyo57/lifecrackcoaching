# LifeCrack Coaching Session Assessor
## Deployment Guide — Netlify + Edge Function Proxy

---

### What's in this package

```
lifecrack-deploy/
├── netlify.toml                          # Routes /api/claude → edge function
├── public/
│   └── index.html                        # The full assessor app (no API key inside)
└── netlify/
    └── edge-functions/
        └── claude-proxy.js               # Server-side proxy — holds the API key
```

The API key lives **only** in Netlify's environment variables — never in the browser or the HTML file.

---

### Step 1 — Create a Netlify account
Go to https://netlify.com and sign up (free tier is sufficient).

---

### Step 2 — Deploy the site

**Option A — Drag and drop (simplest)**
1. In Netlify dashboard → "Add new site" → "Deploy manually"
2. Drag the entire `lifecrack-deploy/` folder into the upload zone
3. Netlify detects `netlify.toml` automatically and configures the edge function

**Option B — GitHub (recommended for ongoing updates)**
1. Push this folder to a GitHub repository
2. In Netlify → "Add new site" → "Import an existing project" → connect GitHub
3. Set build settings:
   - Build command: *(leave empty)*
   - Publish directory: `public`
4. Deploy

---

### Step 3 — Add your Anthropic API key

1. In Netlify dashboard → your site → **Site configuration** → **Environment variables**
2. Click "Add a variable"
3. Key: `ANTHROPIC_API_KEY`
4. Value: your Anthropic API key (get one at https://console.anthropic.com)
5. Scope: **All scopes** (or at minimum: Functions)
6. Save — Netlify redeploys automatically

Your API key is now stored securely server-side. It is never sent to the browser.

---

### Step 4 — Set your cohort codes

Open `public/index.html` and find this line near the top of the `<script>` block:

```javascript
const COHORT_CODES = ['LIFECRACK2025', 'LC-COHORT1', 'DEMO'];
```

Replace with your own codes. Use one per cohort so you can rotate them:

```javascript
const COHORT_CODES = ['C5-AUTUMN2025', 'C6-SPRING2026'];
```

Save and redeploy (drag-drop again, or push to GitHub if using Option B).

---

### Step 5 — (Optional) Set a custom domain

In Netlify → **Domain management** → "Add a domain"
Example: `assessor.lifecrack.com`

Netlify provides free SSL automatically.

---

### Managing API costs

Each session analysis uses approximately 2,000–4,000 input tokens (transcript) + ~500 output tokens.
Each report generation uses ~1,000 input tokens + ~1,500 output tokens.

At claude-sonnet-4 pricing, one full session assessment (analysis + report) costs roughly $0.05–$0.15 USD depending on transcript length.

Monitor usage at https://console.anthropic.com/usage.

To set a monthly spend cap: Anthropic console → **Billing** → **Usage limits**.

---

### Rotating cohort access

When a cohort completes the program:
1. Remove their code from `COHORT_CODES` in `index.html`
2. Add the new cohort's code
3. Redeploy

Past sessions are not stored server-side — all data lives in the assessor's browser session only.

---

### Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| "API key not configured" error | Env var not set or wrong name | Check `ANTHROPIC_API_KEY` in Netlify env vars |
| Analysis fails silently | Transcript too short or malformed JSON response | Try a longer transcript; check browser console |
| Edge function not found | `netlify.toml` not at root of deployed folder | Ensure folder structure matches above |
| CORS error in browser console | Deploying HTML file directly without Netlify | Must be served through Netlify, not opened locally |

---

### Support
For LifeCrack framework questions, contact your program facilitator.
For Netlify issues: https://docs.netlify.com
For Anthropic API issues: https://docs.anthropic.com
