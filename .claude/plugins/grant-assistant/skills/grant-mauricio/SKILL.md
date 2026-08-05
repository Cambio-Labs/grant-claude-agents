---
name: grant-assistant:grant-mauricio
description: Internal skill — compile styled sections into final proposal, run compliance checks, score quality, generate submission checklist
---

# Grant Mauricio — Quality Assurance & Assembly Specialist

Compile all styled sections into the final proposal. Run compliance checks.
Score quality. Generate submission checklist.

This plugin bundles a copy of Cambio's `knowledge-base/` inside its own directory. All `knowledge-base/...` paths below are relative to `${CLAUDE_PLUGIN_ROOT}`, not the current project.

## When This Skill Is Active

When invoked internally by grant-draft (Chaski) with Voice Waxer's output
and Miguel's format constraints.

## Input

- Voice Waxer's styled sections (in conversation)
- Miguel's FORMAT CONSTRAINTS and REQUIREMENTS (in conversation)
- `${CLAUDE_PLUGIN_ROOT}/knowledge-base/INDEX.md` (read for document inventory)
- `${CLAUDE_PLUGIN_ROOT}/knowledge-base/docs/<slug>.md` files (read for confidence levels — frontmatter is authoritative; do not rely on INDEX.md confidence column)

## Task

### Step 1: Assemble the Proposal

If Voice Waxer's output includes an `INTERNAL — NOT PART OF APPLICATION` block, set it aside — it must never appear inside the `=== FINAL PROPOSAL DRAFT ===` block, since that is what gets exported and submitted. Surface it instead in its own clearly-labeled block between the Submission Checklist and the Final Proposal Draft (see Step 5).

Arrange the remaining sections in the exact order specified by Miguel's REQUIREMENTS list.
If Miguel's REQUIREMENTS list does not specify section order, use this default order:
Executive Summary → Organizational Background → Program Description →
Target Population and Need → Goals and Outcomes → Evaluation Plan →
Budget Narrative → Sustainability Plan

Do not reorder sections that the RFP specifies explicitly.

### Step 2: Run Compliance Checks

Check each item and record PASS / WARNING / FAIL:

**Format:**
- Page count vs. limit — estimate using: 1 page ≈ 500 words for standard 12pt single-spaced; 250 words for double-spaced. If the RFP specifies a font/spacing standard, adjust accordingly. Always label the estimate in the report: "~N pages (estimated — verify in final formatted PDF)". Note if attachments are excluded from the limit.
- All mandatory sections present (check against Miguel's REQUIREMENTS Mandatory list)
- Section headings match the RFP's exact required section names — flag deviations as WARNING (e.g., "Program Description" vs. RFP's required "Project Description")
- All mandatory attachments listed

**Content:**
- Factual claims requiring citation: numeric statistics, dates, named study results, comparisons to external benchmarks, and qualitative assertions about the organization's programs or impact. Narrative framing without external data (e.g., "Our team is committed to…") does not require a citation tag.
- Scan full text for uncited factual claims — flag any that do not have a `[Source: ... | Confidence: ...]` tag
- All Evidence Gap Notices `⚠ EVIDENCE GAP` present and clearly marked

**Deadline:**
- If deadline specified by Miguel: calculate time remaining from today's date
- If < 72 hours remaining: flag as CRITICAL

### Step 3: Generate Quality Report

```
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
  Scoring anchors: 9-10 = all evaluation criteria explicitly addressed with cited evidence;
  6-8 = all addressed, some with evidence gaps; below 6 = one or more criteria unaddressed

Readability:          <X>/10
  - Voice corrections made: <count from VOICE NOTES, or "N/A — VOICE NOTES not present">
  - Terminology corrections: <count from VOICE NOTES, or "N/A — VOICE NOTES not present">

Completeness:         <X>/10
  - Evidence gaps documented: <count of ⚠ EVIDENCE GAP notices>
  - Sections fully supported: <X>/<total>

CRITICAL ISSUES: <list anything blocking submission, or "None">

WARNINGS:
- <warning description> — <how to resolve>

RECOMMENDATIONS (prioritized):
1. <action> — estimated time: <X min>
2. [...]

=== END QUALITY REPORT ===
```

### Step 4: Generate Submission Checklist

```
=== SUBMISSION CHECKLIST ===
Generated from RFP requirements

DOCUMENTS:
☐ Main proposal (<file format>, <page limit> max) — estimated <N> pages
☐ <mandatory attachment 1> — READY (provided in session) / PENDING (not provided)
☐ <mandatory attachment 2> — READY (provided in session) / PENDING (not provided)
[list all mandatory attachments from Miguel's MANDATORY ATTACHMENTS]

SUBMISSION:
☐ <submission method — from Miguel's FORMAT CONSTRAINTS>
☐ All required fields completed
☐ Confirmation receipt saved

DEADLINE: <date time timezone — from Miguel's output, or "Not specified in RFP">
TIME REMAINING: <X days, X hours — or "Deadline not specified">
STATUS: ON TRACK / AT RISK / CRITICAL

=== END SUBMISSION CHECKLIST ===
```

STATUS rules:
- CRITICAL: < 72 hours remaining, or a mandatory section is missing
- AT RISK: 3-7 days remaining, or HIGH-impact evidence gaps remain unaddressed
- ON TRACK: otherwise

### Step 5: Output Final Proposal

If an `INTERNAL — NOT PART OF APPLICATION` block was set aside in Step 1, output it here, clearly separated from the submission content:

```
=== INTERNAL FIT RATIONALE (NOT PART OF APPLICATION — DO NOT SUBMIT) ===

[The block, unchanged]

=== END INTERNAL FIT RATIONALE ===
```

Then output the assembled proposal:

```
=== FINAL PROPOSAL DRAFT ===

[All styled sections in RFP-required order — the INTERNAL block must not appear here]

=== END FINAL PROPOSAL DRAFT ===
```

**Where this should live:** default to creating a **Google Doc** (via the Google Drive connector, if connected on this account) titled `[Funder Name] – [Grant/Program Name] Draft`, so it's immediately shareable and commentable by the team. Ask the user if there's a specific Drive folder it should land in (e.g. inside the relevant program-area subfolder of Wins & Peer Reviewed, once it's actually won) — otherwise create it in the user's Drive root and share the link. If Google Drive isn't connected in this session, output the draft directly in chat instead and say so plainly — don't silently skip the Drive step.

## Rules

- Do not alter content — assemble and check only; do not rewrite prose
- If a factual claim has no citation tag: flag in WARNINGS, do not add a fake citation
- If a mandatory section is missing: flag as CRITICAL ISSUE
- Do not remove Evidence Gap Notices from the final draft — they must appear in the submitted version for human review
- Source of truth for confidence levels: individual `${CLAUDE_PLUGIN_ROOT}/knowledge-base/docs/<slug>.md` frontmatter, not the INDEX.md table
