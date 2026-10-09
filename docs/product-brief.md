# Odysseus — product brief

**Living discovery doc (human-edited):** [Daredd <> Lindsay Discovery](https://docs.google.com/document/d/196vcmVRh4cuq7mzBBl3fAhKtnC2DhQbzFQBmiV8ozps/edit?tab=t.0)

This file is the **repo snapshot** for agents. When discovery moves in the Google Doc, update this file (or ask an agent to sync) so git-backed context stays aligned.

---

## Vision

Build a **little buddy**: a sovereign AI companion with **well-structured, continuously improving local context**.

### Sovereignty (non-negotiable)

Data must **not** be extracted and used to manipulate the customer or source. Most buyers do not yet understand what “sovereign” means in practice.

**Sovereign means:** the model runs on infrastructure the customer controls and can inspect — e.g. a **64GB machine on their desk** or a **local data center** they can audit. Not opaque cloud inference that phones home.

### Context (product core)

The buddy depends on **local context** that is:

- Structured (not a junk drawer of notes)
- Maintained over time
- Improved as real work gets done

The product is oriented toward a **context engine** or **agent** that makes that loop reliable — not a generic chat wrapper.

---

## What we are building (working loops)

Three intertwined goals — the repeatable **process** is still being discovered; this is the initial frame:

| Loop | Intent |
|------|--------|
| **1. Figure out the repeatable process** | Define how a customer (or niche) onboarded, runs, and gets value from the buddy — same steps every time, measurable. |
| **2. Build the context to enable that** | Produce and organize the local knowledge structure (sources, schemas, update rules) that makes the process work. |
| **3. Continuous review and improvement** | As the process runs, refine both the **context** and the **process** — explicit review cadence, not one-off setup. |

Agents and humans should treat improvements to `docs/` and customer context packs as **first-class deliverables**, not afterthoughts.

---

## Business goal

**Target:** Context engine or agent product worth **£20 million** by **December 2028**.

### Initial product guidelines (ICP filter)

Prospects should meet **all** of:

1. **< 1,000** prospects in a **definable market**, with contact details reachable in some form
2. Can **comfortably afford > $100/month**
3. **Know they have a problem** and will recognise the solution as valuable
4. **Lateral scalability** to connected niches (adjacent markets without reinventing the product)

---

## Initial market (chosen)

**Angel investor deal processing** — first niche to design the repeatable process and context pack against.

### Why this fits the ICP filter (working hypothesis)

| Guideline | Fit |
|-----------|-----|
| **< 1,000 definable prospects** | Angel groups, syndicates, micro-VCs, and active angel investors in a geography or network are listable; total addressable count in a focused segment should stay under 1,000 for a first wedge. |
| **> $100/month affordable** | Deal volume and sensitivity to mistakes (missed terms, compliance gaps, slow diligence) support tooling budget well above $100/month when value is proven. |
| **Known problem, clear value** | Deal flow is document-heavy, repetitive, and bottlenecked on partners’ time; mistakes are costly. Sovereign/local context matters: cap tables, decks, and correspondence must not leak to extractive cloud training. |
| **Lateral niches** | Adjacent without reinventing the product: seed funds, family offices, SPV/syndicate leads, angel networks, later-stage associate workflows on smaller rounds. |

### Problem themes (to validate in discovery)

- **Knowledge bottlenecks:** Term sheets, SAFEs/notes, side letters, data room clutter, founder updates scattered across email and drives
- **Compliance / risk:** Investor eligibility, jurisdictional rules, conflict checks, audit trail for decisions
- **Urgency:** Competitive rounds (speed), missed follow-ups, key-person dependency when one partner “holds the context”

### Deal-processing scope (draft — not final process)

Use this as a map for interviews and context design; steps and owners TBD:

1. **Inbound** — deck, intro, warm referral; triage against thesis
2. **Screen** — fit, stage, sector, check size; pass or pursue
3. **Diligence** — questions, references, financial/legal review; structured notes
4. **Decision** — IC/member vote, allocation, conflicts documented
5. **Close** — docs, signatures, wire; cap table / instrument recorded
6. **Post-close** — portfolio tracking, updates, follow-on rights

The **repeatable process** and **context schema** for Odysseus v1 should be nailed for this chain only — not generic “finance AI.”

### Other markets (deferred)

Broader business verticals (legal, healthcare, logistics, etc.) and **personal markets** remain in the [Google Doc](https://docs.google.com/document/d/196vcmVRh4cuq7mzBBl3fAhKtnC2DhQbzFQBmiV8ozps/edit?tab=t.0) for later wedges — not the initial build target.

---

## Tools under consideration

Evaluate for context capture, structure, and diagrams — not committed stack:

| Tool | Typical role |
|------|----------------|
| **Notion** | Structured docs, light workflows |
| **Obsidian** | Local-first notes, linking, vault as context source |
| **Paperclip** | TBD — confirm product/version in discovery |
| **Mermaid** | Diagrams in markdown (architecture, flows) |
| **Hermes** | TBD — confirm product/version in discovery |

See [TOOLS.md](../TOOLS.md) for CLI and AWS tooling used in this workspace.

---

## Technical direction (prototype)

- **Sovereign runtime:** local or customer-controlled inference (e.g. 64GB-class workstation); avoid dependency on extractive cloud models for core buddy loop.
- **Prototype cloud:** AWS personal account `203712223134`, profile `odysseus`, region `eu-west-1` — for experiments only; not the sovereignty story for end customers.
- **Application repos:** not created yet — add to [REPOS.md](../REPOS.md) when the first codebase lands.

---

## For agents

1. Read [CONTEXT.md](../CONTEXT.md) first, then this file for product intent.
2. Prefer updates that strengthen **process + context + review** — not feature churn without a loop.
3. Do not contradict **sovereignty** (no “just use OpenAI API” as the default architecture without explicit user approval).
4. Sync material changes back from the [Google Doc](https://docs.google.com/document/d/196vcmVRh4cuq7mzBBl3fAhKtnC2DhQbzFQBmiV8ozps/edit?tab=t.0) when the user shares updates.
