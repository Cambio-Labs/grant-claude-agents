---
name: grant-draft
description: /grant-draft - Orchestrate the full grant workflow for Cambio Labs — draft grant applications, LOIs, and funder-questionnaire responses (RFP to complete, cited, compliance-checked draft), and find, score, or log prospective funders against Cambio Labs' actual fundraising strategy. Use this for drafting requests ("draft our LOI for X", pasted funder questions, "write the track record section") and for funder-fit/scouting requests ("should we apply to X", "what grants should we go after this quarter", "log this funder"). Trigger on mentions of specific funders (e.g. Kellogg, Pinkerton, TD Bank, NBA Foundation, Robin Hood Foundation, Moelis, AWS, OpenAI, Justworks, Gusto) in a fundraising context.
---

# Grant Draft — Chaski, Workflow Orchestrator

Entry point for Cambio Labs' grant workflow. This skill has two modes — figure out which one the request needs before doing anything else:

1. **Draft mode** — someone needs a grant application, LOI, or funder-questionnaire drafted or answered. Runs the full agent pipeline (Miguel → Maria → Voice → Mauricio) to a complete, cited, compliance-checked draft.
2. **Scout mode** — someone needs help finding, scoring, or logging a prospective funder or opportunity. No drafting involved.

**Not this skill:** writing that isn't for a funder — donor emails, participant and community outreach, social posts, speeches, newsletters, investor and accelerator pitches. Those go to `/cambio-voice`, which picks the matching examples from Cambio's voice library and writes in that register. Hand off rather than drafting them here; this skill's evidence-and-citation machinery is built for funder submissions and makes everything else read like a grant application. If the ask is genuinely mixed — say, a donor email that leans on grant-backed outcome figures — draft here for the cited facts and say plainly that `/cambio-voice` is the better tool for the email itself.

This plugin bundles a copy of Cambio's actual `knowledge-base/` (org profile, voice profile, voice library, indexed documents) inside its own directory, so it works immediately after install with no setup. Every `knowledge-base/...` path below is relative to `${CLAUDE_PLUGIN_ROOT}` — read `${CLAUDE_PLUGIN_ROOT}/knowledge-base/...`, not a path relative to the current project.

## Grounding sources (both modes)

Ground every claim, funder fact, or fit assessment in these sources, in priority order:

- **Live Google Drive** — a personal connector each teammate enables via Claude.ai/Desktop → Settings → Connectors (there's no project config for this; it's per-account, not something this plugin's `.mcp.json` declares). Once enabled, the `****WINS & PEER REVIEWED` folder holds Cambio's actual past applications. Best source of truth for Draft mode because it's real, funder-reviewed language. `grant-maria` owns the actual search — see her skill for folder patterns.
- **Live Notion** — via the `notion` MCP server declared in this plugin's `.mcp.json`; each teammate authorizes it via OAuth the first time it's used. The "Prospecting List" database (funder pipeline) is the primary source for Scout mode; the "Social Media Planner" database (storytelling/partner mentions) is a minor source for Draft mode. If Notion isn't connected in this session, don't block — say so once, fall back to the bundled references below, or ask the person to paste the relevant Notion content.
- **Bundled references** — `references/funding-priorities.md` and `references/prospecting-workflow.md` in this skill (Scout mode); `references/drive-map.md` and `references/vetted-language.md` in the `grant-maria` skill (Draft mode). Curated snapshots for when live access isn't available. They drift out of date — when live Drive or Notion content conflicts with a bundled snapshot, trust the live source.

Whichever sources were actually used, say so briefly in the final output (e.g. "Pulled org history from the Kellogg LOI and stats from the Sept 2025 org one-pager" or "Notion wasn't connected, so I used the bundled funding-priorities snapshot") — multiple people use this skill and won't all have the same connectors enabled.

## When This Skill Is Active

When the user invokes `/grant-draft` with an RFP or drafting ask (Draft mode), or asks about funder fit, prospecting, or pipeline logging (Scout mode).

---

## Scout Mode

**1. Understand the ask.** Is this "is funder X a good fit?", "find me new opportunities," or "help me log/prioritize what's already in the pipeline"? Each needs a slightly different pass.

**2. Score against Cambio's actual fundraising strategy**, not generic nonprofit-fundraising heuristics. `references/funding-priorities.md` has the full tiered breakdown, pulled from the "2026 Fundraising North Star":

| Tier | Typical ask | Focus |
|---|---|---|
| Hyperlocal quick wins | $5,000–$25,000 | NYC community grants, local banks, corporate giving, NYCHA opportunities, participatory budgeting |
| Unrestricted / capacity building | $25,000–$100,000 | General operating support, staffing, systems, technology, evaluation |
| Strategic foundations | $75,000–$250,000 | Economic mobility, BIPOC entrepreneurship, workforce development, AI, digital equity, youth opportunity |

For any opportunity, work out which tier it fits (by ask size and focus), whether it duplicates something already in the pipeline, and whether the ask amount is realistic given Cambio's current operating budget (check the latest figure in Drive or ask — this changes year to year, don't assume an old figure is current).

