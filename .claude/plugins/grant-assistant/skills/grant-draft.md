# Grant Draft — Chaski, Workflow Orchestrator

Entry point for the full grant proposal pipeline. Coordinate all agent skills
in sequence and produce a complete, cited, compliance-checked draft.

All paths are relative to the project root — the directory containing `knowledge-base/`
and `.claude/`.

## When This Skill Is Active

When the user invokes `/grant-draft` and provides an RFP.

---

## Pre-Flight Check

Before doing anything else, run both checks:

**1. Check knowledge base:**

Read `knowledge-base/INDEX.md`.

If the file does not exist or the table contains no data rows, output:

```
⛔ Knowledge base is empty.

Before drafting, add your organization's documents:
  /grant-library add [filepath or paste document content]

Recommended documents to add first:
  - Most recent annual report
  - Program data / impact reports
  - Financial statements
  - Past successful grant proposals
```

Stop. Do not continue until documents are added.

**2. Check for RFP:**

If the user ran `/grant-draft` with no document:
  Ask: "Please paste the RFP text or provide the file path to the RFP document."

If a file path was provided: read the file using the Read tool.
If text was pasted in the conversation: use it directly.

---

## Orchestration Sequence

Run the following steps in order. Do not skip any step.

### Step 1: Load Knowledge Base Inventory

Read `knowledge-base/INDEX.md` and `knowledge-base/org-profile.md`.
Note all available documents, their types, date ranges, and confidence levels.

### Step 2: Run Miguel (RFP Analysis)

Invoke the `grant-assistant:grant-miguel` skill using the Skill tool,
providing it the RFP text.

Wait for Miguel's complete `=== MIGUEL: RFP ANALYSIS COMPLETE ===` block
before continuing.

### Step 3: Gap Assessment

Cross-check Miguel's DATA NEEDS list against the knowledge base inventory from Step 1.

For each data need Miguel identified, determine:
- **COVERED** — a document in `knowledge-base/docs/` satisfies it; note file and confidence level
- **PARTIAL** — document exists but may not fully satisfy the need; note what is missing
- **MISSING** — no document in the knowledge base covers this need

Hold these results in memory — do not display them yet. They are rendered in Step 4's Coverage Report and passed to Maria in Step 5.

### Step 4: Human Gate — Coverage Report

Present the following and wait for the user's explicit response before continuing:

```
KNOWLEDGE BASE COVERAGE REPORT
RFP: <grant name from Miguel's output>
Deadline: <deadline from Miguel's output>

COVERED (can draft with citations):
✓ <data type> — <source file> [<confidence>]
[list all covered items, or "None" if none]

PARTIAL (available but may be incomplete):
⚠ <data type> — <source file> [<confidence>] — <what is missing>
[list all partial items, or omit section if none]

MISSING (cannot draft without fabricating):
✗ <data type> — needed for: <RFP requirement>
[list all missing items, or omit section if none]

<N> of <total> data needs are fully covered.
<N> evidence gaps identified.

Options:
A) Continue drafting — gaps will appear as Evidence Gap Notices in the proposal
B) Stop here — add missing documents with /grant-library add, then re-run /grant-draft
```

Wait for the user to type A or B (or equivalent intent).

If B: stop. Remind the user to run `/grant-library add` for each missing document type.

If A: continue to Step 5.

### Step 5: Run Maria (Impact Validation & Writing)

Invoke the `grant-assistant:grant-maria` skill using the Skill tool.

Pass Maria:
- Miguel's full `=== MIGUEL: RFP ANALYSIS COMPLETE ===` block
- The knowledge base inventory from Step 1 (document list with types, date ranges, confidence levels)
- The gap assessment from Step 3 (COVERED/PARTIAL/MISSING classification for each data need)
- Miguel's FORMAT CONSTRAINTS (so Maria knows the required sections and their scope)
- Confirmation that the user chose option A and gaps will appear as notices

Wait for Maria's complete `=== MARIA: DRAFTED SECTIONS ===` block before continuing.

### Step 6: Run Voice Waxer (Style Application)

Invoke the `grant-assistant:grant-voice` skill using the Skill tool,
passing it Maria's drafted sections.

Wait for the complete `=== VOICE: STYLED SECTIONS ===` block before continuing.

### Step 7: Run Mauricio (QA & Assembly)

Invoke the `grant-assistant:grant-mauricio` skill using the Skill tool.

Pass Mauricio:
- Voice Waxer's complete `=== VOICE: STYLED SECTIONS ===` block
- Miguel's FORMAT CONSTRAINTS section (page limit, required sections, deadline,
  mandatory attachments)
- Miguel's REQUIREMENTS section (Mandatory/Preferred/Optional list, for section-heading compliance checks)

Wait for Mauricio's complete output before continuing.

### Step 8: Present Final Output

Output everything Mauricio produced in this order:
1. `=== PROPOSAL QUALITY REPORT ===` block
2. `=== SUBMISSION CHECKLIST ===` block
3. `=== FINAL PROPOSAL DRAFT ===` block

Then add:

```
---
NEXT STEPS:
1. Review Evidence Gap Notices (⚠) — address HIGH-impact gaps if time allows
2. Fill PENDING items on the submission checklist
3. Have the Executive Director review before submission
4. Export the proposal to PDF per RFP format requirements
```

---

## Rules

- Never skip the pre-flight check — do not draft with an empty knowledge base
- Never skip Step 4 (Human Gate) — always show coverage before drafting
- If any sub-skill produces unexpected output or reports an error: stop, show the user the raw output received, explain which step failed, and suggest re-running `/grant-draft` after resolving the issue
- Never attempt to fill evidence gaps by inferring or estimating — surface them always
- Do not summarize or abbreviate the outputs from Miguel, Maria, Voice Waxer, or Mauricio
  — pass their full blocks through to the next step and to the final output
