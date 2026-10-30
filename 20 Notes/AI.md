---
note_type: Note
created: 2026-10-04 12:05
---
>  Artificial intelligence tools, models, and how I use them


# Frontier AI Providers 

## 1. OpenAI — GPT-5.6 family

OpenAI's current lineup is a three-tier family (all with a 1.05M-token context window), plus the older GPT-5.5 at 1M context. 

| Model | Context | Cost (in / out per MTok) | Good at | Not good at |
|---|---|---|---|---|
| GPT-5.6 Sol (flagship) | 1.05M | $4 / $20 (promo through Nov 21, 2026) | Agentic coding, browsing, computer use — top scores on Terminal-Bench (88.8), BrowseComp, OSWorld | Premium tier pricing jumps to $8/$30 beyond 272K context |
| GPT-5.6 Terra | 1.05M | $2 / $12 | Balanced, high-volume production work | Mid-tier reasoning vs. Sol |
| GPT-5.6 Luna | 1.05M | $0.20 / $1.20 | Everyday tasks, cheap bulk processing | Not for hard reasoning or long-horizon agents |

Strengths overall: deepest tooling ecosystem, structured outputs, function calling, strong agentic benchmarks. Weakness: long-context surcharges and the priciest "pro" SKUs in the industry.

---

## 2. Anthropic — Claude Opus / Sonnet / Haiku

Anthropic's tiers map cleanly to *think hard / balanced / fast*. Models from Claude 4.6 onward include the full 1M-token context at standard per-token rates, with no long-context surcharge.

| Model | Context | Cost (in / out per MTok) | Good at | Not good at |
|---|---|---|---|---|
| Claude Opus 5.5 | 1M | $4 / $20 | Long-running agentic coding, knowledge work; the most reliable autonomous coding agents on production codebases | Expensive for high-volume chat |
| Claude Sonnet 5.5 | 1M | $2 / $10 | Best speed/intelligence balance; everyday engineering work | Half the ceiling of Opus on hard reasoning |
| Claude Haiku 4.5 | ≤200K | $1 / $5 | Fastest responses, near-frontier quality for classification/extraction | Shorter context, weakest deep reasoning |

Strengths: agentic coding reliability (Opus-tier leads SWE-bench Pro at ~69%), predictable flat pricing across the full 1M window, strong prompt caching (~10x cheaper cache hits). Weaknesses: no image/video generation, conservative refusals on edge cases. 

---

## 3. Google DeepMind — Gemini 3.x

Google's differentiator is **native multimodality** — text, image, audio, and video processed in a single model pass, up to ~9.5 hours of audio or 1 hour of video per call. 

| Model | Context | Cost (in / out per MTok) | Good at | Not good at |
|---|---|---|---|---|
| Gemini 3.1 Pro (flagship) | 1M | $2 / $12 (≤200K); $4 / $18 (>200K) | Multimodal apps, video/long-doc analysis, scientific reasoning (GPQA ~90.8%), fast output (~138 t/s) | Price doubles over 200K context; notable hallucination on unknown facts; closed weights |
| Gemini 3.8 / 3.7 Flash | 1M | $0.75 / $3.75 (promo through Dec 31, 2026, then $1.50 / $7.50) | Long-horizon agents, document-heavy workflows at low cost | Promo pricing expires Jan 1, 2027 |

Strengths: cheapest frontier-class input pricing, every tier gets 1M context, free API tier for prototyping on Flash. Weaknesses: the 200K pricing cliff can surprise budgets; confident fabrication when the model doesn't know something.

---


## 4. Meta AI — Llama 4 (open weights)

Meta plays a different game: **you download the weights and self-host**, paying only for compute (or pennies via managed providers). Both flagships are natively multimodal mixture-of-experts models under the Llama 4 Community License.

| Model | Context | Cost | Good at | Not good at |
|---|---|---|---|---|
| Llama 4 Scout (109B total / 17B active) | 10M (≈5M effective) | Free weights; cheap managed hosting | Long-context retrieval — needle-in-haystack over huge corpora, legal review, repo scans, on-prem/privacy work | Synthesis across buried tokens collapses (15.6% on Fiction.LiveBench at 128K vs. Gemini's 90.6%); heavy quantization degrades quality past ~5M |
| Llama 4 Maverick (~400B total / 17B active) | 1M | ~$0.27 / $0.85 managed (e.g., Together AI) | Stronger writing and reasoning; whole-document rewrites; fits a single H100 host | Trails the closed frontier on aggregate intelligence |

Also note: Meta shipped **Muse Glimmer** (30B, true Apache-2.0 license) in August 2026. Weaknesses overall: the Community License isn't OSI-approved open source, and raw capability lags GPT/Claude/Gemini flagships.

---

## 5. xAI — Grok 4.x

xAI's niche is **real-time awareness** (native X/Twitter + web search baked in) and aggressive pricing.

| Model | Context | Cost (in / out per MTok) | Good at | Not good at |
|---|---|---|---|---|
| Grok 4.7 | 500K | $2 / $6 (≤200K); $4 / $12 (>200K) | Live, current-events-aware answers; long-horizon agentic tasks at ~¼ the per-task cost of rivals; native tool use | Smaller context than competitors; lags on coding and agentic maturity vs. Claude/GPT |
| Grok 4 Heavy | 500K | Subscription (SuperGrok Heavy) | Highest-reasoning tier, multi-agent "Heavy" mode | Not the value play; API access more limited |

Strengths: frontier-adjacent intelligence at $2/$6 — arguably the best cost-per-task value of 2026 — plus real-time search integration. Weaknesses: 500K context ceiling, thinner enterprise tooling. 

---

## Summary Cheat Sheet

| If you need… | Pick | Why |
|---|---|---|
| Autonomous coding agents on real repos | Claude Opus 5.5 / GPT-5.6 Sol | Top agentic-coding benchmarks |
| Video, audio, or mixed-media input | Gemini 3.1 Pro | Only natively multimodal frontier |
| Cheapest frontier intelligence | Grok 4.7 or Gemini Flash | $2/$6 and $0.75/$3.75 respectively |
| Privacy / self-hosting / no API dependency | Llama 4 Scout or Maverick | Open weights, run on your own hardware |
| Massive single-shot documents | Llama 4 Scout (retrieval) or Gemini (synthesis) | 10M nominal vs. 1M-but-reliable context |
| Real-time news/social awareness | Grok 4.7 | Native X + web search |

**Takeaway:** there's no single "best" model in late 2026 — the market has specialized. Claude owns agentic coding reliability, OpenAI owns tooling breadth, Google owns multimodal and price-to-context, Meta owns openness and self-hosting, and xAI owns real-time data and raw value. The professional skill is matching the workload to the tier — and using cheap tiers (Luna, Haiku, Flash) for the 80% of tasks that don't need a flagship. 

*Note: pricing in this industry changes monthly — verify against each provider's official pricing page before budgeting.*


 