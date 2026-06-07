# ACN — Changelog

Append-only. Most recent entry first.

---

## [2026-05-31] — Workflow B + v3.0 Schema Upgrade

**Trigger**: Scheduled incremental update (2026-05-18 → 2026-05-31) + v3.0 schema rewrite.
**Sources reviewed**: [Yahoo Finance](https://finance.yahoo.com/quote/ACN), [stockanalysis.com forecast](https://stockanalysis.com/stocks/acn/forecast/), [stockanalysis.com ratings](https://stockanalysis.com/stocks/acn/ratings/), [Finviz](https://finviz.com/quote.ashx?t=ACN&ty=si), [Accenture newsroom — OpenAI Federal partnership](https://newsroom.accenture.com/news/2026/accenture-federal-services-and-openai-partner-to-accelerate-secure-ai-adoption-across-the-federal-government), [Benzinga coverage](https://www.benzinga.com/markets/large-cap/26/05/52568818/accenture-expands-federal-ai-push-with-openai)

### What Changed
- **Schema**: v2.14 → v3.0; rule cross-references corrected (§6 Rule #25 → #23; §12 Rule #26 → #24).
- **Price**: $168.82 (May 15) → $187.07 (May 29 close); 52-wk high corrected $322.86 → $321.77. Market cap ~$103.7B → ~$115B. Short interest: 3.5% / 3.1 DTC → 3.94% / 3.83 DTC.
- **Valuation**: Forward P/E ~12.3x → ~13.6x; FCF yield ~10% → ~9.5%; div yield 3.9% → ~3.5%. R/R ~3.3:1 → ~2.2:1 at new spot; upside to PW EV ~38% → ~25%. Spot now marginally above 20% MoS entry ceiling (~$186); recommendation adjusted Watch/Initiate-on-dips / Hold.
- **Buyback authority**: Corrected $7.9B (FY25 year-end) → $4.4B remaining (as of Q2 FY26 end, per [Q2 FY26 PR](../../../raw/ACN/press-releases/2026-03-19-Q2-FY2026-results.htm)).
- **§4 Management — Outsider grade added**: **Outsider-leaning** — countercyclical buybacks at trough multiples (~12–13x fwd EPS) tempered by 20-yr dividend-growth streak; same tier as BKNG. Added to cross-ticker table in [outsiders.md](../../frameworks/outsiders.md).
- **§5 / §3 / §9 — OpenAI Federal partnership** (May 14, 2026 — missed in initial ingest): Accenture Federal Services + OpenAI collaboration; 15,000 professionals with secure OpenAI access; FedRAMP-aligned Agentic Lab at The Forge. Deepens ecosystem moat; partially counters federal-headwind narrative.
- **§6 — US federal risk** updated: "mostly priced + partially mitigating" (OpenAI partnership opens a new AI-transformation wedge but does not reverse the near-term budget freeze).
- **§9 — Analyst consensus**: 21 → 28 analysts; median target $260 → $248.50; one sell removed; hold count 5 → 10. DBS (May 28) maintains Hold, PT cut $216 → $190. UBS maintained Buy (PT $320) in May. Q3 FY26 earnings confirmed June 18, 2026.
- **§10 — Technical lens**: stock moved from ~5th %ile to ~19th %ile of 52-wk range; prior near-52-wk-low tailwind reduced.

### Thesis Status
- **Overall**: Unchanged — wide-moat capital-light compounder still at trough multiples. OpenAI Federal partnership is a mild thesis-positive (counters the federal-headwind narrative). Higher spot price reduces asymmetry modestly but thesis is intact.
- **BAIT delta**: B-Strong unchanged; T-Moderate — stock rose from near 52-wk low.
- **Price target delta**: Scenarios unchanged — Bull $300 | Base $235 | Bear $135. PW EV ≈ $233.
- **Catalyst & Sentiment delta**: DBS PT cut ($216 → $190); analyst count expanded + median PT compressed ($260 → $248.50); earnings confirmed Jun 18, 2026.

### Recommendation
- **For a non-holder**: Watch / Initiate on dips — spot $187 is just above the 20% MoS entry ceiling (~$186); prefer accumulating ≤ $185.
- **For a current holder**: Hold — still 25% below PW EV; add on dips below $185.

**Next review trigger**: Q3 FY2026 earnings **June 18, 2026**.

---

## [2026-05-18] — v2 Initial Ingest

**Trigger**: First-run Workflow A ingest of Accenture plc (NYSE: ACN).
**Sources reviewed**:
- 10-Ks FY2021–FY2025 ([raw/ACN/filings/ACN-10K-FY2021..2025.htm](../../../raw/ACN/filings/))
- 10-Qs Q1 & Q2 FY2026 ([raw/ACN/filings/](../../../raw/ACN/filings/))
- Earnings press releases Q3 FY25, Q4/FY25, Q1 FY26, Q2 FY26 ([raw/ACN/press-releases/](../../../raw/ACN/press-releases/))
- Earnings call transcripts Q3 FY25, Q4 FY25, Q1 FY26, Q2 FY26 ([raw/ACN/transcripts/](../../../raw/ACN/transcripts/))
- DEF 14A 2025 (2026 AGM) ([raw/ACN/filings/ACN-DEF14A-2025.htm](../../../raw/ACN/filings/ACN-DEF14A-2025.htm))
- Shareholder letters FY2021–FY2025 ([raw/ACN/shareholder-letters/](../../../raw/ACN/shareholder-letters/))
- 8-K 2025-06-20 growth-model / leadership reorg ([raw/ACN/filings/ACN-8K-2025-06-20-growth-model-reorg.htm](../../../raw/ACN/filings/ACN-8K-2025-06-20-growth-model-reorg.htm)); 8-K 2026-04-24
- Live data: [Yahoo Finance](https://finance.yahoo.com/quote/ACN), [stockanalysis.com](https://stockanalysis.com/stocks/acn/), [Finviz short interest](https://finviz.com/quote.ashx?t=ACN&ty=si), [OpenInsider](http://openinsider.com/ACN)

### What Changed
- New wiki page created: header, Summary block (Rule #18), Business Overview, Pivotal Question, Key Stats, Sections 1–13.
- Live price verified **$168.82 (May 15, 2026 close, Yahoo Finance)** — stock down ~48% over trailing 12 months, sitting ~$13 above its 52-wk low of $155.82 (52-wk high $322.86).
- FY25 financials anchored: revenue $69.7B (+7%), GAAP EPS $12.15 / adjusted EPS $12.93 (+8%), FCF $10.9B, adjusted operating margin 15.6%.
- Q2 FY26 anchored: revenue $18.0B (+4% LC), record bookings $22.1B, GAAP EPS $2.93, raised FY26 FCF guide to $10.8–11.5B; FY26 revenue-growth guide 3–5% LC.

### Thesis Status
- **Overall**: Initiated — Strengthened on valuation, Unchanged on business quality. Wide-moat, capital-light compounder trading at a multi-year-trough multiple (~12x fwd EPS, ~10% FCF yield) on AI-disruption fear that the data does not yet corroborate.
- **BAIT delta**: Behavioral STRONG · Analytical MODERATE · Informational MODERATE · Technical MODERATE (3–4 lens overlap).
- **Price target delta**: Bull $300 | Base $235 | Bear $135 (5-yr terminal). PW EV ≈ **$233**.
- **Catalyst & Sentiment delta**: Analyst consensus Buy (15 buy / 5 hold / 1 sell of 21), median target $260; targets cut hard post-Q2 (BMO $300→$230, RBC $295→$253) but still ~50%+ above spot. Short interest low (3.5% float). Insider activity routine, net small sells.

### Recommendation
- **For a non-holder**: Initiate — accumulate ≤ ~$185; valuation discounts a structural-impairment scenario that bookings and share-gain data contradict.
- **For a current holder**: Add — below PW EV with double-digit FCF yield; thesis intact.

**Next review trigger**: Q3 FY2026 earnings (expected ~June 2026), or any management commentary materially changing AI-cannibalization framing.
