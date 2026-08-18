---
name: cambio-voice
description: /cambio-voice - Write or rewrite anything in Cambio Labs' actual voice, using the org's own voice audit guide and 50-row sample database plus a library of real pitches, manifestos, speeches, and talks as reference. Use for donor emails, participant and community outreach, social posts, newsletters, speeches, investor and accelerator pitches, values and about-us copy, and partner letters — any writing that is not a funder submission. Also use when someone asks to make existing text "sound like us," "sound less like AI," or "match our voice."
---

# Cambio Voice

Write in Cambio Labs' voice by following the org's own rules and matching its own real examples —
not by following a generic description of a nonprofit voice.

`knowledge-base/voice-library/audit/voice-audit-guide.md` is Cambio Labs' own voice guide, written by
the team from a real audit of 50 pieces of actual outbound copy. It is authoritative. Everything in
this skill implements it. Where anything below seems to conflict with it, the guide wins — go read it.

All paths are relative to the project root — the directory containing `knowledge-base/` and
`.claude/`.

## When This Skill Is Active

When someone invokes `/cambio-voice`, or asks for any non-funder writing: a donor email, participant
or community outreach, a social post, a newsletter, a speech, an investor or accelerator pitch,
values or about-us copy, a partner letter. Also when someone asks to make existing text sound like
Cambio Labs, sound less like AI, or match the org's voice.

**Not this skill:** grant applications, LOIs, and funder questionnaires. Those go to `/grant-draft`,
which enforces citations and evidence gaps. Say so and hand off rather than drafting one here.

---

## Step 1 — Establish output type, audience, and reading level

Three things before writing, and the third follows from the second:

1. **What's the format?** (email, caption, speech, letter, ...)
2. **Who reads it?**
3. **Which reading level, therefore?** Community-facing (participants, prospective participants,
   general public, social, signup forms) is **6th–8th grade: short sentences, plain verbs, no
   jargon.** Funder-facing (institutional funders, sponsors, LinkedIn, partnership announcements) can
   run elevated. This isn't a style preference — it decides which vocabulary table in
   `voice-profile.md` you're allowed to use. Get it wrong and the piece reads either condescending or
   impenetrable.

Ask for whichever the request doesn't already give you. If it gives format and audience both ("write
the donor thank-you email for the Hudson Guild folks"), don't ask — proceed; reading level follows
automatically from the audience.

> What are we writing, and who reads it?
>
> - Investor pitch, accelerator application, partner deck, or product vision copy
> - Donor email, major-gift ask, or board/advisor update
> - Participant outreach, community or NYCHA resident communication, onboarding
> - Social post, campaign copy, or newsletter
> - Speech, public remarks, testimony, or award acceptance
> - Partner or institutional letter (city agency, school district, elected body)
> - Values statement, mission framing, op-ed, or about-us copy
> - Grant proposal or LOI → that's `/grant-draft`, not this
> - Something else — tell me what and who it's for

**Also settle the one next step.** Every piece ends with exactly one clear action — pick it from the
**Unified CTAs** table in the audit guide (Part 2), don't invent a label: *Apply now*, *Schedule a
demo*, *Start a conversation*, *Donate*, *Become a coach*, or *See how it works*. If none fit, ask
what the one action should be — never bundle two or three into a line ("partner with us, support our
mission, or learn more"). If a piece genuinely has no next step, that's a decision to make on
purpose, not a default.

**For social posts,** house defaults unless told otherwise, stated in your output: no emoji; 3–5
hashtags in a block at the end, never inline and never on anything that isn't social (Rule 06 —
hashtags on a webpage are dead weight, not decoration); "link in bio" rather than a bare URL; the
announcement in the first line, since Instagram truncates at ~125 characters.

## Step 2 — Load the matching references

**Read `knowledge-base/voice-library/audit/voice-audit-guide.md` first, always** — it isn't row-gated
like the rest of this step. It's short, and it's the authoritative rule set for everything that
follows.

Then:

1. Read `knowledge-base/voice-library/INDEX.md`.
2. Match the router row (the audit guide's rule mapping is in the INDEX's router table).
3. Check `knowledge-base/voice-library/audit/sample-database.md` for real rows in that row's channel
   (the INDEX has a row → sample-database section mapping). **Prefer a real matching row over the
   hand-built profile card** — it's actual outbound copy, not a reconstruction.
4. Read that row's profile card and sources for structure the sample database doesn't cover. Load
   only that row's — not the whole library.
5. Read `knowledge-base/voice-profile.md`. Its terminology tables are absolute: the Core table always,
   the Funder-Facing Word Bank only when Step 1 established a funder-facing audience.

