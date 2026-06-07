# CLAUDE.md — kg-invest-wiki Schema (v3.0)

Operating manual for the LLM agent that maintains this wiki. **Read this file at the start of every session before touching `wiki/` or `raw/`.**

---

## 1. What This Wiki Is

A personal, position-agnostic investment knowledge base maintained by an LLM agent. Knowledge compounds from primary sources so every session starts from the latest synthesis, not a blank page.

- **Owner**: Karthik G
- **Started**: April 2026
- **Schema**: v3.0 (May 2026) — Karpathy LLM Wiki pattern, adapted

**The wiki page is the deliverable.** `wiki/tickers/[TICKER]/[TICKER].md` is the single, crisp, non-repetitive analysis surface — kept up to date in place. There is no separate "polished report" and no standalone weekly-summary document; those were retired in v3.0. The page itself, plus its `changelog.md`, is the product.

### What this wiki is *not*
- **Not a portfolio tracker** — it never records what the owner holds.
- **No position sizing** — no tranche %, position %, target allocation, or stock/options split, anywhere. Price-level *valuation* ranges ("attractive below $140") are allowed; those are valuation, not sizing.
- **No invented numbers** — every figure traces to a primary source or is tagged `[Estimate]` / `[Analyst consensus]` / `[Management guidance]`.

---

## 2. Directory Layout

```
rwh/  (kg-invest-wiki)
├── CLAUDE.md                     ← This file. Read before every session.
├── README.md                     ← Top-level ticker table (Rule #13).
├── raw/                          ← Immutable source material. NEVER modify.
│   ├── [TICKER]/
│   │   ├── filings/              ← 10-K, 10-Q, 8-K, DEF 14A
│   │   ├── transcripts/          ← Earnings call transcripts
│   │   ├── press-releases/       ← Earnings + corporate announcements
│   │   ├── shareholder-letters/  ← CEO/founder letters
│   │   ├── investor-day/         ← Slide decks, conference presentations
│   │   └── analyst-reports/      ← User-uploaded PDFs
│   └── clippings/                ← General articles, not ticker-specific
└── wiki/                         ← LLM-owned. Write and maintain everything here.
    ├── index.md                  ← Master ticker catalog.
    ├── watchlist.md              ← Cross-ticker attractiveness ranking (no allocation).
    ├── tickers/[TICKER]/
    │   ├── [TICKER].md            ← Single consolidated wiki page (the deliverable).
    │   └── changelog.md           ← Append-only per-ticker event log.
    └── frameworks/               ← bait.md, moneyball.md, asset-types.md, outsiders.md
```

---

## 3. Core Rules

