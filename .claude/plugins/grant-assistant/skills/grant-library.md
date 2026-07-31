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

```
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
```

7. Update `knowledge-base/INDEX.md`:
   - Add row to the table: `| <slug>.md | <type> | <date-range> | <confidence> | <2-3 key metrics> |`
   - Update "Last updated" date to today
   - Update "Total documents indexed" count

8. Confirm to user:

```
✓ Document indexed: <slug>.md
Type: <type> | Confidence: <level> | Date range: <range>
Key metrics captured: <N>
Notable quotes: <N>

Knowledge base now contains <N> documents.
Run `/grant-library status` to see coverage gaps.
```

### HARD RULE
Never write a value in the summary file that cannot be directly quoted or referenced
from the document. If a number appears without a clear source section, write
`[unverified — confirm source]`. Never infer, estimate, or extrapolate.

---

## Command: `/grant-library list`

Show all indexed documents.

1. Read `knowledge-base/INDEX.md`
2. If file does not exist or table is empty (no data rows), output:
   ```
   Knowledge base is empty.
   Run `/grant-library add [filepath]` to add your first document.
   ```
3. Otherwise output:
   ```
   KNOWLEDGE BASE — <N> documents indexed
   Last updated: <date>

   <Table from INDEX.md rendered clearly>

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

```
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

---

## After creating the file, commit:

```bash
git add .claude/plugins/grant-assistant/skills/grant-library.md
git commit -m "feat: add grant-library (Librarian) skill for knowledge base management"
```

## Context

Working directory: `/Users/tranv/development/intern/cambio-lab-26/grant-claude-agents`

This is Task 2 of 8 for the Cambio Labs grant-assistant plugin. The plugin's core design principle is anti-hallucination: Claude may only include facts that are directly traceable to source documents. This skill is the gateway — it indexes documents into `knowledge-base/docs/` as structured `.md` summaries so that the drafting agents (Miguel, Maria, Voice Waxer, Mauricio) can cite them precisely.

Task 1 already created the directory structure. The skills directory already exists at `.claude/plugins/grant-assistant/skills/`.

## Your Job

Write the skill file with the exact content specified above. Do not add, remove, or paraphrase any section. The content IS the implementation — get it exactly right. After creating, verify line count and commit.

## Report Format

- **Status:** DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT
- Files created
- Line count
- Commit SHA
- Any concerns
