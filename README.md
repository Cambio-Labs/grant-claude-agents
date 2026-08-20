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

And one agent outside that pipeline:

| Agent | Role |
|---|---|
| **Cambio Voice** | Writes non-funder material — donor emails, outreach, social, speeches, pitches — using real Cambio Labs writing as reference |

You interact with three commands: `/grant-library`, `/grant-draft`, and `/cambio-voice`.

---

## Requirements

- [Claude Code](https://claude.ai/code) (the CLI or desktop app)
- This repository open as your working directory

---

## Setup

**Working directly in this repo** (Tran, or anyone with it cloned): open Claude Code in this directory — the plugin at `.claude/plugins/grant-assistant/` and the standalone skills in `.claude/skills/` are both already there. No install step needed.

**Installing as a plugin elsewhere** (any teammate, in any project):

```
/plugin marketplace add Cambio-Labs/grant-claude-agents
/plugin install grant-assistant@grant-claude-agents
```

This bundles a real copy of `knowledge-base/` (org profile, voice profile, indexed documents) with the plugin — it works immediately, no need to re-add documents.

### Connecting your Google Drive (optional, per person)

Funder scouting can pull from past applications in your personal Google Drive. This isn't bundled with the plugin — there's no shared/org-wide connection to install, since Google Drive access in Claude is a personal connector tied to your own account. To enable it: in Claude, go to **Settings → Connectors**, add **Google Drive**, and sign in. Each teammate who wants this does it once for their own account; skipping it just means the assistant falls back to local/bundled references instead of your Drive.

### Staying up to date

The plugin's version is tied to this repo's git commit history (not a fixed version number), so Claude Code checks for new commits automatically in the background each session and updates installed copies on its own — no manual reinstall needed to pick up new knowledge-base documents or skill changes pushed here.

One caveat since this repo is **private**: that background check needs working git authentication. It works automatically if your git is set up over SSH (a key loaded in `ssh-agent`). Over HTTPS, the background check's first attempt can't use a stored credential helper, but it falls back to a full re-clone that does use your credentials — so it still catches up, just less efficiently. If updates ever seem stale, run `/plugin marketplace update` manually to force it.

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

## Writing that isn't a grant

Grant proposals are only part of what gets written. For donor emails, participant and community
outreach, social posts, newsletters, speeches, investor pitches, and about-us copy:

```
/cambio-voice
```

It asks what you're writing and who reads it, then pulls the matching examples from the voice library
and drafts in that register. It also rewrites: paste something in and ask to make it sound like
Cambio Labs, and it will change how it reads without touching what it claims.

Every output ends with a `VOICE REFERENCES` block naming exactly which examples it drew on — so you
can tell whether the voice was matched against a real reference or extrapolated from an adjacent one.

### The voice library

`knowledge-base/voice-profile.md` describes the voice as rules. `knowledge-base/voice-library/` holds
the **examples**. Rules alone produce prose that breaks none of them and still sounds like nobody;
the examples are what fix that.

```
knowledge-base/voice-library/
├── INDEX.md        # router: what you're writing → which references to load
├── audit/          # AUTHORITATIVE — Cambio's own voice guide + 50-row real-copy database
├── profiles/       # distilled voice cards, one per register
└── sources/        # long-form real writing, with verbatim excerpts
```

**`audit/` is the authority.** It's Cambio Labs' own voice guide (`voice-audit-guide.md`) — eight
rules, a unified CTA table, and rules written directly for AI agents including Claude Code — built
from a real audit of 50 pieces of actual outbound copy (`sample-database.md`, also in `audit/`). Where
anything else in the library conflicts with it, it wins. `/cambio-voice` and `grant-voice` both read
it before anything else.

`profiles/` and `sources/` are supplementary: hand-built cards derived from three long-form pieces
(a pitch script, a manifesto, an album's front matter), useful for rhythm and structure where the
audit's shorter real-copy rows don't cover the register.

`INDEX.md` is the router. It maps each output type to a profile card and a short list of sources
(plus the matching rows in the audit database), and only that row gets loaded — pulling the whole
library at once averages the voices back into a generic register.

Add a new long-form sample:

```
/grant-library add-voice [filepath or pasted transcript]
```

Two things the library is deliberately not: it is **never evidence** — nothing in it may be cited as
the source of a factual claim, even when an excerpt contains a real number — and it **never overrides
the terminology table** in `voice-profile.md` (which itself now has two tables: a core one for
everyone, and a funder-facing word bank from the audit's Rule 08 — see that file).

Current state: audit guide and 50-row database in; 6 of 10 hand-built profile sources indexed
(investor pitch, board/strategy, and tech-vision registers are now backed by real transcripts), with
the rest either awaiting transcript or (Harlem workshop) blocked on a video's sharing settings. Rows
pointing at pending sources still work, but they say so in their output.

### Sparky's voice — separate from this plugin

`knowledge-base/sparky/conversation-log.md` is a real eval log of Sparky, the AI tutor embedded in
the Journey platform — a distinct, live, unsupervised consumer of Cambio's voice, not something this
plugin drives. Kept for reference and because it documents an open question ("is Sparky a distinct
character or just Cambio's voice in a chat window?") flagged for Leadership.

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
│               ├── cambio-voice.md  # /cambio-voice command
│               ├── grant-miguel.md  # RFP analysis (internal)
│               ├── grant-maria.md   # Writing (internal)
│               ├── grant-voice.md   # Style (internal)
│               └── grant-mauricio.md # QA & assembly (internal)
├── knowledge-base/
│   ├── INDEX.md                     # Master document index
│   ├── org-profile.md               # Cambio Labs organizational profile
│   ├── voice-profile.md             # Voice rules (formality, terminology)
│   ├── voice-library/               # Voice examples
│   │   ├── INDEX.md                 # Router: output type → references
│   │   ├── profiles/                # Distilled voice cards
│   │   └── sources/                 # Real writing, verbatim excerpts
│   └── docs/                        # Indexed document summaries
├── tests/
│   ├── sample-rfp.md                # Sample RFP for testing
│   └── sample-doc.md                # Sample annual report excerpt
└── docs/
    └── superpowers/
        ├── specs/                   # Design specification
        └── plans/                   # Implementation plan
```