1. **Never modify `raw/`.** Immutable source of truth. Add, never edit.
2. **Position-agnostic.** Analyze the *company*, not the owner's holdings. Recommendations split into non-holder / holder framings when they diverge.
3. **No portfolio sizing.** No tranche %, position %, target allocation, or stock/options split. Price-level *valuation* ranges are allowed.
4. **Always update `index.md`** when adding or substantially changing a wiki page.
5. **Per-ticker `changelog.md` is the only event log.** Material actions live in the relevant ticker's `changelog.md`; cross-ticker / schema-only events live in commit messages.
6. **Source from primary sources**: SEC filings, earnings releases, official IR pages, transcripts, conference materials. Non-primary content must be tagged `[Estimate]`, `[Analyst consensus]`, `[Management guidance]`, or `[Source: <name>, <date>]`.
7. **Live price always verified first.** Fetch from `https://finance.yahoo.com/quote/[TICKER]` before any valuation work. Fall back to CNBC / Google Finance / MarketWatch via web search. Never trust a search-snippet price; click through.
8. **`changelog.md` is the action layer.** Every wiki update writes a changelog entry stating Thesis Status (Strengthened / Weakened / Unchanged) and an action verb (Initiate / Add / Reduce / Exit / Hold / Avoid).
9. **Quiet weeks still log.** A weekly run with no material events writes a `[YYYY-MM-DD] — No Material Events` changelog entry with a price / short-interest / analyst-consensus snapshot. This becomes the next week's baseline. (Active tickers only — paused tickers write nothing.)
10. **Always push to `origin` after every commit.** The remote is the source of truth. Surface push failures to the user and to the affected ticker's `changelog.md` if mid-Workflow-B.
11. **One wiki page per ticker.** Per-ticker folder contains exactly two files: `[TICKER].md` (header + Summary + Business Overview + Pivotal Question + Key Stats + Sections 1–13) and `changelog.md`. Never create separate `overview.md` / `thesis.md` / `financials.md`; fold any legacy files into `[TICKER].md` on first re-ingest.
12. **Date discipline.** Before writing any date-stamped artifact (Last Updated, changelog title, weekly snapshot), run `date -u +%Y-%m-%d` via Bash and use the literal result. NEVER infer the date from session memory, prior file contents, or context. When delegating to sub-agents, pass the verified date as a literal string.
13. **Maintain the README ticker table.** The `## Tickers Covered` table in `README.md` is the top-level entry point.
    - **Order**: alphabetical by ticker (A → Z, case-sensitive).
    - **Columns** (exactly 4): `Ticker | Status | Last Updated | Punchline`.
    - **Ticker**: relative link `[TICKER](wiki/tickers/TICKER/TICKER.md)`.
    - **Status**: `Active` or `Paused` — mirror the ticker page header (Rule #14).
    - **Last Updated**: ISO date of the most recent *material* update (NOT row-touched date, NOT quiet weeks).
    - **Punchline**: 1–2 sentences synthesizing the latest thesis (typically §13 thesis sentence + action verbs). Update on material thesis change; preserve verbatim during quiet weeks.
    - **Add** a new ingest in alphabetical position; **remove** a delisted/divested/retired ticker (history preserved in git).
    - **Counter line** (`*N tickers above.*`) directly below the table must reflect the current count.
14. **Active / Paused ticker status.** Every ticker declares status in the `[TICKER].md` header on a `**Status**: Active` or `**Status**: Paused — since YYYY-MM-DD` line directly below `Last Updated`. README, `index.md`, and `watchlist.md` all read from this, and Workflow B skips Paused tickers. Pause/resume governed by Workflow C.
    - README and `index.md` include a `Status` column.
    - `watchlist.md` ranks Active only; Paused tickers move to a "Paused Tickers" footer with pause date.
    - Quiet week ≠ paused week. Active-quiet writes a `No Material Events` entry; Paused writes nothing.
15. **All sources must be linked** as real Markdown links — never bare text.
    - **Absolute URL** for public web (SEC EDGAR, IR pages, news). Link the *specific* document, not the publisher home page.
    - **Relative path** when the file is stored locally — e.g., `[Q4 2025 PR](../../../raw/[TICKER]/press-releases/2026-02-Q4-results.pdf)`. Prefer relative when the file exists locally.
    - **Format**: `[Human-readable label](URL-or-path)`. No bare `[Source: ...]` tags.
    - **Unresolvable**: append `[link pending]` so the next pass can fix it. Never silently drop a citation.
16. **Visual emphasis & emoji conventions.** Each glyph carries specific meaning — not decorative:

    | Emoji | Meaning |
    |---|---|
    | 🟢 | Bullish / Strengthened / Initiate / Add |
    | 🔴 | Bearish / Weakened / Exit / Avoid |
    | 🟡 | Neutral / Unchanged / Hold / Watch |
    | ⚠️ | Material risk / warning |
    | ✅ | De-risked / resolved / delivered (pair with `~~strikethrough~~`) |
    | 📅 | Date-anchored upcoming catalyst |
    | 💰 | Capital allocation / buyback / dividend |
    | 📈 / 📉 | Notable price or metric move (sparing) |
    | 🎯 | Price target / entry zone / trim zone |

    **Bold** punchlines and thesis-carrying numbers only. *Italics* for source attributions and meta-tags (*[Estimate]*). No HTML `<span style>` or hex colors — GitHub render gaps.
17. **Summary block.** `[TICKER].md` opens with a `## Summary` (after header, before Business Overview) in 4 fixed parts. Refresh whenever §13 or §9 changes.
    1. **Thesis + verbs** — 1–2-sentence thesis, then verb line: `🟢 Non-holder: <verb> · 🟡 Holder: <verb>`.
    2. **Scenario table** (8 cols): `52-wk range | Now (+%ile) | Bear | Entry | Base / PW EV | Trim | Bull | R/R`. Percentile = LLM judgment. R/R per Rule #24.
    3. **KPI strip** (6 cells): slot 1 BAIT, slot 2 Moat, slot 3 FY26E P/E (or asset-class primary multiple), slots 4–5 ticker-relevant (FCF yld / Div yld / Short int. / segment KPI / FX, etc.), slot 6 Next catalyst.
    4. **Why / Why not / Next read** — 3 🟢 Why bullets + 3 ⚠️ Why not bullets, **one bullet per line**, then a single `📅 Next read:` line.
18. **Annual shareholder letters are required primary sources.** Every Workflow A ingest fetches and synthesizes 5 fiscal years of CEO/founder letters; letters take precedence over third-party summaries.

    | Pattern | When | Fetch |
    |---|---|---|
    | A — Annual letter publishers | Standalone annual letter (typ. Feb–Mar) | 5 letters (1/year × 5 yrs) |
    | B — Quarterly letter publishers | Letter to Shareholders with each quarterly print | All 4 letters × 5 yrs (up to 20). Q4 = primary annual-wrap weight. Min first ingest: 5 Q4 + 3 most-recent quarterly = 8. |
    | C — No standalone letter | Chairman's Letter in annual report or DEF 14A introductory letter | 5 letters; if neither exists, log gap in §4 and rely on transcripts |

    Sub-5-year coverage: fetch all letters since IPO. Storage: `raw/[TICKER]/shareholder-letters/YYYY_letter.pdf` named by *fiscal year covered*. Synthesis surface: §4 `### Recent Management Commentary — Primary Source Synthesis` — verbatim quotes mapped to investment relevance, optional multi-year framework-arc table. A new letter is a Meaningful Event; refresh §4 + extend the arc on incremental.
19. **10-K MD&A and Risk Factors are required primary sources — last 5 fiscal years for first-run ingest.** The MD&A explains *why* numbers moved; multi-year Risk Factor evolution shows *how* management's worldview shifted. Required integration map:

    | 10-K Item | Wiki Section(s) | What to extract |
    |---|---|---|
    | Item 1 (Business) | §1, §2 | Founding insight, segment definitions |
    | Item 1A (Risk Factors) | **§6** | Verbatim language for the highest-impact 6–8; flag *new* risks vs. prior 10-K |
    | Item 7 (MD&A) | **§1, §3, §7** | Segment drivers, competitive dynamics, macro sensitivity |
    | Item 7A (Market Risk) | §6, §7 | Quantified rate/FX/commodity sensitivities |
    | Item 8 (Statements & Notes) | §1, §2, §8 | Segment data, contingencies, debt, share count |
    | Item 15 / DEF 14A | §4 | Exec comp, board, insider ownership |

    §1 carries a `### Primary Source: 10-K Segment Detail (FY[N])` subsection explaining drivers across the 5-year window. §6 risks each derive from Item 1A or MD&A; analyst-only risks tagged `*[Analyst speculation]*`. Workflow B diffs Item 1A year-over-year; newly added risks drive a §6 update with a `[NEW in FY[N] 10-K]` tag. Source preference: SEC EDGAR HTML > IR-site PDF > analyst summaries. PDF binary fetches fall back to EDGAR HTML.
20. **Synthesis over transcription.** Primary sources inform; agent synthesis is the deliverable. Verbatim quotes only when paraphrase would weaken the insight. Replace 5+ row chronological tables of source extracts with a 2–4 sentence synthesis paragraph naming what genuinely changed. The 5-Year Strategic Framework Arc table for letters in §4 is preserved; a parallel Risk Factor table is not — use prose.
21. **Wiki-page output discipline.** The page must stand alone without exposing schema mechanics:
    - **No CLAUDE.md self-references** in `[TICKER].md`. Light references in `changelog.md` are OK (audit trail).
    - **No retrospective / "corrected from" / "prior framing" language** in page bodies. Git history is the audit trail. Acceptable in changelog.
    - **Table orientation**: time in columns, metrics in rows. Exception: ≤4-row summary tables (Bull/Bear/Base) where one-row-per-scenario reads better.
    - **Bullet preference**: sentences with 3+ data points → bullets.
22. **Competitive moat & landscape live in §3 only.** No standalone "Moat Assessment" block before the Pivotal Question; the Summary keeps a one-line moat verdict, all detail in §3. §3 must include a `### Competitive Landscape` subsection: named direct competitors (US + international where relevant) with market share + a 1–2 sentence read on each peer's moat / threat vector; explicit framing of how *this company's* moat differs and the evidence; an honest tail-risk read. Apply judgment — structurally unique businesses get a one-line note, narrow peer sets a 2-row table, broad categories fuller tables. Cite every market-share figure (Statista, BuiltWith, peer 10-Ks, market-research reports).
23. **Risk Factor materiality filter.** §6 focuses on risks material to the *investment decision*, not every Item 1A line.
    - **Drop** universal boilerplate ("revenue could fluctuate," generic payments regulation, generic cyber-attacks, generic third-party reliance, generic key-personnel risk).
    - **Keep** risks meeting ≥1 criterion:
      - (a) **Materially differentiated from peers** — meaningfully more severe for this company.
      - (b) **Not yet priced into the multiple** — state *"not priced in"* in Notes when so.
      - (c) **Tied to a specific thesis-break trigger** — a quantified condition the page commits to monitor.
      - (d) **Tied to a specific large discretionary investment** with uncertain outcomes (multi-billion capex bet, in-flight integration).
    - Collapse the 5-Year Risk Factor Evolution to a 2–4 sentence synthesis paragraph (Rule #20).
24. **Valuation discipline — one 5-year forward lens.**
    - **Horizon**: §11 scenarios always use a 5-year terminal horizon. Bull/Base/Bear (+ optional Bull+) probabilities sum to 100%.
    - **PW EV is the sole buy/sell anchor.** §13 zones derive mechanically from §12 PW EV:
      - **Entry** = price ≤ PW EV − MoS (typ. 15–25% discount)
      - **Trim** = PW EV < price < Bull
      - **Exit / avoid** = price ≥ Bull
    - **R/R** = (Bull % upside) ÷ (Bear % downside) vs. current spot, anchored to the §11 scenario set. Cite **one** canonical figure consistently in Summary, §12, and watchlist. Multiple Bull tiers — state both (e.g., *"~10:1 Bull/Bear, ~15:1 with Bull+ tail"*).
    - **Watchlist 3-column collapse**: blend Bull + Bull+ via probability-weighted average so PW EV reconciles to the canonical §11 number.
    - Consensus analyst targets and 12–18-month re-rating math are *inputs* to scenario probabilities — never anchors for §13 zones.
25. **Outsiders capital-allocation lens (§4).** §4's capital-allocation block carries a one-line **Outsider grade** per `wiki/frameworks/outsiders.md` (Thorndike's five tests, anchored on countercyclical buyback discipline).
    - **Grade vocabulary**: `Outsider · Outsider-leaning · Reinvestor · Steward (not Outsider) · Anti-Outsider`. One sentence of evidence; woven into the existing capital-allocation block — no new subsection.
    - **Surface to the §0 Summary only on a material capital-allocation event** — buyback authorization/execution, dividend init/raise/cut, M&A announce/close, large debt issuance/paydown, or a capital-allocation insider-alignment signal. When such an event lands, refresh the §4 grade *and* add/update a one-line Outsider read in the Summary "Why / Why not". Absent such an event, the lens stays in §4 only.
    - The cross-ticker scoring table in `wiki/frameworks/outsiders.md` is the authoritative central record; keep the graded ticker's row in sync.
26. **Conciseness & anti-duplication — state once, reference don't restate.** The page is read top-to-bottom by a human; sections do **not** re-establish context. Every insight, metric, and source has exactly one canonical home and is *referenced* (not re-explained) elsewhere.
    - **Canonical homes**: live price / 52-wk / mkt cap → header (Key Stats may table it once more); multiples + price targets → §8; analyst / short-interest / insider / sentiment → §9; scenario targets + R/R → §11–§12; capital-return yield + Outsider grade → §4; pivotal question → Summary. Stating a fact in its home licenses a one-clause reference anywhere else — never a restatement.
    - **One R/R figure** (Rule #24). Never compute R/R three ways across §11/§12/Summary.
    - **No triple pivotal-question**: once in the Summary; Business Overview carries at most a single clause.
    - **Merge overlapping prose**: Business Overview + "why it exists" are one block; BAIT lenses are one line each; the §4 RMC arc and §6 risk-evolution are ≤2 sentences (Rule #20).
    - **Per-section soft word budgets** (prose only; tables and source lists don't count). A full page body targets **≤2,800 words**; a Workflow B incremental block targets **≤250 words**.

      | Block | Budget | Block | Budget |
      |---|---|---|---|
      | Summary | ≤300 | §6 Key Risks | ≤90 |
      | Business + Pivotal Q | ≤150 | §7 Macro | ≤150 |
      | Key Stats | table | §8 Valuation | ≤130 |
      | §1 Financials | ≤130 | §9 Catalyst/Sentiment | ≤190 |
      | §2 Revenue/Geo | ≤90 | §10 BAIT | ≤120 |
      | §3 Moat & Landscape | ≤220 | §11+§12 Scenarios+PW EV | ≤140 |
      | §4 Management | ≤220 | §13 Recommendation | ≤250 |
      | §5 Growth | ≤100 | | |

    - **Duplication audit (required closing step)** of every Workflow A and Workflow B write: scan the draft for any thesis-carrying phrase (a yield, a risk name, a growth stat) appearing >2× and collapse extras to a reference to the canonical home. Budgets are soft; the state-once discipline is hard.

---

## 4. Investment Framework — 13-Section Thesis Structure

The page header (Schema / Last Updated / Status / Live Price), Summary, Business Overview, Pivotal Investment Question, and Key Stats Snapshot precede Section 1.

| # | Section | Purpose |
|---|---------|---------|
| 1 | Annual Financial Metrics | 4–6 year trend + recent quarters; primary 10-K segment detail |
| 2 | Revenue Mix & Geographic Split | Revenue streams + business model + region table + forward shifts |
| 3 | Competitive Moat & Landscape | Wide / Narrow / None + sources + vulnerabilities + named competitors with market share + how-this-company-differs |
| 4 | Management & Leadership | CEO/CFO assessment + capital-allocation track record + Outsider grade + RMC subsection (Rule #18) |
| 5 | Strategic Growth Initiatives | Growth vectors that justify forward multiples |
| 6 | Key Risks | Materiality-filtered Impact × Probability × Priced-In table (Rule #23) |
| 7 | Industry-Specific Macro Analysis | TAM, structural dynamics, regulatory environment |
| 8 | Valuation & Comparable Analysis | Multiples, peer set, "fair price" range |
| 9 | **Catalyst & Sentiment Tracker** | Analyst ratings, short interest, options skew, insider activity, news, upcoming events |
| 10 | BAIT Framework | Behavioral / Analytical / Informational / Technical lenses |
| 11 | Bull / Bear / Base Cases | 5-year terminal scenario price targets (Rule #24) |
| 12 | Probability-Weighted Expected Value | PW EV vs. current price (Rule #24) |
| 13 | **Recommendation & Bottom Line** | Action verb + price-level rationale + thesis-break triggers + next review trigger |

### Section 9 — Catalyst & Sentiment Tracker (detail)

Drives weekly incrementals. Standardized fields:

- **Live price + 52-wk range + % from high/low** (date-stamped)
- **Analyst consensus**: Buy/Hold/Sell counts, median target, high/low; rating changes since last update with firm name + direction
- **Short interest**: % of float, days-to-cover, WoW + MoM delta. Flag sustained >10% MoM increase
- **Options skew (optional)**: 30-day put/call, IV percentile if material
- **Insider activity (last 90 days)**: net buy/sell, transactions >$1M or by officers/directors (OpenInsider + SEC Form 4)
- **Recent corporate news (last 90 days)**: `[YYYY-MM-DD] [Event Type] — [one-line] [linked source]`
- **Upcoming catalysts**: earnings, shareholder meeting, FDA/regulatory date, product launch, contract decision

### Section 13 — Recommendation & Bottom Line (template)

```
**Thesis in one sentence**: [Single sentence stating the central thesis]

**For a non-holder**: [Initiate / Watch / Avoid] — [price-level rationale]
**For a current holder**: [Add / Hold / Reduce / Exit] — [price-level rationale]

**Attractive entry zone**: [$X – $Y] (rationale)
**Trim zone**: [$X – $Y] (rationale)
**Exit / avoid zone**: [$X – $Y] (rationale)

**Thesis-break triggers** (would force re-rating):
- [Specific quantified trigger]
- ...

**Next review trigger**: [Specific event or date]
```

---

## 5. Frameworks (one-line index)

- **BAIT** (Mauboussin) — §10. Four lenses (Behavioral / Analytical / Informational / Technical), each rated Strong / Moderate / Weak. Triple+ overlap = highest conviction. Detail: `wiki/frameworks/bait.md`.
- **Moneyball** — §11/§12. 5-year terminal Bull/Base/Bear scenarios; PW EV per Rule #24. Detail: `wiki/frameworks/moneyball.md`.
- **Asset Type Rules** — per-asset-class key metrics + valuation primary (capital-light platform, three-sided marketplace, franchise royalty, financial/brokerage, pharma, managed care, mortgage, consumer staples, etc.). Detail: `wiki/frameworks/asset-types.md`.
- **Outsiders** (Thorndike) — §4 capital-allocation lens, five tests; one-line grade in §4, surfaced to §0 only on a material capital-allocation event (Rule #25). Detail: `wiki/frameworks/outsiders.md`.

---

## 6. Workflow A — First-Run Ingest

Triggered by "ingest [TICKER]" or "build wiki page for [TICKER]".

### Step 1 — Pre-flight
Read this `CLAUDE.md` and the `kg-investment-analysis` skill SKILL.md. If `wiki/tickers/[TICKER]/` already exists, switch to Workflow B.

### Step 2 — Fetch standard raw set
Create `raw/[TICKER]/` and populate (source preference SEC EDGAR > company IR > major aggregator; log gaps, never fabricate):
- **Last 5 annual 10-Ks** (or all since IPO) → `filings/[TICKER]-10K-FY[YYYY].pdf`
- **Last 4 quarterly transcripts** → `transcripts/`
- **Last 4 quarterly press releases** → `press-releases/`
- **All 8-Ks in last 12 months** + **most recent DEF 14A** → `filings/`
- **Last 5 annual shareholder letters** (Pattern A/B/C per Rule #18) → `shareholder-letters/YYYY_letter.pdf`
- **Latest investor day deck** if available → `investor-day/`
- **User-supplied PDFs** → `analyst-reports/`

### Step 3 — Verify live data
Live price (Yahoo; fallback CNBC/Google/MarketWatch), 52-wk range, market cap, EV, float, short interest, analyst consensus, last-90-days insider activity.

### Step 4 — Synthesize the 13 sections
Compile from the raw set, not media summaries. Cite a primary source for every material claim; tag estimates. Apply Rules #18 (letters → §4 RMC), #19 (10-K integration map), #20 (synthesis), #21 (output discipline), #22 (moat + landscape), #23 (risk filter), #24 (valuation).

### Step 5 — Write the wiki page (the deliverable)
- `wiki/tickers/[TICKER]/[TICKER].md`:
  1. Header block (ticker, company name, schema version, Last Updated, `**Status**: Active`)
  2. Summary (Rule #17, emoji per Rule #16)
  3. Business Overview (1–2 paragraphs)
  4. Pivotal Investment Question
  5. Key Stats Snapshot
  6. Sections 1–13 (financial tables embedded inline in §1, §2, §8, §12)
- `wiki/tickers/[TICKER]/changelog.md` — initial entry "v3 Initial Ingest".
- Delete legacy `overview.md` / `thesis.md` / `financials.md` if present.
- **Close with the duplication audit** (Rule #26): any thesis-carrying phrase appearing >2× collapses to its canonical home. Hold the page to the per-section word budgets (≤2,800 words total).

### Step 6 — Update cross-cutting files
- `wiki/index.md` — add row, refresh ticker summary + last-updated date.
- `wiki/watchlist.md` — add row in attractiveness ranking.
- `README.md` — insert row in alphabetical position (Rule #13).

### Step 7 — Commit and push
- `git commit -m "INGEST [TICKER]: v3 initial ingest — [headline]"`
- `git push origin <branch>` (Rule #10).

---

## 7. Workflow B — Weekly Incremental

Triggered by "weekly update" / "update [TICKER]".

### Step 1 — Determine baseline and active set
For each ticker in `wiki/tickers/`:
- Read `**Status**:` from the header. **If `Paused`, skip entirely** (no fetch, no scan, no entry, no page change).
- If `Active`: baseline = date of the latest `changelog.md` entry. Lookback window = everything since.

### Step 2 — Scan for meaningful events
Check the **Meaningful Events List** (§10) across: company IR, SEC EDGAR, earnings calendar, analyst rating changes, short-interest aggregator, insider Form 4 aggregator, news search.

### Step 3a — If material events exist
1. Fetch new raw material into `raw/[TICKER]/<subfolder>/`.
2. Walk the section-refresh checklist:

   | Section | Refresh on earnings/material event |
   |---------|------------------------------------|
   | 1 — Annual Financial Metrics | **Always** on earnings: add new quarter row + roll TTM |
   | 2 — Revenue Mix & Geo | If segment / geo data disclosed |
   | 3 — Moat & Landscape | On competitor moves, market-share shifts, moat-altering events only |
   | 4 — Management | On management commentary, capital-allocation changes; refresh RMC if a new letter dropped |
   | 5 — Strategic Growth | On new strategic disclosures |
   | **6 — Key Risks** | **Always scan**: resolved risks marked `~~struck~~ DE-RISKED [date]`; new risks added with Rule #23 filter |
   | 7 — Macro | On regulatory / sector developments |
   | 8 — Valuation | **Always** on earnings: re-compute multiples; refresh Assessment |
   | **9 — Catalyst & Sentiment** | **Always**: refresh price, consensus, actions, insiders, news, upcoming. Move delivered → "Delivered ✅" |
   | 10 — BAIT | Refresh any B/A/I/T justification the new data alters |
   | 11 — Scenarios | Refresh assumptions/targets/probabilities if scenarios shifted |
   | 12 — PW EV | Recompute if §11 changed; verify R/R (Rule #24) |
   | **13 — Recommendation** | **Always review**: thesis sentence, verbs, zones, thesis-break triggers |

   §2/§3 rarely move on a single earnings event — refresh only on true strategic pivots, business-model changes, or moat-altering events.
3. Append a `changelog.md` entry; the `What Changed` block mirrors the refreshed sections (one bullet each). Close with the duplication audit (Rule #26; ≤250-word incremental block).
4. Update `wiki/index.md` last-updated + moved fields.
5. Update `wiki/watchlist.md` ranking + earnings calendar + price targets if attractiveness changed.
6. Update `README.md` Last Updated + Punchline (Rule #13).
7. Refresh the §0 Summary if §13 or §9 changed (Rule #17).

### Step 3b — If no material events (quiet week)
Write a `[YYYY-MM-DD] — No Material Events` changelog entry with a snapshot (price, 52-wk %, short interest %, consensus median, news headlines reviewed and dismissed). Do NOT modify `[TICKER].md` or bump README dates.

### Step 4 — Commit and push
- `git commit -m "WEEKLY YYYY-MM-DD: [N] events / [M] quiet — [headline]"`
- `git push origin <branch>`.

---

## 8. Workflow C — Pause / Resume

Triggered by `pause [TICKER][: <reason>]` or `resume [TICKER]`.

### C.1 — Pause
1. Verify the ticker exists and is `Active` (no-op + surface if already Paused).
2. Run `date -u +%Y-%m-%d`.
3. In `[TICKER].md` header, set `**Status**: Paused — since YYYY-MM-DD`.
4. Append a changelog entry stating reason + last active baseline.
5. Update `README.md` (Status → Paused; do NOT bump Last Updated/Punchline), `wiki/index.md` (Status → Paused), `wiki/watchlist.md` (remove from ranking; append to "Paused Tickers" footer).
6. Commit `PAUSE [TICKER]: <reason>` and push.

### C.2 — Resume (with catch-up incremental)
A multi-quarter pause may span multiple earnings, analyst clusters, and macro events — catch-up reconstructs all of them, not just price-stamping.

1. Verify status is `Paused`. Run `date -u +%Y-%m-%d`. Read the pause-since date.
2. **Catch-up scan** over the full pause window: all 10-Q/10-K filings, all earnings PRs + transcripts, all 8-Ks, full-window analyst rating changes (note clusters), current short interest + insider 90-day + analyst consensus, any management/M&A/regulatory/dividend/buyback events.
3. Apply the full Workflow B Step 3a section-refresh checklist. §9 must enumerate each earnings print in chronological order before settling on the current state.
4. Set header `**Status**: Active`, bump `Last Updated`.
5. Append a `## [YYYY-MM-DD] — Resumed (Catch-Up)` changelog entry: pause window, events reconstructed, sources reviewed, What Changed vs. pre-pause baseline, Thesis Status, Recommendation, Next review trigger.
6. Update `README.md` (Status → Active, bump Last Updated, refresh Punchline), `wiki/index.md`, `wiki/watchlist.md` (re-insert into ranking, remove from Paused footer).
7. Commit `RESUME [TICKER]: catch-up over [N]-day window — <headline>` and push.

---

## 9. Parallelization Patterns

- **Workflow A fetch phase**: optional fan-out — 3–4 parallel fetcher agents split by source type (filings / transcripts+PRs / shareholder letters / live + insider + analyst). A single synthesizer agent then reads the aggregated raw output.
- **Weekly update across N tickers**: parallelize trivially — one `Agent` call per Active ticker, dispatched as parallel tool uses in a single message.
- **Within a single ticker**: single agent. Section synthesis is dependency-dense (§12 PW EV depends on §11 depends on §6 + §10) — section-level parallelism creates merge headaches exceeding the time saved.
- **Shared file writes** (`README.md`, `wiki/index.md`, `wiki/watchlist.md`): sequential. Never parallel writes to the same file across agents.
- **Agent definition**: prefer a Sonnet research agent (WebSearch / WebFetch / Edit / Write) for fetch-heavy work; `general-purpose` for synthesis-heavy work.

---

## 10. Meaningful Events List

- **Earnings**: 10-Q / 10-K, earnings PR, transcript
- **Annual shareholder letter published** (Pattern A/B/C per Rule #18)
- **Shareholder meeting**: annual, special, proxy-vote outcomes
- **Strategic announcements**: product launch, market entry/exit, divestiture, restructuring
- **M&A**: acquisition announce/close, merger, JV, strategic partnership
- **Capital allocation**: buyback authorization/execution, dividend init/raise/cut/suspend, debt issuance, equity issuance
- **Analyst rating changes**: any upgrade/downgrade/initiation/target revision; a cluster ≥3 firms in a week gets special attention
- **Short interest delta**: >10% MoM, or a sustained 3-week trend either direction
- **Insider activity**: any Form 4 >$1M, any cluster, any unusual pattern (CEO/CFO sell into decline)
- **Major regulatory action**: investigation, fine, ruling, new rule affecting the business model, antitrust
- **Litigation**: material suit filed, settlement, judgment
- **Management changes**: CEO/CFO/COO appoint or depart, board changes
- **Credit / rating agency actions**: S&P / Moody's / Fitch rating change

Extensible — add new event types when encountered.

---

## 11. Data Source Priority

| Data Type | Primary | Fallback |
|-----------|---------|----------|
| Live price | Yahoo Finance (`finance.yahoo.com/quote/[TICKER]`) | CNBC, Google Finance, MarketWatch (web search) |
| Filings | SEC EDGAR | Company IR site |
| Transcripts | Company IR, Motley Fool, Seeking Alpha | Yahoo Finance transcripts |
| Press releases | Company IR, BusinessWire, PRNewswire | Web search |
| Analyst ratings | Primary research-firm releases | User-uploaded PDFs in `analyst-reports/`, TipRanks / Yahoo |
| Short interest | Aggregator (Fintel, ChartExchange, NASDAQ) | FINRA twice-monthly |
| Insider activity | OpenInsider | SEC EDGAR Form 4 direct |
| Options data | CBOE, Yahoo options chain | Barchart |

---

## 12. changelog.md Format (Per Ticker)

Append-only. Most recent entry first.

### Standard event entry

```markdown
## [YYYY-MM-DD] — [Event Type: Earnings Q[X] / Strategic Announcement / Analyst Action / Insider Cluster / etc.]

**Trigger**: [What caused this update; cite primary source filename or URL]
**Sources reviewed**: [List of files in raw/ or URLs]

### What Changed
- [Specific metric or thesis element — direction and magnitude]

### Thesis Status
- **Overall**: Strengthened / Weakened / Unchanged
- **BAIT delta**: ...
- **Price target delta**: Bull $X → $Y | Base $X → $Y | Bear $X → $Y
- **Catalyst & Sentiment delta**: ...

### Recommendation
- **For a non-holder**: Initiate / Watch / Avoid — [rationale]
- **For a current holder**: Add / Hold / Reduce / Exit — [rationale]

**Next review trigger**: [event or date]
```

### Quiet-week entry

```markdown
## [YYYY-MM-DD] — No Material Events

**Lookback window**: [Prior baseline] → [today]
**Sources scanned**: IR, SEC EDGAR, analyst feed, short-interest, insider feed, news

### Snapshot
- Price: $X (Δ: ±Y%)
- 52-wk range: $L – $H (% from high: ±Z%)
- Short interest: A% of float (Δ MoM: ±B%)
- Analyst consensus: median $C (Δ: ±D%)
- News scanned and dismissed: [bullet list]

**Recommendation**: Unchanged.
**Next review trigger**: Next weekly review (default), or [specific catalyst].
```

---

## 13. index.md Maintenance

Required columns: `Ticker | Status | Company | Moat | Conviction | Last Updated | Summary`. Status is `Active`/`Paused` per Rule #14. Ticker links to `tickers/[TICKER]/[TICKER].md`.

Plus a Ticker Summary table: `Ticker | Price | vs. 52-wk High | FCF Yield | P/E Fwd | BAIT | Recommendation (non-holder / holder)`.

Plus a Pending Data Gaps table.

Update on every ingest or substantive change. Refresh the "last full index refresh" date at the bottom.

---

## 14. watchlist.md Maintenance

Pure attractiveness ranking. **No portfolio allocation, no target %, no stock/options splits.** Active tickers only — paused move to a "Paused Tickers" footer (Rule #14).

Required columns: `Rank | Ticker | Conviction | BAIT Overlap | Asymmetry (PW EV vs. price) | Recommendation (non-holder / holder) | Next Catalyst`.

Plus the Price Targets Summary (Probability-Weighted) table, the Earnings Calendar & Key Watch Events, and Cross-Portfolio Macro Watch Items.

---

## 15. Schema Co-Evolution

This file evolves — but it does **not grow by default**. Rule #26's state-once discipline applies to CLAUDE.md itself: every change is *integrated into its existing home*, not appended as new sediment. When a new framework is added, a ticker type encountered, or an operation needed:

1. **Find the home first.** Locate where the concept already lives — a Core Rule, a Workflow step, a framework one-liner. Default to editing that home in place. Create a *new* rule or section only when no home exists. One concept, one home.
2. **Replace, don't append.** A change that adds lines must delete the lines it supersedes in the same commit — obsoleted clauses, dead references, stale migration scaffolding. Net line growth is a smell to justify, not a default.
3. **Keep rationale out of the body.** The *what* lives in this file; the *why* and the migration history live in the `SCHEMA: vX.Y` commit message — never as "prior framing" / "changed from" residue in the text (mirrors Rule #21).
4. **Apply to wiki content** on the next material touch (migration discipline below).
5. **Consolidate periodically.** When rules sprawl, duplicate, or contradict, do a ground-up condense and bump the *major* version rather than patching (v3.0 collapsed 28 rules → 26, retired `outputs/`).
6. **Commit `SCHEMA: vX.Y — [what changed and why]` and push to `origin`** — the commit message is the audit trail (Rule #5).

**Migration discipline**: new schema conventions apply on the next material update to a page (Workflow B / re-ingest); never backfill untouched pages — git history is the record.

Browse evolution: `git log --oneline CLAUDE.md` · `git log -p CLAUDE.md` · `git blame CLAUDE.md`. Each `SCHEMA: vX.Y — ...` commit body captures the *rationale*, not just the *what*. v3.0 (May 2026) is current.
