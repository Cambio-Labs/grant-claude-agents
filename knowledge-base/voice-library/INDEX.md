# Voice Library — Router and Manifest

Last updated: 2026-08-12

Real Cambio Labs writing and talks, kept so drafts sound like Cambio Labs instead of like generic AI
prose. `knowledge-base/voice-profile.md` states the voice as *rules*; this library holds the
*examples*. Prose can satisfy every rule in the profile and still sound like nobody.

**This library is never evidence.** Nothing here may be cited as the source of a factual claim, even
when an excerpt contains a statistic or a story. It governs how something is written, never what is
asserted. Facts come from `knowledge-base/docs/` with a confidence level attached.

## Read `audit/` first, every time — it isn't row-gated

`audit/voice-audit-guide.md` is Cambio Labs' **own** voice guide, built by the team from a real audit
of 50 pieces of actual outbound copy (`audit/sample-database.md`). It is short, rule-shaped, and
addresses AI agents directly — including Claude Code by name — in its Part 3. **It is authoritative.**
Where anything else in this library conflicts with it, it wins. See Precedence below.

Unlike the router rows, the audit guide applies to *everything* — read it before drafting regardless
of which row you match. The row system below still matters for choosing which long-form profile card
and source to load, and for finding the closest real example in the sample database.

---

## Router — which references to load

Find the row matching what is being written. Load the audit guide (above) plus **only** that row's
profile card and sources — not the whole library.

| # | Writing this | Profile | Load these sources |
|---|---|---|---|
| 1 | Investor pitch, accelerator application, partner deck, product vision | `profiles/investor-pitch.md` | `sources/pitch-script-v3.md`, `sources/anew-final-pitch.md`, `sources/sparky-ai-tech-vision.md` — **all three indexed** |
| 2 | Grant proposal, LOI, funder questionnaire | `profiles/values-manifesto.md` **plus `voice-profile.md`, which wins on any conflict** | `sources/modern-humanist-manifesto.md`, `sources/cec-commission-presentation.md` |
| 3 | Donor email, major-gift ask, board or advisor update | `profiles/spoken-civic.md` | `sources/advisory-board-feb-2026.md` — indexed; `sources/hudson-guild-speech.md` still pending — see caution ⚠3 |
| 4 | Participant outreach, community and NYCHA resident communication, onboarding | `profiles/spoken-explanatory.md` | `sources/intro-to-cambio-labs.md`, `sources/harlem-workshop.md`, `sources/hudson-guild-speech.md` |
| 5 | Social post, campaign copy, newsletter | `profiles/creative-narrative.md` | `sources/sparky-ai-tech-vision.md` — indexed; `sources/dissident-introduction.md` — indexed — see caution ⚠5 |
| 6 | Speech, public remarks, testimony, award acceptance | `profiles/spoken-civic.md` | `sources/hudson-guild-speech.md`, `sources/cec-commission-presentation.md` — **neither indexed yet; weakest row in the router** |
| 7 | Partner or institutional letter (city agency, school district, NYCHA, elected body) | `profiles/spoken-explanatory.md` | `sources/cec-commission-presentation.md` — pending; `sources/advisory-board-feb-2026.md` — indexed, but written for an insider board, not an outside institution; use with care |
| 8 | Values statement, mission framing, op-ed, about-us copy | `profiles/values-manifesto.md` | `sources/modern-humanist-manifesto.md`, `sources/dissident-introduction.md` |

**Also check `audit/sample-database.md`** for the row's channel — it's real, short-form, actual
outbound copy, which is often a closer match than the long-form profile cards:

| Router row | Closest sample-database section |
|---|---|
| 1 — Investor pitch, accelerator, partner deck | Internal Docs (rows 1–8) |
| 2 — Grant proposal, LOI, funder questionnaire | Internal Docs (rows 1–8), Emails (rows 23–30) |
| 3 — Donor email, major-gift ask, board update | Emails (rows 23–30) |
| 4 — Participant outreach, NYCHA resident communication | Website (rows 38–39, 42–45) |
| 5 — Social post, campaign copy, newsletter | Social (rows 17–22) |
| 6 — Speech, testimony, award acceptance | Internal Docs (row 6, candid reflective register) |
| 7 — Partner or institutional letter | Website (row 46, board bio), Internal Docs (rows 1–8) |
| 8 — Values statement, mission framing, about-us | Website (rows 31, 35, 47, 48) |

**If no row matches, ask.** Do not guess a register.

**If two rows match** — one on format, one on audience — take the **format** row as primary; it
determines shape, length, and structure. Read the audience row's profile card as a secondary
constraint on register and vocabulary. Name both rows in the output. This comes up most often with
NYCHA-adjacent writing, where three rows are in play and they are genuinely different jobs: row 4 is
writing *to residents*, row 7 is writing *to the agency*, and a social post recruiting residents is
row 5 primary with row 4 as the register check.

### Row cautions

**⚠3 — Donor email now has a real reference, but only for one register.**
`profiles/spoken-civic.md` is `established`, backed by `advisory-board-feb-2026.md` — a genuinely
strong match for a board update or a major-gift ask to someone who already knows Cambio (candid
numbers, named relationship, unresolved-question closing). It does **not** cover a first-touch or
ceremonial donor note; `hudson-guild-speech.md` (the ceremonial/gratitude register) is still pending.
For a donor thank-you or first ask, treat the card's traits as a starting point and say so.
`modern-humanist-manifesto.md` remains deliberately excluded from this row: its documented moves —
90–110-word catalogue sentences, unresolved suffering vignettes, species-level accusatory "we" — do
not survive contact with a short note to one person.