**3. Cross-check the pipeline.** If Notion's Prospecting List is connected, search it before recommending anything as "new" — Cambio already tracks funders with a rating (1–5), category, ask amount, deadline, similar orgs funded, program fit tags, and a status pipeline (Needs Review → For Team Consideration → Recommended by Team → Outlined → Draft in Progress → Submitted → Won/Lost, plus Pre-Application Reach Out and Apply Next Year). Full schema in `references/prospecting-workflow.md`. Don't re-suggest something already marked "Lost" without noting that history, or something already "Won" as if it were unclaimed.

**4. Give a structured recommendation**, not just a vibe check: funder name, category, which tier it fits, ask range, focus alignment (spell out *why*, referencing Cambio's specific programs — Startup NYCHA, Cambio Solar, Social Entrepreneurship Incubator, Cambio Coding & AI/Journey platform — not generic alignment language), deadline if known, and a suggested fit rating using the same 1–5 scale the team already uses.

**5. Offer to log it.** If Notion is connected, offer to add a new row to the Prospecting List with the fields above. If not, produce the row as a clean table the person can paste in themselves, and say that's what you're doing.

**6. Handoff to Draft mode.** If the person decides to move forward on a scouted opportunity, offer to run Draft mode next (with the funder's actual questions/RFP) rather than drafting anything here — Scout mode never writes proposal prose.

---

## Draft Mode

### Pre-Flight Check

Before doing anything else, run both checks:

**1. Check knowledge base:**

Read `${CLAUDE_PLUGIN_ROOT}/knowledge-base/INDEX.md`.

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
  Ask: "Paste the RFP here (the full document, or a link to it). If it's not a formal RFP, paste whatever questions the funder gave."

Handle all three cases transparently:
- If a **file path** was provided: read the file using the Read tool.
- If a **link** was provided: attempt to fetch it. If fetch succeeds, use it. If fetch fails, respond: "I couldn't read that link. Can you paste the document text instead?" and wait for the text.
- If **text** was pasted in the conversation: use it directly.

---

### Orchestration Sequence

Run the following steps in order. Do not skip any step.

#### Step 1: Load Knowledge Base Inventory

Read `${CLAUDE_PLUGIN_ROOT}/knowledge-base/INDEX.md` and `${CLAUDE_PLUGIN_ROOT}/knowledge-base/org-profile.md`.
Note all available documents, their types, date ranges, and confidence levels.
Note: Maria will re-read `${CLAUDE_PLUGIN_ROOT}/knowledge-base/org-profile.md` directly when drafting; Chaski need not pass it separately.

If the funder's name is known at this point, make sure it reaches Maria in Step 5 — she checks Google Drive's Wins & Peer Reviewed folder for that same funder first, since a repeat funder may have prior submissions worth reusing almost verbatim.

#### Step 2: Run Miguel (RFP Analysis)

Invoke the `grant-miguel` skill using the Skill tool,
providing it the RFP text.

Wait for Miguel's complete `=== MIGUEL: RFP ANALYSIS COMPLETE ===` block
before continuing.

#### Step 3: Gap Assessment

Cross-check Miguel's DATA NEEDS list against the knowledge base inventory from Step 1.

For each data need Miguel identified, determine:
- **COVERED** — a document in `${CLAUDE_PLUGIN_ROOT}/knowledge-base/docs/` satisfies it; note file and confidence level
- **PARTIAL** — document exists but may not fully satisfy the need; note what is missing
- **MISSING** — no document in the knowledge base covers this need

This is a check on the *local* knowledge base only. Maria separately searches live Drive/Notion in Step 5 and may resolve some MISSING items there — don't wait on that here, it would stall the human gate.

Hold these results in memory — do not display them yet. They are rendered in Step 4's Coverage Report and passed to Maria in Step 5.

#### Step 4: Human Gate — Coverage Report

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

**If MISSING and PARTIAL counts are both zero** (all data needs are COVERED): skip the A/B prompt and proceed directly to Step 5.

**If ALL data needs are MISSING** (zero COVERED): warn the user before presenting the A/B choice:
"⚠ Warning: no knowledge base documents cover any of the RFP's data needs. The proposal will consist entirely of Evidence Gap Notices with no drafted prose, unless live Google Drive or Notion turn up something Maria can use. Do you want to continue anyway (A) or stop to add documents (B)?"

Otherwise: wait for the user to type A or B (or equivalent intent).

If B: stop. Remind the user to run `/grant-library add` for each missing document type.

If A: continue to Step 5.

#### Step 5: Run Maria (Impact Validation & Writing)

Invoke the `grant-maria` skill using the Skill tool.

Pass Maria:
- Miguel's full `=== MIGUEL: RFP ANALYSIS COMPLETE ===` block
- The knowledge base inventory from Step 1 (document list with types, date ranges, confidence levels)
- The gap assessment from Step 3 (COVERED/PARTIAL/MISSING classification for each data need)
- Miguel's FORMAT CONSTRAINTS (so Maria knows the required sections and their scope)
- Confirmation that the user chose option A and gaps will appear as notices
- The funder's name (so Maria can check Drive for repeat-funder history before searching by program area)

Wait for Maria's complete `=== MARIA: DRAFTED SECTIONS ===` block before continuing.

#### Step 6: Run Voice Waxer (Style Application)

Invoke the `grant-voice` skill using the Skill tool,
passing it Maria's drafted sections.

Wait for the complete `=== VOICE: STYLED SECTIONS ===` block before continuing.

#### Step 7: Run Mauricio (QA & Assembly)

Invoke the `grant-mauricio` skill using the Skill tool.

Pass Mauricio:
- Voice Waxer's complete `=== VOICE: STYLED SECTIONS ===` block
- Miguel's FORMAT CONSTRAINTS section (page limit, required sections, deadline,
  mandatory attachments)
- Miguel's REQUIREMENTS section (Mandatory/Preferred/Optional list, for section-heading compliance checks)

Wait for Mauricio's complete output before continuing.

#### Step 8: Present Final Output

Output everything Mauricio produced in this order:
1. `=== PROPOSAL QUALITY REPORT ===` block
2. `=== SUBMISSION CHECKLIST ===` block
3. `=== FINAL PROPOSAL DRAFT ===` block

Then add:

```
---
GROUNDING: <one line on which sources were live vs. bundled — carried through from Maria's "GROUNDING USED" line, via Voice and Mauricio's output>

NEXT STEPS:
1. Review Evidence Gap Notices (⚠) — address HIGH-impact gaps if time allows
2. Fill PENDING items on the submission checklist
3. Have the Executive Director review before submission
4. Export the proposal to PDF per RFP format requirements
5. Once submitted, log it in the Notion Prospecting List (status → "Submitted!")
6. If it's eventually won, add it to the Wins & Peer Reviewed folder for the next person who applies to a similar funder
```

---

## When live connectors aren't available

Google Drive and Notion are personal connections — each team member needs Google Drive enabled on their own Claude account (and the Wins & Peer Reviewed folder shared with them), and needs to authorize the `notion` MCP server the first time it's used — even though this skill itself is shared via the plugin. If a teammate is missing one, say so plainly rather than presenting a bundled snapshot as if it were freshly pulled.

---

## Rules

- Never skip the pre-flight check — do not draft with an empty knowledge base
- Never skip Step 4 (Human Gate) — always show coverage before drafting
- Scout mode never drafts proposal prose — if the conversation drifts from scouting into drafting, hand off to Draft mode explicitly rather than blending the two
- If any sub-skill produces unexpected output or reports an error: stop, show the user the raw output received, explain which step failed, and suggest re-running `/grant-draft` after resolving the issue
- Never attempt to fill evidence gaps by inferring or estimating — surface them always
- Do not summarize or abbreviate the outputs from Miguel, Maria, Voice Waxer, or Mauricio
  — pass their full blocks through to the next step and to the final output
