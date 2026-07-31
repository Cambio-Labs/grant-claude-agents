# Grant Assistant Skills — Design Spec
**Date:** 2026-07-31
**Project:** Cambio Labs Grant Assistant
**Format:** Claude Code skills (.md plugin files)

---

## Overview

A 6-skill Claude Code plugin that gives grant writers a single command (`/grant-draft`) backed by a persistent knowledge base (`/grant-library`). Four internal agent skills handle RFP analysis, impact validation, style, and QA. Every factual claim in the output is cited to a real source document. Hallucination is structurally prevented — agents output gap notices instead of fabricating missing evidence.

---

## File Structure

```
.claude/plugins/grant-assistant/
├── skills/
│   ├── grant-draft.md        → /grant-draft   (Chaski — user entry point)
│   ├── grant-library.md      → /grant-library  (Librarian — knowledge base)
│   ├── grant-miguel.md       → internal        (RFP analysis)
│   ├── grant-maria.md        → internal        (impact validation + writing)
│   ├── grant-voice.md        → internal        (style application)
│   └── grant-mauricio.md     → internal        (QA & assembly)
└── knowledge-base/
    ├── INDEX.md              → master index of all indexed documents
    ├── org-profile.md        → Cambio Labs core facts, mission, programs
    └── docs/                 → one .md summary per uploaded document
```

Only `/grant-draft` and `/grant-library` are user-facing. The four agent skills are invoked internally by Chaski via the Skill tool.

---

## Skill 1: `/grant-library` — The Librarian

### Purpose
Persistent knowledge base management. Grant writers run this once per document; all agents draw from it across sessions.

### Commands

| Command | Action |
|---|---|
| `/grant-library add [filepath or paste]` | Process document, extract facts, write summary to `knowledge-base/docs/`, update `INDEX.md` |
| `/grant-library list` | Show all indexed documents with category, date, confidence |
| `/grant-library status` | Show available data vs. common grant gaps |

### Extraction Schema (per document)
When adding a document, the Librarian extracts:
- Document type (annual-report / financial / program-data / proposal / research)
- Date range covered
- Key metrics: participant counts, outcomes, budgets, demographics
- Programs mentioned with descriptions
- Named partners and funders
- Geographic focus
- Quotes suitable for narrative use
- Data caveats or limitations noted in the document itself

### Document Summary Format
Stored in `knowledge-base/docs/<filename>.md`:

```markdown
---
source: 2024_Annual_Report.pdf
type: annual-report
date-range: 2024-01-01 to 2024-12-31
confidence: HIGH
indexed: 2026-07-31
---
## Key Metrics
- Participants served: 347 youth [p.12]
- Program completion rate: 82% [p.14]
- Budget: $2.3M [p.18]

## Programs
- Journey Platform: digital skills, 200 participants [p.8]
- Startup NYCHA: entrepreneur accelerator, NYCHA residents [p.10]

## Notable Quotes
- "Our community-led model..." [p.3]

## Data Caveats
- Demographic breakdown by race/ethnicity not included in this report
```

### INDEX.md Format
```markdown
# Knowledge Base Index
Last updated: 2026-07-31

| File | Type | Date Range | Confidence | Key Metrics |
|---|---|---|---|---|
| 2024_Annual_Report.pdf | annual-report | 2024 | HIGH | 347 participants, 82% completion |
```

### Anti-Hallucination Rule
Librarian may only write facts it can directly quote or reference from the source. If a number appears without context, it writes `[unverified — confirm source]` instead of inferring.

### Future Knowledge Graph Path
Each document summary uses consistent entity tags (programs, beneficiaries, funders, metrics). These tags are the foundation for explicit relationship mapping in a future graph layer.

---

## Skill 2: `/grant-draft` — Chaski (Orchestrator)

### Purpose
Single user entry point. Reads the RFP, checks the knowledge base, sequences all agents, and produces a complete cited draft.

### Invocation
Grant writer runs `/grant-draft` with the RFP pasted into the chat or as a file path argument.