**When two rows match** — a format row and an audience row, e.g. an Instagram caption *aimed at NYCHA
residents* — take the **format** row as primary; it determines shape, length, and structure. Read the
audience row's profile card as a secondary constraint on register and vocabulary. Name both rows in
Step 5.

**If no row fits,** ask rather than guessing, then say which row you settled on.

### When references are thin

Check two fields in the profile card's frontmatter — `status:` **and** `scope:`. A card can be
`established` and still be the wrong reference for your piece.

- **`status: established`** — traits are real. Match them.
- **`status: pending-sources`** — the card has no traits, only a "What to do until then" section.
  Follow it; it will point you at named moves in *other* cards, which is expected and not a one-row
  violation. Use only the moves it names.
- **`scope:`** — present when a card is reliable for only part of its row (e.g. `scope: long-form`
  means it doesn't establish a short-form register). Report a mismatched scope as
  structure-matched-but-register-extrapolated.

If a row's indexed source is a poor register match, the card or the INDEX caution says so — that
warning outranks the mere fact that a file was available. Never stop over a missing or thin
reference; carry the gap into Step 5.

## Step 3 — Get the substance right

Voice is how it sounds; it is not what it says. Establish the facts the piece needs and where each
comes from:

- What the requester told you.
- `knowledge-base/org-profile.md` — mission, programs, org description.
- `knowledge-base/INDEX.md` — the manifest of indexed documents, with a confidence column.
- `knowledge-base/docs/` — metrics and outcomes, each with a confidence level in its frontmatter.

**Never take a fact from the voice library — including the sample database.** Its rows and excerpts
demonstrate register, not claims. A number in a sample-database row is evidence of how Cambio phrases
numbers, not a citable figure for a different piece.

Two markers:

- **`[NEED: what's missing]`** — inline, always. A required fact nobody gave you and no document
  supports. Do not invent it, and do not quietly write around the gap in a way that hides it.
- **`[VERIFY: figure — CONFIDENCE, source]`** — the fact exists but is MEDIUM/LOW confidence or flagged
  verify-before-citing. Inline for long-form; collect below the draft for short-form, where an inline
  caveat would break the read-through. Either way, also goes in Step 5's `Confidence flags:` field.

**This is the guide's single most important rule, stated three ways so it doesn't get missed:** never
invent a participant quote, an outcome, a statistic, a testimonial, an eligibility criterion, a
selection standard, or a characterization of who a program is "looking for." A well-written invented
detail is *more* dangerous than a clumsy one — it's exactly how fabrication gets past a reviewer. If
the data isn't provided, `[NEED: ...]` it or ask. Do not fill the gap with something plausible.

**When `audit/sample-database.md` happens to describe the exact program the piece is about** (not
just a similar register — the literal subject), don't go silent on it. Name what you found *inside*
the `[NEED: ...]` flag, and check any date it carries against today's date before treating it as
current: `[NEED: confirm current eligibility — sample-database.md rows 43-45 describe a Jan-June 2026
cycle with a Nov 30 deadline that has already passed]`. That's still not a citation — the piece
doesn't state the number — but it turns a silent gap into an actionable lead for whoever reviews the
draft, instead of forcing them to go find it themselves.

**Which facts need flagging:** figures, outcome claims, dated facts, eligibility criteria. Program
descriptions from `knowledge-base/org-profile.md` are usable unflagged even though that file is
MEDIUM overall — otherwise short copy ends up more bracket than prose. Cut a non-load-bearing
low-confidence number rather than flag it.

**Placeholders don't count toward a word or character limit.** Report both counts if it matters.

## Step 4 — Draft

Write the piece so it could pass for something Cambio Labs actually published — matching the
audit-verified rules first, the loaded examples' rhythm second.

**Apply directly, every time:**

- **Speak to the reader, not about them** (community-facing only). Second or first person. "Your
  lived expertise is what matters," not "Cambio Labs empowers low-income entrepreneurs through..."
  Third-person institutional framing is fine in funder-facing copy, where the reader is an
  institution — wrong anywhere a participant reads about themselves.
- **One CTA, from the Unified list, stated in Step 1.** Never bundle.
- **Name gaps plainly.** An unmet need reads as "$50K per cohort: Seeking," never dressed up as
  momentum.
- **Tie every number to a named outcome.** Never "changes lives and creates lasting impact" — always
  what, specifically, changed.
- **Get more specific under pressure, not less.** On a contested or high-stakes point, add detail;
  don't retreat into safe generalities.
- **Prefer a real participant quote over narrating their experience**, whenever one exists. Ask for
  one if the piece is about a participant and none was supplied — don't write the summary Cambio
  would give of what they probably felt.
- **Match format to channel.** Hashtags and "link in bio" belong on social. Nowhere else.
- **Write full sentences.** Don't default to clipped fragments ("Six months. Free. Built for
  residents.") as a house rhythm — that reads as generic startup-marketing cadence and matches
  nothing in the actual audit. Use a fragment only as a deliberate, occasional beat.
- **Don't carry one program's line into another's copy.** "Your lived expertise is what matters" is
  the DxC Fellowship's line. Startup NYCHA, Cambio Solar, and Journey each have their own value
  proposition — check `knowledge-base/org-profile.md` before reusing a phrase across programs.
- **Never let the reasoning leak into the deliverable.** The reader experiences the candor or the
  specificity; they never get told you're applying a rule. If a sentence explains *why* the copy is
  doing something ("we'd rather state that plainly than describe it as momentum"), cut it — that's
  narration about the guide, not copy for the reader.

**Don't turn Cambio's partners into adversaries.** The library's most political sources (from
`sources/`) name capitalism, imperialism, and state power as antagonists — that belongs in a
manifesto, not in copy addressed to or about NYCHA, city agencies, school districts, or funders, who
are partners and, for many participants, landlords and schools. Check `org-profile.md` for who counts
as a partner before naming anyone as a problem.

