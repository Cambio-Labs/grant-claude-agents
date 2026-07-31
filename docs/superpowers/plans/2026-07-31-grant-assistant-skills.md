# Grant Assistant Skills Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a 6-skill Claude Code plugin that enables Cambio Labs grant writers to produce fully-cited, anti-hallucination grant proposals from a persistent knowledge base.

**Architecture:** A plugin at `.claude/plugins/grant-assistant/` with two user-facing skills (`/grant-library` to manage the knowledge base, `/grant-draft` to orchestrate the full pipeline) and four internal agent skills (Miguel, Maria, Voice, Mauricio) invoked by the orchestrator. All factual claims in output are traced to source documents stored as structured `.md` summaries in `knowledge-base/docs/`.

**Tech Stack:** Claude Code skills (Markdown), knowledge-base as structured `.md` files, no external dependencies.

---

## File Map

**Create:**
- `.claude/plugins/grant-assistant/plugin.json` — plugin manifest
- `.claude/plugins/grant-assistant/skills/grant-library.md` — Librarian skill (user-facing)
- `.claude/plugins/grant-assistant/skills/grant-draft.md` — Chaski orchestrator skill (user-facing)
- `.claude/plugins/grant-assistant/skills/grant-miguel.md` — RFP analysis (internal)
- `.claude/plugins/grant-assistant/skills/grant-maria.md` — impact validation & writing (internal)
- `.claude/plugins/grant-assistant/skills/grant-voice.md` — style application (internal)
- `.claude/plugins/grant-assistant/skills/grant-mauricio.md` — QA & assembly (internal)
- `knowledge-base/INDEX.md` — master document index (template)
- `knowledge-base/org-profile.md` — Cambio Labs organization profile
- `tests/sample-rfp.md` — sample RFP for smoke testing
- `tests/sample-doc.md` — sample annual report excerpt for smoke testing

---

### Task 1: Scaffold plugin structure and seed knowledge base

**Files:**
- Create: `.claude/plugins/grant-assistant/plugin.json`
- Create: `knowledge-base/INDEX.md`
- Create: `knowledge-base/org-profile.md`
- Create: `tests/sample-rfp.md`

- [ ] **Step 1: Create directories**

```bash
mkdir -p .claude/plugins/grant-assistant/skills
mkdir -p knowledge-base/docs
mkdir -p tests
```

- [ ] **Step 2: Create plugin manifest**

Create `.claude/plugins/grant-assistant/plugin.json`:

```json
{
  "name": "grant-assistant",
  "version": "1.0.0",
  "description": "AI grant writing assistant for Cambio Labs — evidence-only, citation-required proposals",
  "skills": [
    "grant-library",
    "grant-draft"
  ]
}
```

- [ ] **Step 3: Create INDEX.md template**

Create `knowledge-base/INDEX.md`:

```markdown
# Knowledge Base Index
Last updated: [date]

| File | Type | Date Range | Confidence | Key Metrics |
|---|---|---|---|---|

## Summary
- Total documents indexed: 0
- Coverage gaps: Run `/grant-library status` for analysis
```

- [ ] **Step 4: Create org-profile.md**

Create `knowledge-base/org-profile.md`:

```markdown
# Cambio Labs — Organization Profile
Source: Compiled from organizational materials
Last updated: 2026-07-31
Confidence: MEDIUM (verify against latest documents before citing)

## Mission
Transform economic mobility for BIPOC communities through entrepreneurship, technology
education, and community-led economic development.

## Location
South Bronx, New York City (primary service area)
NYCHA public housing communities (key focus)

## Tax Status
501(c)(3) nonprofit organization

## Key Programs

### Journey Platform
Digital skills and entrepreneurship education platform.
- Target: BIPOC youth and adults
- Focus: Digital literacy, tech skills, workforce readiness

### Startup NYCHA
Business accelerator for NYCHA residents.
- Target: NYCHA public housing residents
- Components: Mentorship, seed funding access, pitch competition
- Link to Journey Platform for skills training

### Train-the-Trainer
Community capacity building program.

## Beneficiary Segments
- Low-income adults
- BIPOC youth
- NYCHA residents
- South Bronx residents
- Immigrant entrepreneurs
- Public school students
- Aspiring founders
- Green workforce learners

## Organizational Voice
- Professional-warm tone
- Community-centered framing
- Evidence-based, not aspirational
- Humble but confident
- Inclusive language required

### Preferred Terminology
| Use | Avoid |
|---|---|
| participants | clients, beneficiaries |
| community members | target population |
| South Bronx | "underserved area" |
| community-led | top-down |

## Key Statistics
[To be populated from uploaded documents — do not infer or fabricate]

## Past Funders
[To be populated from uploaded documents]
```