Related gap: the profile card points at "tier the ask" as one board-update move, but there is no
cost-per-participant or cost-per-microgrant figure anywhere in `knowledge-base/`. That move will
dead-end into a `[NEED: ...]` until someone adds unit-cost data.

**⚠5 — Short-form social now has a real reference for structure, not yet a perfect register match.**
`creative-narrative.md` is `established`, and as of this update both of the row's sources are
indexed: `sparky-ai-tech-vision.md` (short, compressed, product-pitch register) and
`dissident-introduction.md` (long-form, political intensity — its own card warns against borrowing
that intensity for recruitment copy). Neither is literally "a social caption," but `sparky-ai-tech-vision.md`
is now close enough in length and pace to treat as structure-matched for a short post, not just
extrapolated — say so specifically rather than defaulting to "extrapolated" for the whole row.

### When a row's sources aren't available

Several sources are not yet transcribed. Degrade gracefully — never stop, never error:

1. Use whichever sources in the row are `indexed`.
2. If none are, fall back to the profile card's "What to do until then" section and to
   `knowledge-base/voice-profile.md`.
3. **Say so in the output.** Name which references were used and which were unavailable, so the
   reader knows whether the voice was matched or extrapolated.

---

## Audit manifest — read independent of the router

| Source | What it is | Status |
|---|---|---|
| `audit/voice-audit-guide.md` | 8 rules, unified CTA table, AI ALWAYS/NEVER rules | **authoritative**, v1.1 |
| `audit/sample-database.md` | 50 verbatim rows across every channel | in progress — see its Coverage section |

## Source manifest — the row-gated, hand-built profile cards

| Source | Medium | Register | Status |
|---|---|---|---|
| `sources/pitch-script-v3.md` | written-for-speaking | investor / pitch | indexed |
| `sources/modern-humanist-manifesto.md` | written | values / political | indexed |
| `sources/dissident-introduction.md` | written | creative / narrative | indexed |
| `sources/sparky-ai-tech-vision.md` | spoken | product / vision | indexed |
| `sources/advisory-board-feb-2026.md` | spoken | board / strategy | indexed |
| `sources/anew-final-pitch.md` | spoken | investor / pitch | indexed |
| `sources/intro-to-cambio-labs.md` | spoken | explanatory | awaiting transcript |
| `sources/hudson-guild-speech.md` | spoken | civic / ceremonial | awaiting transcript |
| `sources/cec-commission-presentation.md` | spoken | institutional / testimony | awaiting transcript |
| `sources/harlem-workshop.md` | spoken | community / facilitation | blocked — video is private |

**6 of 10 indexed.** All three written sources and three of seven spoken sources are in — the
investor-pitch row (1) is now fully backed, and the donor-email and social rows have real reference
for the first time. Still pending: `intro-to-cambio-labs.md` (the baseline explanatory register that
row 4, participant outreach, most needs), `hudson-guild-speech.md` (ceremonial/gratitude — needed by
rows 3, 4, and 6), and `cec-commission-presentation.md` (institutional testimony — needed by rows 2,
6, and 7). **Row 6 (speech, testimony, award acceptance) has zero indexed sources and is the weakest
row in the router.**

## Profile cards

| Profile | Status | Scope | Backed by |
|---|---|---|---|
| `profiles/investor-pitch.md` | established | full — 3/3 sources indexed | `pitch-script-v3.md`, `anew-final-pitch.md`, `sparky-ai-tech-vision.md` |
| `profiles/values-manifesto.md` | established | full | `modern-humanist-manifesto.md` |
| `profiles/creative-narrative.md` | established | long-form-derived; a short source now also indexed — see ⚠5 | `dissident-introduction.md` |
| `profiles/spoken-civic.md` | established | **board-strategy register only** — see ⚠3 | `advisory-board-feb-2026.md` |
| `profiles/spoken-explanatory.md` | pending sources | — | — |

`status: established` is not a clearance on its own. Check `scope:` too — a card can have real traits
and still be the wrong reference for a given piece.

---

## Adding to this library

```
/grant-library add-voice [filepath or pasted transcript]
```

Placeholders already exist for every pending source — that command fills one in and flips its status
rather than creating a duplicate.

To also check whether a real, published document is strong enough evidence to propose a new audit
rule, a rule revision, or a new voice style (never applied automatically — always a draft for a human
to review), use:

```
/cambio-voice add [filepath or pasted content]
```

See `audit/proposed-changes.md` for anything drafted this way.

## Precedence

These govern different things rather than simply outranking each other:

| Question | Authority |
|---|---|
| Reader-facing rules: addressing the reader, one CTA per piece, naming gaps plainly, fabrication bans, reading level | `audit/voice-audit-guide.md` — **authoritative, overrides everything below on conflict** |
| Which word do I use for the people we serve, and which register applies (community vs. funder-facing) | `voice-profile.md` terminology tables — absolute within the audience they govern; the funder-facing table is itself sourced from the audit guide's Rule 08 |
| Inclusive language, evidence-based posture, not pleading | `voice-profile.md` — absolute |
| A real, closest-match example for this exact channel | `audit/sample-database.md` — prefer this over the profile cards when a row matches |
| Sentence length, rhythm, intensity, structure | The matched **profile card**, for structure the sample database doesn't cover. `voice-profile.md`'s numeric settings are written for funder-facing prose; outside grant work they're a default to depart from deliberately. See that file's Scope section. |
| Concrete phrasing and construction | The **source excerpts** in `sources/` |
| Any factual claim | None of the above — `knowledge-base/docs/` only |

One exception: for **row 2 (grant proposals, LOIs, funder questionnaires)**, `voice-profile.md`
governs rhythm too, in full. `grant-voice` applies it that way.

Examples never override the terminology table, and they never supply a fact.