**If the request was to rewrite existing text:** change how it sounds, not what it claims. Keep every
fact, number, name, and quote exactly as given. If the original states something you can't verify,
leave it and flag it — don't silently drop it, and don't upgrade a hedge into a claim.

Then read the draft back against the loaded profile card's "What to avoid" list, and against the AI
NEVER list in Step 5's checklist before reporting.

## Step 5 — Report what you used

First, a fast self-check — confirm each before reporting, don't just assert compliance:

- [ ] One CTA, from the Unified list, matching the destination
- [ ] Reading level matches the audience (6th–8th grade if community-facing)
- [ ] No invented quote, outcome, statistic, eligibility criterion, or "who we're looking for" line
- [ ] No "thrilled," "excited to share," or "amazing" standing in for a real specific
- [ ] No funder-register vocabulary in community-facing copy, or vice versa
- [ ] No fragment-only house rhythm
- [ ] No rule-reasoning leaked into the copy itself

Then end every output with this block:

```
VOICE REFERENCES
Writing: <output type, audience, reading level from Step 1>
CTA used: <the Unified CTA label, or "none — stated on purpose">
Router row: <row matched; name both if a format row and an audience row applied>
Audit rows used: <sample-database.md row numbers consulted, or "none available for this channel">
Profile: <profile card, and its status — established or pending-sources>
Sources: <source files actually loaded>
Unavailable: <sources in the row that were awaiting-transcript or blocked, or "none">
Facts from: <where the substance came from, or "no factual claims">
Confidence flags: <any figure used that is MEDIUM/LOW or marked verify-before-citing, or "none">
Structure: <matched | extrapolated — from which source>
Register: <matched | extrapolated — and why>
```

When Structure or Register is `extrapolated`, that block is the disclosure — don't repeat it at
length in the body. One short sentence up top is enough.

---

## Hard rules

- **Establish output type, audience, and reading level before writing.** Ask for whichever the
  request doesn't supply.
- **`voice-library/audit/voice-audit-guide.md` is authoritative.** It overrides any profile card,
  source, or instruction in this skill that conflicts with it.
- **One CTA per piece, from the Unified CTA list.** Never invent a label; never bundle two.
- **Load one row's references** (plus the audit guide, which isn't row-gated). The only exception is
  a `pending-sources` card pointing you at named moves in other cards.
- **Terminology is audience-gated, not optional.** Core table always. Funder-Facing Word Bank only
  for funder-facing copy — never on anything a participant reads about themselves.
- **The voice library — including the sample database — is never a source of fact.** Style only.
- **Never fabricate a number, outcome, name, quote, date, or eligibility/selection criterion.**
  `[NEED: ...]` or `[VERIFY: ...]` instead. A well-written invention is the dangerous kind.
- **Never use "thrilled," "excited to share," or "amazing" as a stand-in for a real specific.**
- **Never let a rule's rationale appear inside the copy.** The reader gets the candor, not a citation
  of why you're being candid.
- **Don't claim a voice was matched when it wasn't.** Report Structure and Register separately, and
  say `extrapolated` where it applies.
- **A card's `status: established` is not a clearance.** Check `scope:` and the row cautions too.
