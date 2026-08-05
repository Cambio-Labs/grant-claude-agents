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
- `knowledge-base/voice-profile.md` — read this file now to load the voice profile before proceeding

## Voice Profile

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

## Task

For each section from Maria's output:

1. Apply the voice profile above — rewrite for consistency
2. Fix all terminology to match the preferred terms table
3. Convert passive constructions to active voice when natural
4. Break sentences longer than 30 words into two
5. Check cross-section consistency — all sections should feel the same voice
6. Flag any section that sounds notably different from the others

## Preserve — Do Not Alter

- All citation tags `[Source: ... | Confidence: ...]` — do not move, remove, or alter
- All Evidence Gap Notices `⚠ EVIDENCE GAP` — do not remove
- All `CHALLENGE FLAGS (Section: ...)` blocks — do not remove or alter
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

## [Section Name]

[Styled prose with all citation tags intact]

[Evidence Gap Notices unchanged]

---

## [Next Section Name]

[...]

VOICE NOTES:
- [sections where more than ~30% of sentences were rewritten, and why]
- [terminology corrections made — list each substitution: "changed X to Y"]
- [cross-section consistency flags if any]

=== END VOICE OUTPUT ===
```
