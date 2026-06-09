# FreightIQ — Freight Rate Intelligence Agent

A personal learning project — building an agentic AI system 
that enables faster decision making with fragmented data in 
freight forwarding, using Pinecone Vector DB and Retrieval 
Augmented Generation (RAG).

Built with Claude Code | Python | Pinecone Vector DB | 
Anthropic Claude API | PyMuPDF

---

## What This Is

A two-agent Agentic AI System for freight rate intelligence. 
Logistics operations managers receive spot freight quotes in 
multiple PDF formats from different forwarders — with 
surcharges buried in footnotes and validity dates expiring. 
Comparing them manually takes 2-3 hours per shipment decision.

FreightIQ ingests those PDFs into a Pinecone Vector DB and 
answers plain English rate questions in seconds.

20-25% of ocean freight shipments are booked on spot quotes 
in 2026. For a mid-size importer doing 200 containers a year 
— that's 40-50 manual rate decisions annually.
*(Source: Freightos Ocean Freight Procurement Report, 2026)*

---

## Architecture

![FreightIQ Architecture](freightiq_architecture.png)

---

## The Two Agents

### Agent 1 — Quote Ingester

Perceives PDF freight quotes, decides on data structure and 
quality, acts by storing in Pinecone Vector DB, loops until 
all quotes are ingested or confirmed unrecoverable.

**3-round agentic loop:**
- Round 1: Extract text via PyMuPDF → Claude structures JSON 
  → validate → embed → upsert to Pinecone
- Round 2: Automatic retry on all failures with stricter prompt
- Round 3: Final validation — confirms vector count matches 
  expected ingestion count

**Self-corrections on Run 1:** 2 automatic fixes
- Pinecone metadata type constraint (floats → strings)
- LOCODE extraction from filename vs Claude extraction

---

### Agent 2 — Rate Advisor

Perceives plain English questions, decides on retrieval 
strategy, acts by returning recommendations, loops until 
answer is complete and confident.

**4-loop agentic architecture:**
- Loop 1: Input validation — confirms route and intent 
  before searching
- Loop 2: 3-attempt retrieval — progressively broader 
  search if no results found
- Loop 3: Quality enrichment — recalculates true all-in 
  cost, flags buried surcharges, flags expiry within 7 days
- Loop 4: Completeness check — Claude reviews its own 
  answer before returning it

---

## RAG Pipeline
Plain English question
↓
Pinecone semantic search — finds quotes by meaning,
not keywords
↓
Expired quotes filtered out automatically
↓
Retrieved quotes passed to Claude as context
↓
Claude reasons across quotes — not from memory
↓
Plain English answer with surcharge breakdown
and expiry warning

RAG = Retrieval Augmented Generation — the AI answers 
from your documents, not from its training data.

---

## Sample Output

**Q: "What is the cheapest all-in rate from Mumbai 
to Rotterdam this week?"**

A: Maersk at $2,150 all-in — cheapest, but expires 
in 4 days. Switch to MSC at $2,610 valid till July 15.

**Q: "What hidden surcharges should I watch out for 
on the Shanghai to Mumbai route?"**

A: Hapag-Lloyd quote has BAF + CAF + THC + PSS = 
$320 buried on top of $580 base rate — 55% hidden markup.

---

## Run 1 Results

| Metric | Result |
|---|---|
| PDFs ingested | 15/15 |
| Questions answered | 5/5 |
| Self-corrections | 2 |
| Hidden markup caught | 55% |
| Formats handled | 3 different PDF layouts |

---

## Simulated Data

15 freight quotes across 3 PDF formats covering:

**Routes:** Mumbai→Rotterdam · Mumbai→Hamburg · 
Shanghai→Mumbai · Mumbai→Dubai · Delhi→London

**Carriers:** Maersk · MSC · CMA CGM · Hapag-Lloyd

**Quote mix:**
- 5 quotes: all-in rate clearly stated
- 5 quotes: base rate + buried surcharges, total not stated
- 3 quotes: validity already expired
- 2 quotes: expiring within 7 days

All quotes labelled: *"Simulated data for prototype 
demonstration only. No affiliation with named carriers."*

---

## Tech Stack

🔹 Language: Python
🔹 Vector DB: Pinecone (llama-text-embed-v2 · 1024 dimensions)
🔹 AI Layer: Anthropic Claude API (claude-sonnet-4-5)
🔹 PDF Extraction: PyMuPDF
🔹 PDF Generation: fpdf2
🔹 Built with: Claude Code — plain English prompts only

---

## Production Note

In this prototype, the database schema is shared with the 
AI to generate queries. In production, companies would 
deploy this on AWS Bedrock or Azure AI — so nothing leaves 
the enterprise environment. The logic is the same. The 
infrastructure is what changes.

---

## Roadmap

- ✅ Run 1: Two-agent MVP — terminal only
- 🔄 Run 2: Eval framework — accuracy, hallucination rate
- 🔄 Run 3: Flask frontend — interactive demo with 
  pre-loaded sample quotes

---

## Built By

**Sarthak Shirsat**
Founder, Acharooz | Ex-Movam | Ex Tolaram Group | 
IIM Mumbai MBA
9.5 years across fleet management, last mile logistics, 
B2B logistics SaaS and D2C brand building.

[LinkedIn](https://www.linkedin.com/in/sarthak-shirsat)
[AI Logistics Control Tower](https://github.com/sarthakshirsatai-lab/ai-logistics-control-tower)

---

## Disclaimer

All freight quote data is simulated. Maersk, MSC, CMA CGM 
and Hapag-Lloyd are referenced for simulation realism only 
— no affiliation. Built with Claude Code.