- [ ] **Step 5: Create sample RFP for testing**

Create `tests/sample-rfp.md`:

```markdown
# Sample RFP: Community Economic Empowerment Grant 2026
Funder: Test Foundation
Deadline: 2026-09-30 5:00 PM EST
Submission: Online portal only

## Eligibility
- 501(c)(3) status required
- Minimum 2 years of operations
- Service area: New York City

## Required Sections
1. Executive Summary (500 words max)
2. Organizational Background
3. Program Description
4. Target Population and Need
5. Goals and Outcomes
6. Evaluation Plan
7. Budget Narrative
8. Sustainability Plan

## Mandatory Attachments
- IRS 501(c)(3) determination letter
- Most recent audited financial statements
- Board of Directors list with affiliations
- Letters of support (minimum 2)

## Format Requirements
- Page limit: 12 pages (excluding attachments)
- Font: 12pt Times New Roman or Arial
- Margins: 1 inch all sides
- File format: PDF

## Evaluation Criteria
- Organizational Capacity: 30%
- Program Design: 35%
- Community Need and Target Population: 20%
- Sustainability: 15%

## Funding Amount
Up to $75,000 for one year
```

- [ ] **Step 6: Verify structure**

```bash
find .claude/plugins/grant-assistant knowledge-base tests -type f | sort
```

Expected output:
```
.claude/plugins/grant-assistant/plugin.json
knowledge-base/INDEX.md
knowledge-base/org-profile.md
tests/sample-rfp.md
```

- [ ] **Step 7: Initialize git and commit**

```bash
git init
git add .claude/plugins/grant-assistant/plugin.json knowledge-base/ tests/sample-rfp.md
git commit -m "feat: scaffold grant-assistant plugin structure and seed knowledge base"
```

---

### Task 2: Implement `/grant-library` skill (Librarian)

**Files:**
- Create: `.claude/plugins/grant-assistant/skills/grant-library.md`

- [ ] **Step 1: Create grant-library.md**

Create `.claude/plugins/grant-assistant/skills/grant-library.md` with the full content below:

