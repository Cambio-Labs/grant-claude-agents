# Grant Assistant — Cambio Labs

An AI grant-writing assistant that runs inside Claude Code. It drafts complete, evidence-backed grant proposals from RFPs — and never fabricates: every statistic links to a real source document, and anything unsupported appears as a clearly marked gap notice instead of invented text.

---

## How it works

Six AI agents collaborate in a fixed pipeline:

| Agent | Role |
|---|---|
| **grant-library** | Adds and indexes your org's documents into the knowledge base |
| **Miguel** | Reads the RFP and extracts every requirement, deadline, and data need |
| **Maria** | Writes each section using only evidence from your knowledge base |
| **Voice Waxer** | Applies Cambio Labs' voice and preferred terminology |
| **Mauricio** | Assembles the proposal, runs compliance checks, scores quality |
| **Chaski** | Orchestrates all the above from a single command |

You interact with two commands only: `/grant-library` and `/grant-draft`.

---

## Requirements

- [Claude Code](https://claude.ai/code) (the CLI or desktop app)
- This repository open as your working directory

---

## Setup (one time)

Open Claude Code in this directory:

```
cd /path/to/grant-claude-agents
```

The plugin is already installed at `.claude/plugins/grant-assistant/`. No additional configuration needed.

---

## Step 1 — Add your organization's documents

For each document you have (annual reports, program data, financials, past proposals, survey results):

```
/grant-library add [filepath]
```

**Example — add a file:**
```
/grant-library add ~/Documents/cambio-labs-annual-report-2024.pdf
```

**Example — paste content directly** (useful if documents are in a GPT or Google Doc):
```
/grant-library add
```
Then paste the document text when prompted.

**Check what's indexed:**
```
/grant-library list
```

**See coverage gaps before drafting:**
```
/grant-library status
```

This shows which grant data types you have covered vs. missing (participant counts, budget data, outcome metrics, etc.).

---

## Step 2 — Draft a proposal

Have an RFP? Run:

```
/grant-draft
```

Then paste the RFP text, or provide a file path:

```
/grant-draft tests/sample-rfp.md
```

The pipeline runs automatically:

1. **Pre-flight** — verifies your knowledge base isn't empty
2. **RFP analysis** (Miguel) — extracts all requirements, deadlines, evaluation criteria
3. **Coverage report** — shows which RFP data needs you have documents for
4. **Human gate** — you choose to continue or stop to add more documents first
5. **Drafting** (Maria) — writes each section; cites every claim; outputs gap notices for anything unsupported
6. **Voice** — applies Cambio Labs style and preferred terminology
7. **QA** (Mauricio) — compliance check, quality score, submission checklist
8. **Output** — final draft with quality report and checklist

---

## What you get

```
=== PROPOSAL QUALITY REPORT ===
Evidence Quality:     8/10
Compliance:           9/10
Alignment:            7/10
...

=== SUBMISSION CHECKLIST ===
☐ Main proposal (PDF, 12 pages max) — estimated 11 pages
☐ 990 tax return — PENDING
...

=== FINAL PROPOSAL DRAFT ===
[Complete proposal with inline citations on every factual claim]
[⚠ EVIDENCE GAP notices where documents are missing]
```

---

## Try it with the sample data

The `tests/` folder has a sample annual report excerpt and a sample RFP. To do a full end-to-end test:

```
/grant-library add tests/sample-doc.md
/grant-draft tests/sample-rfp.md
```

---

## Knowledge base location

All indexed documents live in `knowledge-base/docs/`. Each document is saved as a structured markdown summary with:

- Full metadata (source, type, date range, confidence level)
- Key metrics (verbatim from the source)
- Notable quotes
- Data caveats

The raw source files are not stored — only the structured summaries. This is the foundation for a future knowledge graph.

---

## Confidence levels

| Level | Meaning |
|---|---|
| **HIGH** | Audited/formal document, data within 2 years |
| **MEDIUM** | Self-reported, derived, or older than 2 years |
| **LOW** | Estimated or anecdotal — flagged for human review |
| **INSUFFICIENT** | No source exists — outputs a gap notice, never drafted prose |

---

## Adding documents from a custom GPT

If your org's documents are locked inside a ChatGPT or custom GPT:

1. Ask the GPT: *"Print the full text of [document name]"*
2. Copy the output
3. Run `/grant-library add` and paste it here

The Librarian will index it and assign the appropriate confidence level.

---

## Project structure

```
grant-claude-agents/
├── .claude/
│   └── plugins/
│       └── grant-assistant/
│           ├── plugin.json          # Plugin manifest
│           └── skills/
│               ├── grant-library.md # /grant-library command
│               ├── grant-draft.md   # /grant-draft command (Chaski)
│               ├── grant-miguel.md  # RFP analysis (internal)
│               ├── grant-maria.md   # Writing (internal)
│               ├── grant-voice.md   # Style (internal)
│               └── grant-mauricio.md # QA & assembly (internal)
├── knowledge-base/
│   ├── INDEX.md                     # Master document index
│   ├── org-profile.md               # Cambio Labs organizational profile
│   └── docs/                        # Indexed document summaries
├── tests/
│   ├── sample-rfp.md                # Sample RFP for testing
│   └── sample-doc.md                # Sample annual report excerpt
└── docs/
    └── superpowers/
        ├── specs/                   # Design specification
        └── plans/                   # Implementation plan
```