### Orchestration Sequence

```
1. Load knowledge-base/INDEX.md → inventory available documents
2. Invoke grant-miguel → extract RFP requirements + data needs list
3. Cross-check data needs against INDEX.md → identify coverage and gaps
4. [HUMAN GATE] Present gap summary to grant writer → ask to proceed or pause
5. Invoke grant-maria → validate evidence, draft sections with citations
6. Invoke grant-voice → apply Cambio Labs organizational voice
7. Invoke grant-mauricio → compile, compliance-check, score, generate checklist
8. Output final proposal + Quality Report + Submission Checklist
```

### Human-in-the-Loop Gate (Step 4)
After gap identification, Chaski pauses and shows:

```
KNOWLEDGE BASE COVERAGE REPORT

Available data (can draft now):
✓ Participant count 2024: 347 [HIGH confidence]
✓ Completion rate: 82% [HIGH confidence]
✓ 501(c)(3) status: confirmed [HIGH confidence]

Gaps (cannot draft without fabricating):
⚠ Race/ethnicity breakdown 2024 — MISSING
⚠ Letters of support (3 required) — MISSING
✗ Longitudinal outcomes data — NOT IN KNOWLEDGE BASE

Continue drafting with available data and gap notices?
Or stop here to gather missing documents first?
```

### Chaski's Constraints
- If `knowledge-base/INDEX.md` does not exist or is empty: halt, direct writer to `/grant-library add` first
- Never skip the gap check before drafting
- Never pass unverified claims to grant-voice
- If a required RFP section has INSUFFICIENT evidence: always include a gap notice, never fill with an estimate

---

## Skill 3: `grant-miguel` — RFP Analysis (Internal)

### Purpose
Structured extraction of all requirements, constraints, and data needs from the RFP. Produces no prose — only structured output for Chaski and Maria.

### Input
RFP document (text or file)

### Output Structure
```
REQUIREMENTS:
  Mandatory: [list with source section]
  Preferred: [list]
  Optional: [list]

FORMAT CONSTRAINTS:
  Page limit: X
  Font: X
  Submission method: X
  Deadline: YYYY-MM-DD HH:MM timezone

EVALUATION CRITERIA:
  [criterion]: [weight %]

DATA NEEDS (prioritized):
  HIGH: [what's needed, which RFP section requires it]
  MEDIUM: [...]
  LOW: [...]
```

### Constraint
Miguel does not consult the knowledge base. He reads only the RFP. Gap assessment happens in Chaski's cross-check.

---

## Skill 4: `grant-maria` — Impact Validation & Writing (Internal)

### Purpose
Match RFP requirements against knowledge base evidence. Write proposal sections with inline citations. Output gap notices for anything unsupported.

### Input
- Miguel's requirements + data needs list
- `knowledge-base/docs/` (reads directly)

### Confidence Scoring
| Level | Criteria | Action |
|---|---|---|
| HIGH (0.85–1.0) | Direct quote, audited document, within 2 years | Include with citation |
| MEDIUM (0.60–0.84) | Derived from reliable source, >2 years old, or self-reported | Include with caveat in citation |
| LOW (0.30–0.59) | Estimated or anecdotal | Flag for review before including |
| INSUFFICIENT (<0.30) | No source in knowledge base | Output Evidence Gap Notice — never draft prose |

### Evidence Gap Notice Format
```
⚠ EVIDENCE GAP — [Requirement: X]
Available: [what exists at what confidence]
Missing: [what's needed]
Recommendation: [where to find it / how to gather it]
Impact on proposal: HIGH/MEDIUM/LOW
```

### Maria's Hard Rule
Maria cannot write a factual claim (number, statistic, outcome, quote) without a citation tag. If she cannot cite it, she writes a gap notice. No exceptions.

### Challenge Mode
Maria actively flags weak evidence before it reaches Voice Waxer:
- "Source says '95% completion rate' — not 'success rate'. Do not conflate."
- "Comparison to national average is from a 5-year-old source — flag as MEDIUM, not HIGH."
- "'Significant improvement' is undefined — require a metric or remove the claim."