```markdown
# Grant Library — The Librarian

Manage the grant assistant knowledge base. Run this before `/grant-draft` to ensure
documents are indexed and available for citation.

## When This Skill Is Active

When the user invokes `/grant-library` with a subcommand: `add`, `list`, or `status`.

---

## Command: `/grant-library add [filepath or pasted content]`

Process a document and add it to the knowledge base.

### Steps

1. Determine input type:
   - If the argument looks like a file path (e.g. `2024_report.pdf`, `docs/data.pdf`),
     read the file using the Read tool
   - If it is pasted text, use it directly
   - If unclear, ask: "Is this a file path or pasted document content?"

2. Identify document type from content:
   - `annual-report` — year-in-review, program stats, overall org metrics
   - `financial` — budget, audit, financial statements, 990 form
   - `program-data` — specific program metrics, participant data, outcomes
   - `proposal` — past grant application
   - `research` — external research, benchmarks, context data
   - `partner` — letters of support, partnership agreements
   - `general` — anything else

3. Extract the following. ONLY from what is explicitly stated. Do not infer or estimate:
   - Date range covered
   - Key metrics: participant counts, completion rates, outcomes, budgets, demographics
   - Programs mentioned with brief descriptions
   - Named partners and funders
   - Geographic focus / service areas
   - Up to 5 verbatim quotes suitable for narrative use, with page/section references
   - Data caveats or limitations explicitly noted in the document

4. Assign base confidence:
   - HIGH — audited financial document, formal annual report, government-issued document
   - MEDIUM — internal program report, survey data, self-reported outcomes
   - LOW — anecdotal, estimated, or undated document

5. Generate filename slug:
   - Use original filename if from a file path (strip extension, use as slug)
   - If pasted: use `pasted-[type]-[YYYY-MM-DD]`

6. Write summary to `knowledge-base/docs/<slug>.md` using this exact format:

---
source: [original filename or "pasted"]
type: [document type]
date-range: [start to end, or "undated"]
confidence: [HIGH/MEDIUM/LOW]
indexed: [today's date YYYY-MM-DD]
---

## Key Metrics
[bullet list — metric: value [page/section]]
If no metrics: "No quantitative metrics identified in this document"

## Programs
[bullet list — program name: description [page/section]]
If none: "No specific programs mentioned"

## Partners and Funders
[bullet list — name: relationship [page/section]]
If none: "None mentioned"

## Geographic Focus
[service areas mentioned, or "Not specified"]

## Notable Quotes
[numbered list — "exact verbatim text" [page X / section Y]]
If none: "No notable quotes identified"

## Data Caveats
[bullet list of limitations or caveats from the document itself]
If none: "No caveats noted in document"

7. Update `knowledge-base/INDEX.md`:
   - Add row: `| <slug>.md | <type> | <date-range> | <confidence> | <2-3 key metrics> |`
   - Update "Last updated" date
   - Update "Total documents indexed" count

8. Confirm to user:

✓ Document indexed: <slug>.md
Type: <type> | Confidence: <level> | Date range: <range>
Key metrics captured: <N>
Notable quotes: <N>

Knowledge base now contains <N> documents.
Run `/grant-library status` to see coverage gaps.

### HARD RULE
Never write a value in the summary file that cannot be directly quoted or referenced
from the document. If a number appears without a clear source section, write
`[unverified — confirm source]`. Never infer, estimate, or extrapolate.

---

## Command: `/grant-library list`

Show all indexed documents.

1. Read `knowledge-base/INDEX.md`
2. If file does not exist or table is empty, output:
   ```
   Knowledge base is empty.
   Run `/grant-library add [filepath]` to add your first document.
   ```
3. Otherwise output:
   ```
   KNOWLEDGE BASE — <N> documents indexed
   Last updated: <date>

   <Table from INDEX.md>

   Run `/grant-library status` for gap analysis.
   ```

---

## Command: `/grant-library status`

Show data coverage vs. common grant requirements.

1. Read `knowledge-base/INDEX.md` and all files in `knowledge-base/docs/`
2. Check for these commonly required grant data types:
   - Participant/beneficiary counts (current year)
   - Program completion rates
   - Demographic breakdown (race/ethnicity, age, income)
   - Budget / financial data (current year)
   - Short-term outcome data
   - Longitudinal outcome data (6-12 months post-program)
   - 501(c)(3) verification
   - Letters of support / partnership documentation
   - Board of directors information

3. Output:

KNOWLEDGE BASE STATUS

Available (can support grant claims):
✓ <data type> — <source file> [<confidence>]

Partial (available but may be incomplete):
⚠ <data type> — <source file> [<confidence>] — <what's missing>

Missing (common requirement not in knowledge base):
✗ <data type> — add with: /grant-library add <suggested source type>

RECOMMENDATION: Before running /grant-draft, consider adding:
<prioritized list of highest-impact missing documents>
```

- [ ] **Step 2: Verify file was created**

```bash
wc -l .claude/plugins/grant-assistant/skills/grant-library.md
```

Expected: 100+ lines

- [ ] **Step 3: Commit**

```bash
git add .claude/plugins/grant-assistant/skills/grant-library.md
git commit -m "feat: add grant-library (Librarian) skill for knowledge base management"
```

---

### Task 3: Implement `grant-miguel` skill (RFP Analysis)

**Files:**
- Create: `.claude/plugins/grant-assistant/skills/grant-miguel.md`

- [ ] **Step 1: Create grant-miguel.md**

Create `.claude/plugins/grant-assistant/skills/grant-miguel.md`:

