---
note_type: Note
created: 2026-10-08 12:00
company: Ensemble Weather AI
role: Full-Stack Engineer
status: prep
source: https://www.ensemblegains.com/careers/full-stack-engineer
apply: https://www.ensemblegains.com/careers/full-stack-engineer/apply
wellfound: https://wellfound.com/jobs/4781678-full-stack-engineer
---

# Ensemble Weather AI — Full-Stack Engineer

Interview prep capture from the live posting on 2026-10-08. Official source is the company careers page; Wellfound is a syndication with a few extra logistics fields.

You're probably wondering why this isn't just "React + API job, apply and talk." It is that job. The twist is they are hiring a contractor to *own the customer surface* while the forecast science is still being built. That is product engineering, not a ticket queue.

**Pause and predict:** If they say "own everything customers touch," what three systems are they actually pointing at?

Answer: marketing site + docs, customer dashboard, and the paid `/v1` API (accounts, keys, usage, billing, alerts). Not the model training loop.

---

## Snapshot

| Field | Value |
|:---|:---|
| Company | Ensemble Weather AI |
| Site | [ensemblegains.com](https://www.ensemblegains.com/) |
| Role | Full-Stack Engineer |
| Type | Independent contractor, full-time intent |
| Pay | **$40–$80/hr** published on the posting |
| Equity | None (Wellfound: "No equity") |
| Location | Remote across the Americas (US, Canada, Mexico, Argentina) |
| Wellfound locations | California, Pennsylvania, Texas, Aguascalientes |
| Hours | Collaboration window 8:00 AM–8:00 PM ET |
| Intro calls | Weekdays 8:00 AM–4:00 PM ET, 15 min, booked ≥24h ahead |
| Company size | 1–10 (Wellfound) |
| Stage | Early. Ingest, ensembling layer, and v1 API are being built now |
| Experience listed | 4 years (Wellfound) |
| Visa | Not available |
| Relocation | Not allowed |

Rate check against a $30/hr floor: even the bottom of their range clears it. Mid-range $60/hr is roughly $125k FTE-equivalent before contractor tax and benefits. $80/hr is roughly $166k equivalent. Ask for the high half if the conversation is about owning the whole customer surface.

---

## Official job text (verbatim)

### Headline

Own everything customers touch: the website, the dashboard, and the API itself — accounts, keys, usage, billing and alerts. Make it fast, clear and beautiful.

### What you'll do

* Build and polish the marketing site, docs and customer dashboard (Next.js, React, Tailwind)
* Build the `/v1` API customers pay for: sign-up, API keys, rate limiting, usage metering and Stripe billing
* Show forecasts and their uncertainty clearly — maps, radar loops and storm-track ensembles
* Keep it fast and accessible; the site is image-heavy and is read at 3am during a storm

### What we look for

* Real production experience with TypeScript, React and Next.js
* Backend experience building and running an API: Node or Python, Postgres, authentication
* A strong eye for design and detail — you care how things look and read, not just whether they work
* Maps or data-visualisation experience (MapLibre, deck.gl, D3) is a strong plus

### How hiring works

1. **Apply** — One page, about five minutes. LinkedIn and CV do most of the talking.
2. **Verify your identity** — About three minutes: phone, an ID document, and a quick live selfie, run by a specialist identity provider. They do this early so a fit can go straight to interview.
3. **We review** — They claim to read every application. If it's a fit, email within a week with a 15-minute intro call (weekdays, 8am–4pm ET, ≥24 hours ahead).
4. **Talk and agree terms** — Intro call, a technical conversation about work you've actually done, then contract terms, rate, and start date in writing.

That last line is the whole interview strategy. They are advertising a work-history conversation, not a whiteboard gauntlet.

### Pay (their words)

$40 – $80 per hour as an independent contractor, depending on experience. We publish the range on every role so you know before you apply, not after three interviews.

### About Ensemble Weather AI (from the posting)

We combine many independent AI weather models into one better-calibrated forecast and deliver it as an API — severe storms, nowcasting, air quality and long-range climate. It's early: the ingest pipelines, the ensembling layer and the v1 API are being built now, so the people who join next will own that core rather than maintain someone else's.

---

## What the product actually is

**Pause and predict:** Most weather APIs return one number. What does Ensemble return instead, and why would a utility care?

Answer: a distribution — p10 / p50 / p90 plus named model weights. A utility does not want "28°C". It wants to know the models disagree by four degrees, and hedge.

### Pitch from the homepage

* No single weather model wins everywhere. They run many and combine them, and they tell you how much the models disagree.
* Free tier: 1,000 calls/day, ensemble spread included, no sales call.
* Every response carries spread across members and the weight of each contributing model.
* They verify against ERA5 reanalysis and score against ECMWF IFS. They currently lose on precipitation beyond day seven and they put that on the accuracy page.
* Customers: energy/utilities, insurance/reinsurance, agriculture, logistics/aviation, emergency management, developers.

Example envelope from the homepage (Houston point, 2026-09-26):

```json
{
  "init_time": "2026-09-23T00:00:00Z",
  "valid_time": "2026-09-26T12:00:00Z",
  "location": { "lat": 29.76, "lon": -95.37 },
  "members": 42,
  "models": [
    { "name": "graphcast", "weight": 0.31 },
    { "name": "aurora", "weight": 0.29 },
    { "name": "aifs", "weight": 0.22 },
    { "name": "gencast", "weight": 0.18 }
  ],
  "spread": {
    "temperature_2m": { "p10": 24.1, "p50": 28.3, "p90": 32.4 },
    "wind_gust_10m": { "p10": 7.2, "p50": 11.8, "p90": 19.6 }
  },
  "units": "metric",
  "resolution_deg": 0.25
}
```

Know those four model names. You do not need to derive GraphCast. You do need to say, without flinching, that the product is *calibrated disagreement*, not a prettier radar widget.

### `/v1` surface (from [docs](https://www.ensemblegains.com/docs))

One versioned API, bearer token. JSON by default; GeoJSON and NetCDF on grid endpoints. Spread is not a flag and not a paid extra.

Quickstart they publish:

```bash
curl https://api.ensemblegains.com/v1/forecast \
  -H "Authorization: Bearer $EG_API_KEY" \
  -G \
  -d lat=29.76 \
  -d lon=-95.37 \
  -d vars=temperature_2m,wind_gust_10m \
  -d horizon=72h
```

| Method | Path | What it is | Tier |
|:---|:---|:---|:---|
| GET | `/v1/forecast` | Point forecast, p10/p50/p90 per step | Developer |
| GET | `/v1/forecast/grid` | Bounding-box grid, NetCDF or GeoJSON | Builder |
| GET | `/v1/nowcast` | Minute precipitation, 0–120 min | Builder |
| GET | `/v1/severe` | Tornado, hail, wind, cyclone tracks | Professional |
| GET | `/v1/severe/cyclones` | Tropical cyclones with per-member tracks | Professional |
| GET | `/v1/air-quality` | PM2.5, PM10, ozone, NO₂, AQI, pollen | Builder |
| GET | `/v1/climate/seasonal` | 1–12 month, ENSO, drought probability | Enterprise |
| GET | `/v1/historical` | ERA5 reanalysis, 1940–present | Professional |
| GET | `/v1/ensemble/members` | Per-model member values and weights | Professional |
| GET | `/v1/skill` | Live verification scores, public, unauthenticated | Developer |
| POST | `/v1/alerts` | Threshold alert with webhook | Professional |
| GET | `/v1/alerts` | List / inspect / delete subscriptions | Professional |

Reliability claims they will expect you to protect, not admire:

* `X-RateLimit-*` on every response
* `429` + `Retry-After` — never a bare failure
* Point forecast p95 < 200 ms cached, < 800 ms uncached
* Nowcast p95 < 150 ms — "a late nowcast is not a nowcast"
* OpenAPI 3.1 generated from source
* TypeScript and Python SDKs at launch
* Full per-endpoint reference is still "in progress" with Phase 1

This role builds the *paid wrapper* around that science: keys, metering, Stripe, dashboard, docs, maps. If you walk in talking about training GraphCast, you missed the job.

---

## Fit map

**Pause and predict:** Which of your five stories is the closest analog to "API keys, rate limits, usage metering, Stripe"?

Answer: Calltext (third-party APIs, rate limiting, webhooks) plus Makpar (auth, API Gateway throttling, JWT). Stripe itself is the gap to name, not hide.

| They want | You have | How to say it | Gap |
|:---|:---|:---|:---|
| TypeScript + React | Makpar SPA, TypeScript, Redux | Lead with production React, not "I also know Vue" | Next.js App Router / RSC not shipped as a named product |
| Next.js + Tailwind | React + CSS systems; you can pick up Next | "I've shipped React/TS production apps. Next.js is the framework I'd use for the marketing site + dashboard because docs, SEO, and the app can live in one repo." | Do not fake a Next.js war story |
| Node or Python API + Postgres + auth | Python Lambda + API Gateway + RDS/Dynamo + JWT | "Python API, Postgres/RDS for durable records, auth as a first-class concern — that's the SBA portal." | Node is optional; they listed Python on purpose |
| API keys, rate limits, usage, Stripe | Makpar throttling; Calltext rate limits + webhooks | "I've built metered, authenticated APIs. Stripe Checkout + webhooks + usage records is the piece I'd implement first, because that's how the API gets paid." | Stripe Billing / metered subscriptions — study before the tech call |
| Maps, radar, ensembles (plus) | Data-heavy UIs, not MapLibre/deck.gl/D3 as a specialty | Treat as a plus. "I haven't shipped MapLibre. I have shipped image-heavy, high-stakes UIs and I care about the 3am readability problem." | One evening with MapLibre + a GeoJSON overlay is enough to not freeze |
| Design / detail / 3am storm UX | Performance work on Makpar lists; accessibility as discipline | Talk about load, contrast, no-flicker, virtualization, "the page has to work when the user's heart rate is 140" | They will judge writing and visual taste on the application itself |
| Own the customer surface at a 1–10 person company | Red Pocket CTO, small-team ownership | "I've been the person who owns the thing customers touch, not a slice of a 40-person platform team." | Contractor, no equity — confirm they actually want a founder-level owner vs a cheap pair of hands |
| Work-history interview, ET hours, remote Americas | ET-willing, remote, hates whiteboard loops | This is the process you want. Pre-screen already passed on format. | Identity verification step is unusual; just do it |

Wellfound also listed React Native and HTML/CSS. The **careers page does not**. Do not volunteer React Native. If asked, you can say mobile is not the posted surface.

---

## Stories to bring (order)

1. **Makpar SBA portal** — React/TS SPA, Python API, auth, rate limiting, image/list performance, government-grade "this cannot be wrong at 3am" energy. Closest to dashboard + API.
2. **Calltext** — third-party APIs, webhooks, Redis rate limiting, usage-ish campaign sending. Closest to Stripe/metering/alerts.
3. **Ronati sync** — Python, validation, idempotency, ship under a deadline. Closest to "early, the v1 is being built now."
4. **Red Pocket** — only if they ask who owns product vs engineering at 1–10 people.

Keep GSATi in reserve for "messy early codebase" questions.

---

## 30-second pitch for *this* role

I'm a full-stack engineer who owns the customer surface: the app people click and the API they pay for. Most recently I led a React/TypeScript portal with a Python API, JWT auth, and production testing for an SBA customer system — the kind of product that has to be correct when nobody is watching. Before that I was CTO at a small company, so I'm used to building the billing-adjacent API, the dashboard, and the public site without a platform team behind me. Ensemble's job is that shape: Next.js + a metered `/v1` with keys, usage, and Stripe, plus making uncertainty readable on a map at 3am. That's the work I want.

---

## Likely technical conversation

They said they will talk about work you have actually done. Still prepare to go one level down on *their* domain, because a 15-minute intro that goes well becomes a deeper call.

### 1. Metered public API

You're probably wondering why this isn't just "slap API Gateway in front of Postgres." Because the product *is* the meter. If keys, quotas, and Stripe drift, the forecast science does not get paid.

Sketch if asked how you'd build `/v1` billing:

* `customers` / `api_keys` / `usage_events` / `invoices` in Postgres
* Key hashed at rest, prefix shown in the dashboard (`eg_live_sk_…`)
* Every request: authenticate → check plan entitlements (which endpoints) → increment usage atomically → enforce `X-RateLimit-*`
* Stripe Customer + metered Billing Meter or subscription items; webhooks for `invoice.paid` / `invoice.payment_failed` to freeze keys
* Idempotency keys on signup and webhook handlers
* Free tier: 1,000 calls/day is a product promise, so the counter has to be correct, not "eventually consistent enough"

Tie to Makpar (authorizers, throttling) and Calltext (webhooks, Redis).

### 2. Uncertainty UI

A map that only plots p50 is lying. The job is to show spread without turning the screen into a statistics lecture.

Talk in user terms:

* Cone / fan for cyclone tracks (per-member ensemble)
* Dual readout: "28°C" with a quiet "24–32°C, models disagree"
* Radar loop that does not jank on a phone at 3am
* Accessibility: not color-only, because a tired operator in a dark room is the actual user

If they bite on MapLibre/deck.gl: "I haven't shipped those libraries in production. The problem I *have* shipped is making a dense, image-heavy UI fast and readable. I'd start with MapLibre for tiles and a GeoJSON overlay for members, and I would not invent a custom WebGL stack in week one."

### 3. Next.js vs the SPA you shipped

Honest split:

* Marketing + docs: Next.js, because SEO and MDX/docs want a server.
* Dashboard: Next.js App Router is fine; the hard part is auth session + Stripe customer portal, not the framework.
* You shipped a React SPA on S3/CloudFront. That is still the right story for client performance. Do not apologize for it. Say when you'd choose SPA (pure app, CDN) vs Next (site + app + docs in one repo, which is what they listed).

### 4. Postgres vs Dynamo

They named Postgres. Use RDS/Postgres as the system of record for accounts, keys, invoices. Cache hot forecast envelopes (the 200 ms p95) in Redis or CDN. Do not propose Dynamo as the billing ledger.

### 5. "Nowcast p95 < 150 ms"

This is an SLO, not a slogan. Cached grid in front of the model output. The full-stack job is: don't let the dashboard, the docs images, or a chatty client blow that budget. Image-heavy marketing site is a separate performance problem (Next `Image`, srcset, not shipping 3840px floods into a phone).

---

## Questions to ask them

Pick three. Do not ask what the company does.

**Technical**

1. Is the `/v1` billing/auth layer already sketched, or is the first hire writing it on a green field?
2. What's generating the OpenAPI today — hand-written, or is "generated from source" already true?
3. Where does the dashboard read forecasts from — the public `/v1`, or an internal admin path?

**Product / 3am**

4. Who is the 3am user you keep talking about — a utility operator, an emergency manager, or a developer integrating during an outage?
5. What does "beautiful" mean here: marketing polish, or operational clarity under stress?

**Business / contract**

6. What does success look like in 90 days — dashboard live, Stripe live, or docs + keys for the free tier?
7. Contractor in practice: hours expectation inside the 8am–8pm ET window, and is $40–$80 a true experience band or a LATAM-vs-US split?
8. How many people currently touch the customer surface? If the answer is "you would be the first," that is the job.

Do not ask salary on the intro call. They already printed it.

---

## Process notes / gotchas

* **Identity verification before interview.** Phone + ID + live selfie. Unusual for a 1–10 weather startup. Do it; don't editorialise in the application.
* **One-page apply, CV + LinkedIn do the talking.** Application writing is part of the design test. Short, specific, no fluff.
* **Intro is 15 minutes ET.** Have the pitch and one story cold. They will not give you a warmup lap.
* **Tech call is "work you've actually done."** Lead with Makpar + Calltext. If they start a live Next.js puzzle, that's a process change — note it.
* **Contractor, no equity.** Fine if the rate is real and the work is ownership. Confirm they are not using "contractor" to avoid a hire they actually need as staff.
* **Wellfound $30k–$80k** is a messy annualization of a contractor band. Trust the careers page: **$40–$80/hr**.
* **Accuracy page and `/v1/skill`** are culture signals. They want people who will publish losses. Don't oversell.

---

## Application / close lines

**Application, ~4 sentences:**

I've shipped production React/TypeScript apps and Python APIs with auth, rate limiting, and the unglamorous billing-adjacent work that makes an API a product. Most recently that was a customer portal for the SBA; before that I was CTO at a small company and owned the surface customers actually used. Ensemble's `/v1` — keys, metering, Stripe, a dashboard that can show forecast spread without lying — is the same shape of problem. I care how it looks at 3am, not just whether the endpoint returns 200.

**Close on the intro call:**

I'm interested because you're hiring someone to own the customer surface while the science is still coming online, and you interview on real work instead of puzzles. I've built that layer before. If the next step is a deeper conversation about the API and dashboard, I'm in. What does scheduling look like?

---

## Links

* Job: https://www.ensemblegains.com/careers/full-stack-engineer
* Apply: https://www.ensemblegains.com/careers/full-stack-engineer/apply
* Home: https://www.ensemblegains.com/
* Docs: https://www.ensemblegains.com/docs
* Accuracy: https://www.ensemblegains.com/accuracy
* Wellfound: https://wellfound.com/jobs/4781678-full-stack-engineer

## Pre-interview 10 minutes

1. Pitch above (1 min)
2. Makpar + Calltext stories (5 min)
3. Sketch keys → rate limit → Stripe webhook on paper (2 min)
4. Questions 1, 4, 6 (2 min)


---

You're probably wondering why this isn't just a React job with a weather skin. The posting is narrower than that: **own the customer surface** — marketing site, docs, dashboard, and the paid `/v1` (keys, rate limits, usage, Stripe). The models are someone else's problem until they hit the API.

**What they printed**

- **$40–$80/hr** contractor, no equity. Floor is above your $30/hr line; treat Wellfound's `$30k–$80k` as a bad annualization and ignore it.
- Remote Americas, intro calls **8am–4pm ET**, collaboration window **8am–8pm ET**.
- Process: one-page apply → ID/selfie verify → they claim a 1-week reply → 15-min intro → **technical talk about work you've actually done** → terms in writing. That's the interview format you want.
- Stack on the page: **Next.js, React, Tailwind, TypeScript**, backend **Node or Python + Postgres + auth**. Maps (MapLibre / deck.gl / D3) are a plus, not the bar.

**Prep angle in the note**

Lead **Makpar** (React/TS + Python API + auth/throttling) and **Calltext** (webhooks, rate limits). Name Stripe as the gap instead of bluffing it. Next.js App Router is the same: "I shipped production React; Next is the right shell for site + docs + dashboard," not a fake Next war story.

The note has the verbatim posting, `/v1` endpoint table, a role-specific pitch, a metering sketch, and questions that test whether you'd actually own v1 or just polish the marketing site.

---

Ensemble Weather AI is building a new kind of weather forecast.  
  
No single weather model is right everywhere. Each one wins in different places, seasons and time ranges, and fails in different ways. We run many of today's leading AI weather models side by side and combine them into one forecast that is more reliable than any of them alone. Many models. One forecast.  
  
What makes us different is that we show our uncertainty. Every forecast comes with a range and tells you how much the models agree, because a business deciding whether to move crews, stock or people needs to know how sure the forecast is, not just what it says. And we measure our accuracy openly against the official European forecast, including where we fall short.  
  
What we forecast:  
  
Severe weather: hurricane tracks and the conditions that produce tornadoes, hail and damaging winds  
Rain and flash floods: minute-by-minute, up to two hours ahead  
Long-range climate: seasonal drought, El Niño and long-term trends  
Air quality: wildfire smoke, pollution and pollen  
Who it's for: energy and utility companies, insurers, agriculture, logistics and aviation, emergency services, and developers building weather into their own products.  
  
Where we are: our work covers the whole globe, with our deepest accuracy focus on North America. We're at an early stage, building the core technology now, and we're hiring across the US and Latin America: AI and data engineers, full-stack engineers, weather data specialists, marketing, and a meteorologist.

---