---

## Skill 5: `grant-voice` — Voice Waxer (Internal)

### Purpose
Apply Cambio Labs' organizational voice consistently across all sections. Does not add facts, does not remove citations.

### Organizational Voice Profile
```yaml
formality: professional-warm
sentence_length: 15-20 words average, mixed simple and complex
active_voice: 80% preferred
vocabulary:
  avoid: jargon without explanation
  emotional_language: moderate, strategic
  power_words: [transform, empower, sustainable, community-driven, community-led]
tone:
  - collaborative, not hierarchical
  - evidence-based, not aspirational
  - humble but confident
  - inclusive language required (no othering language)
terminology:
  - "participants" not "clients" or "beneficiaries"
  - "community members" not "target population"
  - "South Bronx" not "underserved area"
```

### Constraints
- Does not add any new facts
- Does not remove or alter citation tags
- Flags sections where voice is inconsistent across the proposal
- Does not change evaluation criteria weighting language (keep Miguel's original framing)

---

## Skill 6: `grant-mauricio` — QA & Assembly (Internal)

### Purpose
Compile all sections into a final proposal. Run compliance checks. Score proposal quality. Generate submission checklist.

### Assembly Order
Follows structure required by RFP (Miguel's format constraints). Sections assembled in RFP-specified order, not drafting order.

### Compliance Checks
- Page/word count vs. RFP limits
- All mandatory sections present
- All mandatory attachments listed in checklist
- Citation coverage: flag any factual claim without a citation tag
- Deadline buffer: flag if < 72 hours remaining

### Quality Report Format
```
PROPOSAL QUALITY REPORT

OVERALL SCORE: X/10

Evidence Quality:     X/10  [citation coverage %, avg confidence, data recency]
Compliance:           X/10  [format met %, content requirements met %]
Alignment:            X/10  [funder criteria coverage]
Readability:          X/10  [sentence length, active voice %, jargon flags]
Completeness:         X/10  [sections complete, gaps documented]

CRITICAL ISSUES: [anything blocking submission]
WARNINGS: [items to address before submission]
RECOMMENDATIONS: [prioritized, with time estimates]
```

### Submission Checklist
Auto-generated from Miguel's RFP requirements. Format:
```
☐ Main proposal (PDF) — READY / PENDING
☐ [attachment name] — READY / PENDING
☐ [portal action] — READY / PENDING
DEADLINE: [date time timezone]
TIME REMAINING: [X days, X hours]
```

---

## Shared Citation Format (All Skills)

### Inline Citation Tag
```
[Source: <filename> | <page or section> | Confidence: HIGH/MEDIUM/LOW]
```

### Example in Proposal Text
```
In 2024, we served 347 young people [Source: 2024_Annual_Report.pdf | p.12 | Confidence: HIGH],
with 82% completing the full program [Source: 2024_Annual_Report.pdf | p.14 | Confidence: HIGH].
Follow-up surveys showed 78% reported improved academic performance
[Source: 2024_Participant_Survey.pdf | n=285, self-reported | Confidence: MEDIUM].
```

---

## Universal Anti-Hallucination Rules

Applied across all six agent skills:

1. Never write a factual claim without a citation tag referencing a real source
2. Never cite a document not present in `knowledge-base/` or explicitly provided in the session
3. When evidence is INSUFFICIENT: output a gap notice, not an estimate
4. Confidence levels are fixed — an agent cannot upgrade confidence without a stronger source
5. Quotes must be verbatim from source documents, not paraphrased as if quoted

---

## Out of Scope (This Spec)

- Automated PDF ingestion pipelines (future engineering work)
- Vector search / semantic retrieval (knowledge base is currently file-based .md summaries)
- Knowledge graph relationships (foundation is being built — explicit graph layer is future)
- Web-based portal submission integration
- Multi-user / team access controls