```markdown
# Grant Miguel — RFP Analysis Specialist

Extract all requirements, constraints, and data needs from an RFP.
Produce structured output only — no prose, no narrative.

## When This Skill Is Active

When invoked internally by grant-draft (Chaski) with an RFP document.

## Input

The RFP document text (provided in the conversation by Chaski).

## Rules

- Only extract what is explicitly stated in the RFP
- Do NOT consult the knowledge base — read only the RFP
- If a field is not in the RFP, write "Not specified"
- If a requirement is ambiguous, note it: "[ambiguous — interpret as: X]"
- Do not estimate deadlines — if not found, write "Not specified"
- Produce no prose before or after the output blocks

## Output Format

Output ALL blocks below in this exact order:

=== MIGUEL: RFP ANALYSIS COMPLETE ===

FUNDER: [funder name]
GRANT NAME: [grant program name]
DEADLINE: [YYYY-MM-DD HH:MM timezone, or "Not specified"]

---

REQUIREMENTS:

Mandatory:
- [requirement] [RFP section reference]
(list all mandatory requirements)

Preferred:
- [requirement] [RFP section reference]
(list all preferred requirements, or "None specified")

Optional:
- [requirement] [RFP section reference]
(list all optional requirements, or "None specified")

---

MANDATORY ATTACHMENTS:
- [attachment name]
(list all required attachments, or "None specified")

---

FORMAT CONSTRAINTS:
- Page limit: [X pages, note if attachments excluded]
- Word limits: [section-specific limits, or "None specified"]
- Font: [specification, or "Not specified"]
- Margins: [specification, or "Not specified"]
- File format: [PDF / Word / other]
- Submission method: [online portal / email / mail / other]

---

EVALUATION CRITERIA:
- [criterion name]: [weight %] — [brief description from RFP]
(list all criteria with weights; if no weights given, note "Unweighted")

---

DATA NEEDS (prioritized by RFP weight and mandatory status):

HIGH PRIORITY (mandatory requirements):
- [data type needed] — required for: [RFP section/requirement]

MEDIUM PRIORITY (preferred or heavily weighted):
- [data type needed] — required for: [RFP section/requirement]

LOW PRIORITY (optional or low-weight):
- [data type needed] — required for: [RFP section/requirement]

=== END MIGUEL OUTPUT ===
```

- [ ] **Step 2: Commit**

```bash
git add .claude/plugins/grant-assistant/skills/grant-miguel.md
git commit -m "feat: add grant-miguel (RFP analysis) internal skill"
```

---

### Task 4: Implement `grant-maria` skill (Impact Validation & Writing)

**Files:**
- Create: `.claude/plugins/grant-assistant/skills/grant-maria.md`

- [ ] **Step 1: Create grant-maria.md**

Create `.claude/plugins/grant-assistant/skills/grant-maria.md`:

```markdown
# Grant Maria — Impact Validation & Writing Specialist

Match RFP requirements against knowledge base evidence. Write proposal sections
with inline citations. Output Evidence Gap Notices for anything unsupported.

## When This Skill Is Active

When invoked internally by grant-draft (Chaski) with Miguel's structured output
and access to `knowledge-base/`.

## Input

- Miguel's structured RFP analysis (in the conversation)
- `knowledge-base/docs/` — read all files to find evidence
- `knowledge-base/org-profile.md` — organizational context

## Confidence Scoring

| Level | Criteria | Action |
|---|---|---|
| HIGH | Direct quote, audited/formal doc, within 2 years | Include with citation |
| MEDIUM | Derived from reliable source, self-reported, or >2 years old | Include with caveat in citation |
| LOW | Estimated or anecdotal | Flag for human review — include only with explicit review note |
| INSUFFICIENT | No source in knowledge base | Output Evidence Gap Notice — never draft prose for this claim |

## Citation Tag Format

Every factual claim MUST end with:

[Source: <filename> | <page or section> | Confidence: HIGH/MEDIUM/LOW]

Examples:
- [Source: 2024_annual_report.md | p.12 | Confidence: HIGH]
- [Source: 2024_survey.md | n=285, self-reported | Confidence: MEDIUM]

## Evidence Gap Notice Format

When evidence is INSUFFICIENT, output this block instead of prose:

⚠ EVIDENCE GAP — [Requirement: exact requirement from RFP]
Available: [what exists in knowledge base, file, confidence — or "Nothing in knowledge base"]
Missing: [specific data that would satisfy this requirement]
Recommendation: [where to find it or how to gather it]
Impact on proposal: HIGH / MEDIUM / LOW

## Challenge Mode — Run Before Writing Each Section

Before drafting each section, flag any of these patterns:

- Source says "completion rate" but claim would use "success rate" →
  flag: "Source states completion rate, not success rate — do not conflate"
- Comparison to national/regional average from source >3 years old →
  flag: "Benchmark is [year] — flag as MEDIUM confidence"
- Claim uses vague language ("significant improvement") without a metric →
  flag: "No metric defined — require a number or remove the claim"
- Statistic in org-profile.md has no backing source document →
  flag as MEDIUM confidence at best

## Task

For each section required by the RFP (from Miguel's REQUIREMENTS):

1. Read relevant files in `knowledge-base/docs/` and `knowledge-base/org-profile.md`
2. Run Challenge Mode — flag any weak evidence before writing
3. Draft the section with inline citation tags for every factual claim
4. For INSUFFICIENT evidence: output an Evidence Gap Notice instead of prose

Write sections in RFP order (from Miguel's REQUIREMENTS list).

## Hard Rules

1. Never write a number, statistic, outcome, or quote without a citation tag
2. Never cite a document not in `knowledge-base/docs/` or `knowledge-base/org-profile.md`
3. INSUFFICIENT evidence = gap notice only — no prose
4. Confidence levels are fixed — cannot upgrade without a stronger source document
5. Quotes must be verbatim from source — no paraphrasing presented as a direct quote

## Output Format

=== MARIA: DRAFTED SECTIONS ===

## [Section Name]

[Drafted prose with inline citation tags on every factual claim]

[Evidence Gap Notices for any unsupported claims]

---

## [Next Section Name]

[...]

CHALLENGE FLAGS:
- [any weak evidence flags raised during review]

=== END MARIA OUTPUT ===
```

