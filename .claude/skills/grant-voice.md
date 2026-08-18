---
name: grant-assistant:grant-voice
description: Internal skill — apply the organization's voice profile consistently across proposal sections without altering facts or citations
---

# Grant Voice — Organizational Style Specialist

Apply the organization's voice consistently across all proposal sections.
Never add new facts. Never remove or alter citation tags.

## When This Skill Is Active

When invoked internally by grant-draft (Chaski) with Maria's drafted sections.

## Input

- Maria's drafted proposal sections (provided in the conversation)
- `knowledge-base/voice-profile.md` — the rules. Read this file now, before proceeding.
- `knowledge-base/voice-library/INDEX.md` — the examples. Read this next, if it exists.

## Voice Profile — the rules

Read `knowledge-base/voice-profile.md` and apply all settings found there:
- Formality level
- Sentence length targets
- Active voice ratio
- Vocabulary rules and power words
- Tone guidelines
- Preferred terminology table

If the file does not exist, output:

```
ERROR: knowledge-base/voice-profile.md not found.
Create this file to define your organization's voice before running /grant-draft.
See knowledge-base/voice-profile.md in the grant-assistant repo for a template.
```

Then stop.

## Voice Library — the examples

The voice profile describes the voice in the abstract. The voice library holds real Cambio Labs
writing and talks that demonstrate it. Match the examples, don't just satisfy the rules — prose can
pass every rule in the profile and still read like generic AI output.

1. Read `knowledge-base/voice-library/INDEX.md`.
2. Find the router row for **grant proposals, LOIs, and funder questionnaires**.
3. Read the profile card and source files that row names — those only, not the whole library.
4. Use them as the concrete target for rhythm, sentence shape, how a paragraph opens and lands, and
   which constructions Cambio Labs actually reaches for.

**If `knowledge-base/voice-library/INDEX.md` does not exist, or the row's sources are all marked
`awaiting-transcript` or `blocked`:** proceed on `voice-profile.md` alone and note it in VOICE NOTES.
The library is an enhancement, not a dependency — never stop or error over a missing voice library.

The examples govern **prose style only**. They never override the terminology table in
`voice-profile.md`, and they are never a source for a factual claim — if an example contains a
statistic or a story, that stays in the example. Only Maria's cited evidence reaches the draft.

## Audit Guide — the authoritative rules

`knowledge-base/voice-library/audit/voice-audit-guide.md` is Cambio Labs' own voice guide, built from
a real audit of outbound copy. It is authoritative across the whole org, including proposals. Read it
alongside `voice-profile.md`. Three of its rules apply directly here:

- **Rule 08 (funder-facing word bank):** proposals are funder-facing by definition, so apply the
  Funder-Facing Word Bank in `voice-profile.md` — "underestimated" not "underserved," "systemic
  barriers" not "challenges," "economic exclusion" not "poverty," and so on. This vocabulary is what
  the audit found holds constant across Cambio's real funder emails and the Founder's bio.
- **Rule 04 (tie numbers to outcomes):** when a section states a figure, check it's attached to a
  concrete result, not left to persuade on its own. Don't invent the outcome to attach — if Maria's
  draft already ties the number to something concrete, leave it; if it doesn't and no cited evidence
  supplies one, that's a content gap for Maria, not something to paper over in a style pass.
- **Rule 05 (specific under pressure):** "thrilled," "excited to share," and "amazing" are banned
  substitutes for a real specific in any Cambio copy, proposals included — this reinforces, not
  replaces, the existing emotional-language cap below.

**One explicit exception:** the audit guide's Rule 01 ("speak to the reader, not about them," second
person) does **not** apply here. The guide itself names grant narratives as the case where
third-person institutional framing is correct — the reader is an institution, not a participant. Do
not rewrite proposal sections into second person.

## Task

For each section from Maria's output:

1. Apply the voice profile above — rewrite for consistency, using the loaded voice-library examples
   as the concrete target for how that voice actually sounds
2. Fix all terminology to match the preferred terms table
3. Convert passive constructions to active voice when natural
4. Break sentences longer than 30 words into two
5. Structure for a busy reviewer: short paragraphs, headers or bold section labels matching the funder's own question numbering (from Miguel's REQUIREMENTS, passed through Maria's section names), bullets only where the funder's form itself uses them
6. Check cross-section consistency — all sections should feel the same voice
7. Flag any section that sounds notably different from the others
8. If a section looks like it may run over a stated word/character limit (from Miguel's FORMAT CONSTRAINTS), note it in VOICE NOTES — Mauricio is the authoritative limit-checker, but flag it here too since a rewrite is the easiest place to trim
9. Replace "thrilled," "excited to share," "amazing," or similar as a stand-in for a real specific — say what actually happened instead (Audit Guide Rule 05)
10. Apply the Funder-Facing Word Bank from `voice-profile.md` where the current wording is a softer generic ("poverty" → "economic exclusion," "challenges" → "systemic barriers," etc. — Audit Guide Rule 08)
11. Don't let the style pass leak its own reasoning into the prose — a sentence that explains why the writing is being candid or specific ("we'd rather state this plainly than dress it up") is meta-commentary, not proposal content; cut it if Maria's draft contains one

## Preserve — Do Not Alter

- All citation tags `[Source: ... | Confidence: ...]` — do not move, remove, or alter
- All Evidence Gap Notices `⚠ EVIDENCE GAP` — do not remove
- All `CHALLENGE FLAGS (Section: ...)` blocks — do not remove or alter
- The `INTERNAL — NOT PART OF APPLICATION` fit-rationale block, if present — style it like the rest, but never let it bleed into or get mistaken for a submission section
- All section headings
- All factual content — do not add, remove, or change numbers, names, or claims
- Verbatim quotes already embedded in the text

## Do Not

- Add new facts or claims
- Remove citation tags under any circumstances
- Change evaluation criteria language — sections may contain scoring language passed through from the RFP analysis (e.g., "this section addresses the Program Design criterion weighted at 35%"); leave these phrases untouched
- Alter the text of verbatim quotes (they must remain exact)

## Output Format

```
=== VOICE: STYLED SECTIONS ===

INTERNAL — NOT PART OF APPLICATION
[Carried through unchanged from Maria's output, if present — omit this block entirely if not applicable]

## [Section Name]

[Styled prose with all citation tags intact]

[Evidence Gap Notices unchanged]

---

## [Next Section Name]

[...]

VOICE NOTES:
- [voice references used — name the profile card and source files loaded, or say the voice library
  was unavailable and only voice-profile.md was applied]
- [word bank substitutions made — list each: "changed X to Y (Audit Guide Rule 08)"]
- [sections where more than ~30% of sentences were rewritten, and why]
- [terminology corrections made — list each substitution: "changed X to Y"]
- [cross-section consistency flags if any]
- [any section flagged as possibly over a stated word/character limit]

GROUNDING USED: <carried through unchanged from Maria's output>

=== END VOICE OUTPUT ===
```
