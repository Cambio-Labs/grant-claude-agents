---
name: grant-library
description: /grant-library - Manage the grant knowledge base; index and validate documents before running /grant-draft
---

# Grant Library — The Librarian

Manage the grant assistant knowledge base. Run this before `/grant-draft` to ensure
documents are indexed and available for citation.

All paths in this skill are relative to the project root — the directory that contains
the `knowledge-base/` folder and `.claude/` directory — except this plugin bundles its own copy of `knowledge-base/` inside its own directory, so every `knowledge-base/...` path below is relative to `${CLAUDE_PLUGIN_ROOT}`, not the current project. Documents added here with `/grant-library add` are written into this install's own bundled copy — they stay local to this install and are not synced back to Cambio's shared source repo.

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
   - Up to 5 verbatim quotes that are representative of the document's content or directly
     support quantitative claims (not chosen for narrative appeal — chosen for accuracy)
     Include page/section references for each quote.
   - Data caveats or limitations explicitly noted in the document

4. Assign base confidence:
   - HIGH — audited financial document, formal annual report, government-issued document
   - MEDIUM — internal program report, survey data, self-reported outcomes
   - LOW — anecdotal, estimated, or undated document

5. Generate filename slug:
   - Use original filename if from a file path: strip extension, lowercase, replace spaces
     and special characters with hyphens (e.g. `2024 Annual Report (FINAL).pdf` → `2024-annual-report-final`)
   - If pasted: use `pasted-[type]-[YYYY-MM-DD]`
   - Check if `${CLAUDE_PLUGIN_ROOT}/knowledge-base/docs/<slug>.md` already exists. If it does, ask the user:
     "A document with slug `<slug>` is already indexed. Overwrite it, or create a new entry
     with suffix `-v2`?"

6. Write summary to `${CLAUDE_PLUGIN_ROOT}/knowledge-base/docs/<slug>.md` using this exact format:

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

7. Update `${CLAUDE_PLUGIN_ROOT}/knowledge-base/INDEX.md`:
   - If `${CLAUDE_PLUGIN_ROOT}/knowledge-base/INDEX.md` does not exist, create it with this header before adding the row:
     ```
     # Knowledge Base Index
     Last updated: [today's date]

     | File | Type | Date Range | Confidence | Key Metrics |
     |---|---|---|---|---|

     ## Summary
     - Total documents indexed: 0
     - Coverage gaps: Run `/grant-library status` for analysis
     ```
   - If re-adding (slug already existed): update the existing row rather than adding a duplicate
   - Add or update row first: `| <slug>.md | <type> | <date-range> | <confidence> | <2-3 key metrics from step 6, verbatim from document> |`
   - Update "Last updated" date to today
   - Then update "Total documents indexed" count by counting all data rows in the table (after the row has been added)

8. Confirm to user:

```
✓ Document indexed: <slug>.md
Type: <type> | Confidence: <level> | Date range: <range>
Key metrics captured: <N>
Notable quotes: <N>

Knowledge base now contains <N> documents.
Run `/grant-library status` to see coverage gaps.
```

Count "N documents" by counting data rows in the updated INDEX.md table.

### HARD RULE
Never write a value in the summary file or INDEX.md row that cannot be directly quoted
or referenced from the document. If a number appears without a clear source section, write
`[unverified — confirm source]`. Never infer, estimate, or extrapolate.

---

## Command: `/grant-library list`

Show all indexed documents.

1. Read `${CLAUDE_PLUGIN_ROOT}/knowledge-base/INDEX.md`
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

1. If `${CLAUDE_PLUGIN_ROOT}/knowledge-base/INDEX.md` does not exist or has no data rows, output:
   ```
   Knowledge base is empty. Run `/grant-library add [filepath]` to add your first document.
   ```
   Stop.

2. Read `${CLAUDE_PLUGIN_ROOT}/knowledge-base/INDEX.md` and all files in `${CLAUDE_PLUGIN_ROOT}/knowledge-base/docs/`

3. For each of the 9 data types below, check the indexed documents and classify:
   - **Available** — at least one document contains this data at HIGH or MEDIUM confidence,
     and the data appears to be current (within 2 years) or is not time-sensitive
   - **Partial** — data exists but is incomplete, outdated (>2 years old), or only at LOW
     confidence; note specifically what is missing or why it is partial
   - **Missing** — no document in the knowledge base contains this data type

   Data types to check:
   - Participant/beneficiary counts (current year)
   - Program completion rates
   - Demographic breakdown (race/ethnicity, age, income)
   - Budget / financial data (current year)
   - Short-term outcome data
   - Longitudinal outcome data (6-12 months post-program)
   - 501(c)(3) verification
   - Letters of support / partnership documentation
   - Board of directors information

4. Use confidence from the individual `${CLAUDE_PLUGIN_ROOT}/knowledge-base/docs/<slug>.md` frontmatter
   (the `confidence:` field), not from the INDEX.md summary row, as the authoritative source.

5. Output:

```
KNOWLEDGE BASE STATUS

Available (can support grant claims):
✓ <data type> — <source file> [<confidence>]

Partial (available but may be incomplete):
⚠ <data type> — <source file> [<confidence>] — <what's missing or why partial>

Missing (common requirement not in knowledge base):
✗ <data type> — add with: /grant-library add <suggested source type>

RECOMMENDATION: Before running /grant-draft, consider adding:
<prioritized list of highest-impact missing documents>
```