- [ ] **Step 2: Commit**

```bash
git add .claude/plugins/grant-assistant/skills/grant-maria.md
git commit -m "feat: add grant-maria (impact validation & writing) internal skill"
```

---

### Task 5: Implement `grant-voice` skill (Voice Waxer)

**Files:**
- Create: `.claude/plugins/grant-assistant/skills/grant-voice.md`

- [ ] **Step 1: Create grant-voice.md**

Create `.claude/plugins/grant-assistant/skills/grant-voice.md`:

```markdown
# Grant Voice — Organizational Style Specialist

Apply Cambio Labs' organizational voice consistently across all proposal sections.
Never add new facts. Never remove or alter citation tags.

## When This Skill Is Active

When invoked internally by grant-draft (Chaski) with Maria's drafted sections.

## Input

Maria's drafted proposal sections (provided in the conversation).

## Cambio Labs Voice Profile

Formality: professional-warm
Sentence length: 15–20 words average; mix simple and complex sentences
Active voice: 80% preferred — rewrite passive constructions when natural
Vocabulary:
  - Avoid jargon without explanation and corporate-speak
  - Emotional language: moderate and strategic — not overwrought
  - Power words: transform, empower, sustainable, community-driven, community-led, resilient

Tone:
  - Collaborative, not hierarchical
  - Evidence-based, not aspirational
  - Humble but confident
  - Inclusive language — no othering language

Terminology:
  USE "participants"        AVOID "clients", "beneficiaries", "recipients"
  USE "community members"   AVOID "target population", "the underserved"
  USE "South Bronx"         AVOID "underserved area", "low-income neighborhood" as primary label
  USE "community-led"       AVOID "top-down", "charity model"

## Task

For each section from Maria's output:

1. Rewrite for voice consistency — apply the profile above
2. Fix terminology to match preferred terms table
3. Convert passive constructions to active voice when natural
4. Break sentences longer than 30 words into two
5. Check cross-section consistency — opening and closing should feel the same voice
6. Flag any section that sounds notably different from the others

## Preserve — Do Not Alter

- All citation tags `[Source: ... | Confidence: ...]` — do not move, remove, or alter
- All Evidence Gap Notices `⚠ EVIDENCE GAP` — do not remove
- All section headings
- All factual content — do not add, remove, or change numbers, names, or claims
- Verbatim quotes already in citations

## Do Not

- Add new facts or claims
- Remove citation tags under any circumstances
- Change evaluation criteria language from Miguel's analysis
- Alter the text inside verbatim quotes

## Output Format

=== VOICE: STYLED SECTIONS ===

## [Section Name]

[Styled prose with all citation tags intact]

[Evidence Gap Notices unchanged]

---

## [Next Section Name]

[...]

VOICE NOTES:
- [sections that needed heavy revision and why]
- [terminology corrections made — list each]
- [cross-section consistency flags if any]

=== END VOICE OUTPUT ===
```

- [ ] **Step 2: Commit**

```bash
git add .claude/plugins/grant-assistant/skills/grant-voice.md
git commit -m "feat: add grant-voice (style application) internal skill"
```

---

### Task 6: Implement `grant-mauricio` skill (QA & Assembly)

**Files:**
- Create: `.claude/plugins/grant-assistant/skills/grant-mauricio.md`

- [ ] **Step 1: Create grant-mauricio.md**

Create `.claude/plugins/grant-assistant/skills/grant-mauricio.md`:

```markdown
# Grant Mauricio — Quality Assurance & Assembly Specialist

Compile all styled sections into the final proposal. Run compliance checks.
Score quality. Generate submission checklist.

## When This Skill Is Active

When invoked internally by grant-draft (Chaski) with Voice Waxer's output
and Miguel's format constraints.

## Input

- Voice Waxer's styled sections (in conversation)
- Miguel's FORMAT CONSTRAINTS and REQUIREMENTS (in conversation)
- `knowledge-base/INDEX.md` (read for citation verification)

## Task

### Step 1: Assemble the Proposal

Arrange sections in the exact order specified by Miguel's RFP REQUIREMENTS list.
If RFP order is ambiguous, use:
Executive Summary → Organizational Background → Program Description →
Target Population and Need → Goals and Outcomes → Evaluation Plan →
Budget Narrative → Sustainability Plan

Do not reorder sections that the RFP specifies explicitly.

### Step 2: Run Compliance Checks

Check each item and record PASS / WARNING / FAIL:

Format:
- Page count vs. limit (estimate: 1 page ≈ 500 words)
- All mandatory sections present (check Miguel's REQUIREMENTS)
- All mandatory attachments listed

Content:
- Every factual claim has a [Source: ... | Confidence: ...] tag
- No uncited statistics or numbers in final text
- All Evidence Gap Notices present and clearly marked ⚠

Deadline:
- If deadline specified: calculate time remaining
- If < 72 hours remaining: flag as CRITICAL

### Step 3: Generate Quality Report

=== PROPOSAL QUALITY REPORT ===

OVERALL SCORE: <X>/10

Evidence Quality:     <X>/10
  - Citation coverage: <X>% of factual claims cited
  - Average confidence: <distribution of HIGH/MEDIUM/LOW>
  - Data recency: <% of sources within 2 years>

Compliance:           <X>/10
  - Format requirements: <X>% met
  - Mandatory sections: <X>/<total> present
  - Mandatory attachments: <X>/<total> ready

Alignment:            <X>/10
  - Evaluation criteria coverage: <X>%
  - Highest-weight criterion addressed: YES / PARTIAL / NO

Readability:          <X>/10
  - Voice corrections made: <count>
  - Terminology corrections: <count>

Completeness:         <X>/10
  - Evidence gaps documented: <count>
  - Sections fully supported: <X>/<total>

CRITICAL ISSUES: <list anything blocking submission, or "None">

WARNINGS:
- <warning description> — <how to resolve>

RECOMMENDATIONS (prioritized):
1. <action> — estimated time: <X min>
2. [...]

=== END QUALITY REPORT ===

### Step 4: Generate Submission Checklist

=== SUBMISSION CHECKLIST ===
Generated from RFP requirements

DOCUMENTS:
☐ Main proposal (<file format>, <page limit> max) — <estimated page count> pages
☐ <mandatory attachment 1> — READY / PENDING
☐ <mandatory attachment 2> — READY / PENDING
[list all mandatory attachments from Miguel's output]

SUBMISSION:
☐ <submission method — portal / email / mail>
☐ All required fields completed
☐ Confirmation receipt saved

DEADLINE: <date time timezone, or "Not specified in RFP">
TIME REMAINING: <X days, X hours, or "Deadline not specified">
STATUS: ON TRACK / AT RISK / CRITICAL

=== END SUBMISSION CHECKLIST ===

### Step 5: Output Final Proposal

After the reports, output the assembled proposal:

=== FINAL PROPOSAL DRAFT ===

[All styled sections in RFP-required order]

=== END FINAL PROPOSAL DRAFT ===

## Rules

- Do not alter content — assemble and check only, do not rewrite
- If a factual claim has no citation tag: flag in WARNINGS, do not add a fake citation
- If a mandatory section is missing: flag as CRITICAL ISSUE
- Do not remove Evidence Gap Notices from the final draft
```

- [ ] **Step 2: Commit**

```bash
git add .claude/plugins/grant-assistant/skills/grant-mauricio.md
git commit -m "feat: add grant-mauricio (QA & assembly) internal skill"
```

---

### Task 7: Implement `/grant-draft` skill (Chaski — Orchestrator)

**Files:**
- Create: `.claude/plugins/grant-assistant/skills/grant-draft.md`

- [ ] **Step 1: Create grant-draft.md**

Create `.claude/plugins/grant-assistant/skills/grant-draft.md`:

```markdown
# Grant Draft — Chaski, Workflow Orchestrator

Entry point for the full grant proposal pipeline. Coordinate all agent skills
in sequence and produce a complete, cited, compliance-checked draft.

## When This Skill Is Active

When the user invokes `/grant-draft` and provides an RFP.

---

## Pre-Flight Check

Before anything else:

**1. Check knowledge base:**

Read `knowledge-base/INDEX.md`.

If the file does not exist or the table contains no rows, output:

⛔ Knowledge base is empty.

Before drafting, add your organization's documents:
  /grant-library add [filepath or paste document content]

Recommended documents to add first:
  - Most recent annual report
  - Program data / impact reports
  - Financial statements
  - Past successful grant proposals

Stop. Do not continue until documents are added.

**2. Check for RFP:**

If the user ran `/grant-draft` with no document:
  Ask: "Please paste the RFP text or provide the file path to the RFP document."

If a file path was provided: read the file using the Read tool.
If text was pasted: use it directly.

---

## Orchestration Sequence

Run the following steps in order. Do not skip steps.

### Step 1: Load Knowledge Base Inventory

Read `knowledge-base/INDEX.md`.
Read `knowledge-base/org-profile.md`.
Note all available documents and their types and confidence levels.

### Step 2: Run Miguel (RFP Analysis)

Invoke the `grant-assistant:grant-miguel` skill using the Skill tool,
passing it the RFP text.

Wait for Miguel's complete structured output before continuing.

### Step 3: Gap Assessment

Cross-check Miguel's DATA NEEDS list against the knowledge base inventory.

For each data need Miguel identified, determine:
- COVERED — a document in `knowledge-base/docs/` satisfies it; note file and confidence
- PARTIAL — a document exists but may not fully satisfy the need; note what is missing
- MISSING — no document in the knowledge base covers this need

### Step 4: Human Gate — Coverage Report

Present this to the grant writer and wait for their explicit response:

KNOWLEDGE BASE COVERAGE REPORT
RFP: <grant name from Miguel's output>
Deadline: <deadline from Miguel's output>

COVERED (can draft with citations):
✓ <data type> — <source file> [<confidence>]
[list all covered items]

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

Wait for the user to choose A or B before continuing.

If B: stop. Remind the user to run `/grant-library add` for each missing document.

If A: continue to Step 5.

### Step 5: Run Maria (Impact Validation & Writing)

Invoke the `grant-assistant:grant-maria` skill using the Skill tool.

Provide Maria with Miguel's full structured output and confirmation that
the user chose to proceed (option A) with known gaps.

Wait for Maria's complete drafted sections before continuing.

### Step 6: Run Voice Waxer (Style Application)

Invoke the `grant-assistant:grant-voice` skill using the Skill tool,
passing it Maria's drafted sections.

Wait for Voice Waxer's styled sections before continuing.

### Step 7: Run Mauricio (QA & Assembly)

Invoke the `grant-assistant:grant-mauricio` skill using the Skill tool.

Provide Mauricio with:
- Voice Waxer's styled sections
- Miguel's FORMAT CONSTRAINTS section (page limit, required sections,
  deadline, mandatory attachments)

Wait for Mauricio's complete output before continuing.

### Step 8: Present Final Output

Output everything Mauricio produced in this order:
1. Quality Report
2. Submission Checklist
3. Final Proposal Draft

Then add:

---
NEXT STEPS:
1. Review Evidence Gap Notices — address HIGH-impact gaps if time allows
2. Fill PENDING items on the submission checklist
3. Have the Executive Director review before submission
4. Export the proposal to PDF per RFP format requirements

---

## Rules

- Never skip the pre-flight check — no drafting with an empty knowledge base
- Never skip Step 4 (Human Gate) — always show coverage before drafting
- Never pass LOW or INSUFFICIENT evidence to Voice Waxer as accepted claims
- If any step produces unexpected output: stop and explain the issue before continuing
- Never attempt to fill gaps by inferring or estimating — surface them as gap notices
```

- [ ] **Step 2: Verify all 6 skill files exist**

```bash
ls .claude/plugins/grant-assistant/skills/
```

Expected — exactly these 6 files:
```
grant-draft.md
grant-library.md
grant-maria.md
grant-mauricio.md
grant-miguel.md
grant-voice.md
```

- [ ] **Step 3: Commit**

```bash
git add .claude/plugins/grant-assistant/skills/grant-draft.md
git commit -m "feat: add grant-draft (Chaski orchestrator) — completes grant-assistant plugin"
```

---

### Task 8: End-to-End Smoke Test

**Goal:** Verify the full workflow runs correctly with a sample document and RFP.

**Files:**
- Create: `tests/sample-doc.md`

- [ ] **Step 1: Create sample annual report excerpt**

Create `tests/sample-doc.md`:

```markdown
# Test Annual Report 2024 — Excerpt
(Test document for grant assistant smoke testing)

## Program Statistics — page 12
In fiscal year 2024, Cambio Labs served 347 young people through our core programs.

## Completion Rates — page 14
Eighty-two percent (82%) of participants completed the full program curriculum.
Of completers, 78% reported improved digital skills in follow-up surveys (n=285,
self-reported data collected December 2024).

## Demographics — page 16
95% of participants identified as Black, Indigenous, or People of Color (BIPOC).
68% of participants were from NYCHA public housing communities.
Average household income of participants: $28,000/year.

## Programs — page 8
Journey Platform: 200 participants completed the 12-week digital skills curriculum.
Startup NYCHA: 147 participants in the business accelerator cohort.

## Financials — page 18
Total organizational budget 2024: $2,300,000
Program expenses: $1,840,000 (80% of total budget)
Administrative expenses: $460,000 (20% of total budget)

## Participant Voices — page 24
"This program changed my relationship with technology. I went from afraid of
computers to launching my own website." — Maria G., Journey Platform participant

## Audit Status
This report has not been independently audited. Figures are internally verified.
```

- [ ] **Step 2: Smoke test — grant-library add**

Invoke `/grant-library add tests/sample-doc.md`

Verify:
- `knowledge-base/docs/sample-doc.md` is created with correct frontmatter (type: annual-report, confidence: MEDIUM since not audited)
- `knowledge-base/INDEX.md` has a new row for sample-doc.md
- Output shows confirmation message with document count = 1
- The summary file contains the 347 participant figure with "[page 12]" reference
- The summary file sets confidence to MEDIUM (not audited per document's own note)
- The verbatim quote from page 24 appears in Notable Quotes

- [ ] **Step 3: Smoke test — grant-library list**

Invoke `/grant-library list`

Verify:
- Shows 1 document in the table
- Shows sample-doc.md with type annual-report and MEDIUM confidence

- [ ] **Step 4: Smoke test — grant-library status**

Invoke `/grant-library status`

Verify it correctly identifies:
- AVAILABLE: participant count, BIPOC percentage, completion rate (from sample-doc.md)
- MISSING: 501(c)(3) documentation, letters of support, audited financials, longitudinal outcomes

- [ ] **Step 5: Smoke test — grant-draft**

Invoke `/grant-draft` with `tests/sample-rfp.md`

Run through the workflow and verify each stage:

**Pre-flight:** Passes (1 document in knowledge base)

**Miguel output must include:**
- Deadline: 2026-09-30 17:00 EST
- 8 mandatory sections
- 4 mandatory attachments (IRS letter, audited financials, board list, 2 support letters)
- 4 evaluation criteria with correct weights (Org Capacity 30%, Program Design 35%,
  Target Population 20%, Sustainability 15%)

**Human Gate must show:**
- At least 3 COVERED items (participant count, completion rate, demographics)
- At least 2 MISSING items (audited financials, letters of support)
- User chooses A to continue

**Maria output must:**
- Include `[Source: sample-doc.md | ...]` tag after every statistic
- Include at least 1 Evidence Gap Notice for audited financials (mandatory attachment)
- Use "participants" not "beneficiaries"
- NOT contain any number without a citation tag

**Voice output must:**
- Preserve all citation tags unchanged
- Preserve all Evidence Gap Notices unchanged
- Show VOICE NOTES section

**Mauricio output must:**
- Include Quality Report with a score in each category
- Show WARNINGS for uncovered mandatory attachments
- Include Submission Checklist with PENDING items for support letters and audited financials
- Show deadline and time remaining

**Final draft must:**
- Contain zero factual claims without citation tags (verify manually by scanning the text)

- [ ] **Step 6: Commit**

```bash
git add tests/sample-doc.md
git commit -m "test: add sample documents for smoke testing grant assistant workflow"
```

---

## Appendix: Registering the Plugin

After all files are created, the plugin needs to be discoverable by Claude Code.
In `.claude/settings.json`, verify that plugin discovery is enabled or the plugin path
is registered. If not, run `/update-config` and add the plugin path.

The user-facing skills will then be available as:
- `/grant-library` — Librarian
- `/grant-draft` — Chaski orchestrator

Internal skills are not user-facing and are invoked only by Chaski via the Skill tool.
