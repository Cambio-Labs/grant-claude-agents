---
source: Cambio Labs Voice Audit Guide (cambio_voice_guide.md)
origin: https://app.notion.com/p/cambio-labs/Cambio-Labs-Voice-Audit-Guide-3ac313182a6380a1bfd5fc15c24acfda
version: v1.1
authority: AUTHORITATIVE — supersedes ad-hoc guidance elsewhere in this library on conflict
audience: two readers — a teammate drafting copy, and an AI agent (Claude Code, Sparky) generating it
saved: 2026-08-12
---

# Cambio Labs Voice Audit Guide

**This is the org's own voice guide, written by the team that ran the audit — not something
generated for this plugin.** Part 3 addresses AI agents, including Claude Code, by name. Where
anything elsewhere in `voice-library/` (a profile card, a router row, a caution) conflicts with a
rule below, **this file wins.** See the precedence table in `../INDEX.md`.

It was built from real Cambio Labs copy — see [`sample-database.md`](sample-database.md) for the
50 verbatim rows this guide's Do/Don't examples are drawn from, cited by row number throughout.

v1.1 changelog: Added Rule 08 (ethos word bank). Sharpened Rule 04 to require using existing
specifics. Added AI NEVER rules 7, 8, and 9, and broadened NEVER rule 1 to cover invented criteria.

A linked "Test Rounds/Saved Prompts" subpage exists but did not render additional content beyond
what's captured here as of 2026-08-12 — the guide itself says its appendix (sample database, saved
prompts, before/after) follows once manual test rounds are complete. Re-check that subpage
periodically; it may have real before/after examples once populated.

## How to use this guide

This guide has two readers: a teammate drafting a caption at 9pm, and an AI agent (Claude Code,
Sparky) generating copy on Cambio's behalf. Both should be able to follow it literally. Where a rule
says "do this," it means do this — vague guidance produces vague copy.

Each rule follows the same structure: the rule stated plainly; a Do/Don't with a real audit example;
when it applies (modality and audience); and why, tied to Cambio's ethos or a tension the audit found.

## Source of truth — Cambio's ethos

> "Cambio Labs empowers low-income social entrepreneurs and workers through educational programs,
> learning technology, and by providing access to investment and job opportunities."

Every rule traces to a specific clause of that statement, not the whole thing:

| Clause | Voice commitment |
|---|---|
| "empowers low-income social entrepreneurs and workers" | Address the reader as a capable actor, not a charity case. |
| "through educational programs" | Teach and clarify; never gatekeep with jargon. |
| "learning technology" | Model accessibility; don't show off the tech. |
| "access to investment and job opportunities" | Point to a concrete next step. "Access" is empty if the reader doesn't know what to do. |

## Two kinds of rules

- **[REGISTER]** rules govern how Cambio-the-org sounds in different contexts (formal vs. warm,
  confident vs. candid). Choices about our own voice.
- **[SOURCING]** rules govern whose voice appears at all (Cambio narrating about someone vs. quoting
  them directly). Choices about authority, not tone.

---

## Part 1 — The Rules

### Rule 01 — Speak to the reader, not about them [REGISTER]

Address the reader directly in second or first person. Avoid third-person institutional descriptions
of "the community" or "underserved populations" in copy a community member will actually read.

**Do:** "You do not need to be a business or design expert to apply; your lived expertise is what
matters." (Fellowship page, Row 43)

**Don't:** "Cambio Labs empowers low income social entrepreneurs and workers through educational
programs, learning technology..." (Homepage hero, Row 31)

**When it applies:** All community-facing copy — website program pages, signup forms, Journey UI,
Instagram. Third-person framing is acceptable only in funder decks and grant narratives where the
reader is an institution, not a participant.

**Why:** The ethos says Cambio "empowers... entrepreneurs and workers." You cannot empower someone
you're talking about. Third-person distance turns a capable actor into a case study.

### Rule 02 — Every piece ends with one clear next step [REGISTER]

Each standalone piece of copy names exactly one action the reader can take, and says what happens
when they take it. If a piece has no next step, that is a decision to make on purpose — not an
accident.

**Do:** "Want to see Journey in action? Schedule a live demo to see if it could be a good fit for
your learning community!" (Journey page, Row 36 — links to a real Calendly)

**Don't:** "There are a lot of ways to be part of Cambio Labs, from becoming a coach to providing
perks & rewards for our learners" → [Read More >] (Homepage, Row 33 — "Read More" says nothing about
what happens)

**When it applies:** Everywhere with a CTA — website cards, email closings, social captions, program
pages. Especially critical on buttons and forms.

**Why:** The ethos promises "access to investment and job opportunities." A reader who finishes the
copy and doesn't know what to click has been promised access and denied it. **More than half of all
audited samples had no clear next action — this is the guide's single highest-priority fix.**

Use the **Unified CTAs** in Part 2 — don't invent new CTA labels.

### Rule 03 — Name gaps plainly; don't inflate confidence to hide them [REGISTER]

When Cambio has an unmet need, state it directly. Do not dress a gap up as a success. Transparency
is the voice, even with funders.

**Do:** "Funding to support ongoing programs ($50K per 6-month cohort): Seeking." (Impact Report,
Row 7 — later confirmed in the Mott Haven one-pager, Row 16)

**Don't:** Bury the need inside confident framing ("We're thrilled by the momentum and exploring
exciting expansion opportunities") when the plain fact is you need $50K per cohort.

**When it applies:** Internal docs, impact reports, funder decks, grant narratives. In cold-outreach
emails, lead with the plain need after establishing context — don't open with it.

**Why:** Cambio's internal voice states gaps plainly (Seeking) while its external voice sells
confidence. The candor is the trust-builder with underestimated communities — you don't earn their
trust by over-promising, so don't do it to funders either.

*This is the same instinct `grant-voice` already protects via Evidence Gap Notices — Rule 03 is that
same discipline extended past proposals into every other channel.*

### Rule 04 — Tie every number to a named outcome [REGISTER]

When you cite a dollar amount, headcount, or metric, attach it to a specific, concrete result.
Numbers persuade; vague impact language doesn't. When concrete facts already exist — dates, dollar
figures, neighborhoods, eligibility criteria — include them. Do not summarize them away.

**Do:** "Innovation Partner, $130-200k: Unlock 235 additional learners in Year 1. Fund first
AI-graded course, unlocking 30% of instructor time for mentorship." (Sponsor Opportunities, Row 1)

**Don't:** "Your support changes lives and creates lasting impact in our communities." (Generic —
true of any nonprofit, specific to none.)

**When it applies:** Funder decks, sponsor materials, grant paragraphs, impact reports. Also
strengthens any social post announcing results.

**Why:** The ethos names concrete deliverables ("investment and job opportunities"). Treating funders
and community members as people who deserve real information rather than inspiration is itself the
mission in action.

### Rule 05 — Get more specific under pressure, not less [REGISTER]

When the topic is important or contested, add specificity, don't retreat into safe generalities.
Cambio's most authentic voice names the actual stakes.

**Do:** "The concern about whose values are embedded in AI systems is not a talking point. It lives
in how we design lessons." (Edwin's IG comment, Row 19)

**Don't:** "We're thrilled to share that Cambio Labs is partnering with America On Tech to support
the next evolution of their AI education program." (IG caption, Row 17 — interchangeable with any
org's announcement)

**When it applies:** Announcements, thought-leadership posts, partnership news, "about us" copy —
anywhere the temptation is to sound polished and say nothing.

**Why:** Cambio sounds most like itself when it gets specific. "Thrilled" and "excited" are the sound
of a brand managing itself. Specificity is the sound of an organization telling the truth about why
the work matters.

### Rule 06 — Match the format to the channel [REGISTER]

Never copy-paste text between channels without rewriting for the new context. Social captions,
website body, and emails are different modalities with different conventions. Hashtags,
emoji-strings, and "link in bio" belong on social — not on a webpage.

**Do:** Write a caption for Instagram, then rewrite it (not paste it) if the same message needs to
live on the website.

**Don't:** "Join the movement to transform NYC's entrepreneurial landscape! 🌍💼 #StartupNYCHA
#Entrepreneurship #NYC #SmallBusiness #EconomicOpportunity #PublicHousing #Innovation" (Found
verbatim on the Startup NYCHA webpage, Row 32 — hashtags do nothing there)

**When it applies:** Any time content moves between platforms. Especially watch: social → website,
and email → website.

**Why:** A process gap the audit found — a caption bled into permanent, funder-visible web real
estate untouched. Dead hashtags on a webpage are visual noise that make copy harder to scan, which
violates the ethos clause about "educational programs" (copy should clarify, not clutter).

### Rule 07 — When a participant's words exist, use them — don't narrate over them [SOURCING]

When you have a real quote from a participant, use it instead of describing their experience in
Cambio's voice. Speak for someone only when their own words genuinely aren't available.

**Do:** "I think I grew most in problem solving and technology fluency, being introduced to
Claude.ai, Perplexity, and also figuring out how to help our customers with their issues." (Student
Angel W., IFE 2025 Report, Row 11)

**Don't:** "Their model not only enhances youth confidence and self-expression but also tackles
sustainability in fashion. Judges were impressed by its traction..." (Cambio narrating a venture,
Row 13 — reads like generic accelerator copy)

**When it applies:** Impact reports, testimonials, program recaps, social posts celebrating
participants, grant narratives citing outcomes.

**Why:** A sourcing rule, not a register rule — it's about whose authority the copy carries. The
ethos is about empowering people to become agents. Narrating over a participant's own voice quietly
undoes that. Their words are more credible than ours and more true to the mission.

### Rule 08 — Use Cambio's own vocabulary in funder-facing copy [REGISTER]

*Added in v1.1 after test round 1.*

Cambio has a specific vocabulary for naming what it fights and what it builds. In funder-facing
copy, use those words. Do not substitute plainer synonyms that lose the political precision.

**The word bank:**

| Use | Instead of |
|---|---|
| underestimated (communities, New Yorkers) | underserved, disadvantaged, at-risk |
| systemic barriers | challenges, obstacles |
| economic exclusion | poverty, low income |
| digital inequity | the digital divide, lack of tech access |
| environmental injustice | environmental issues |
| collective ownership / worker cooperatives | jobs, employment |
| agents of change | beneficiaries, recipients |

**Do:** "Cambio exists to confront and dismantle systemic barriers — economic exclusion, digital
inequity, and environmental injustice — by equipping underestimated New Yorkers to become agents of
change in their own communities." (Funder email, Row 23)

**Don't:** Replace that vocabulary with softer generics ("helping disadvantaged residents overcome
challenges"), which describes a charity, not a movement.

**When it applies:** Funder decks, grant narratives, sponsor materials, LinkedIn, partnership
announcements. **Not** community-facing copy — this vocabulary runs above an 8th-grade reading level
and belongs in institutional contexts only. On a signup form, say "free" and "for NYCHA residents,"
not "addressing economic exclusion."

**Why:** This vocabulary survives compression — it appears nearly word-for-word in both the long and
short funder emails (Rows 23, 24) and in the Founder's website bio (Row 47), written months apart.
Language that stable across documents isn't decoration; it's ethos. Dropping it flattens Cambio into
a generic service provider.

*This word bank is now merged into `../../voice-profile.md` as the funder-facing register, alongside
the existing community-facing terminology table. Which one applies depends on the audience, not the
document type — see that file.*

---

## Part 2 — Unified CTAs

The audit found the same actions asked for in different words across channels ("Read More" doing
triple duty; "Partner with us, support our mission, or learn more" bundled in one line, Row 9). One
CTA per intent. Use these labels consistently so every door leads to the same organization.

| Intent | Use this CTA | Not this | Notes |
|---|---|---|---|
| Join a program (community member) | **Apply now** | "Read More," "Learn More," "Get Started" | Follow with deadline if one exists: "Apply now — deadline Nov 30." |
| Explore the product (partner org / school) | **Schedule a demo** | "See it in action," "Contact us" | Link directly to Calendly. Already working well (Row 36). |
| Fund the work (institutional funder) | **Start a conversation** | "Donate," "Support our mission" | For funders, a conversation precedes a check. Link to a real calendar. |
| Give money (individual donor) | **Donate** | "Contribute," "Support us" | Reserve "Donate" for individual giving only, so it doesn't blur with funder asks. |
| Volunteer expertise (coach / mentor) | **Become a coach** | "Give back," "Get involved" | Name the specific role, not the vibe. |
| Learn more (genuinely undecided reader) | **See how it works** | "Read More" | Use only when there's a real explainer behind it — never as a filler default. |

**Usage guidance:**

- **One CTA per piece.** If you're tempted to list three ("partner, support, or learn more"), you
  haven't decided what this piece is for. Pick the primary action.
- **The CTA label and its destination must match.** "Apply now" goes to an application, not a
  general info page.
- **The label survives the click.** If the button says "Apply now," the page it opens says "Apply,"
  and the confirmation says "Application received." Consistent vocabulary is how people learn their
  way around.

---

## Part 3 — AI Rules (for Sparky and Claude Code)

These govern any AI agent generating or speaking copy for Cambio. An agent follows written rules
literally, so these are absolute. **This is the section `/cambio-voice` and `grant-voice` implement
directly.**

### An agent speaking for Cambio must ALWAYS:

1. Address the reader directly ("you," "your business") in community-facing copy. Never describe
   the community in third person to its own face. (Rule 01)
2. End every piece with one clear, real next step, using the unified CTA labels in Part 2. (Rule 02)
3. Write community-facing copy at a **6th–8th grade reading level.** Short sentences, plain verbs, no
   jargon. If a funder-grade word sneaks in ("leverage," "ecosystem," "capacity-building"), replace
   it.
4. Prefer a participant's real quote over a Cambio-voiced description of their experience, whenever
   a quote is available. (Rule 07)
5. Tie any number to a concrete outcome. (Rule 04)
6. Match output format to the requested channel — hashtags only on social, never on web body or
   email body. (Rule 06)

### An agent speaking for Cambio must NEVER:

1. **Never invent participant quotes, outcomes, statistics, or testimonials — or eligibility
   criteria, selection standards, or characterizations of who the program is "looking for."** If the
   data isn't provided, insert a clearly marked placeholder like `[$X per cohort]` and flag it, or
   ask. Do not fabricate. **This is the most important never: fabricated community voices and
   invented criteria both betray the ethos directly.**

   > Surfaced in test round 1: an output invented the line *"If you're the person your neighbors
   > come to when something needs fixing, you're who we're looking for"* — warm, well-written, and a
   > completely fictional selection criterion. Good writing is exactly how invented facts get past a
   > reviewer.
2. Never use "thrilled," "excited to share," or "amazing" as a substitute for saying what actually
   happened. Get specific instead. (Rule 05)
3. Never hide an unmet need behind confident framing. If asked to write about a gap, state it
   plainly. (Rule 03)
4. Never bundle multiple CTAs into one line. One action per piece. (Rule 02 + Part 2)
5. Never speak about the community as a problem to be solved. They are the agents; Cambio provides
   tools. Frame accordingly.
6. Never copy funder-register language into community-facing copy. "Underestimated New Yorkers
   facing systemic barriers" is right for a grant; it's wrong on a signup form a resident reads.
7. Never narrate the guide's own reasoning inside the copy. Follow the rules; don't announce that
   you're following them.

   > Surfaced in test round 1: an output ended a grant paragraph with *"We would rather state that
   > plainly than describe it as momentum."* That's Rule 03's rationale leaking into the deliverable.
   > The funder should experience the candor, not be told about it.
8. Never carry one program's framing into another program's copy. Startup NYCHA, the DxC Fellowship,
   Cambio Solar, and Journey have different value propositions. "Your lived expertise is what
   matters" is the Fellowship's line (Row 43) — don't paste it onto a business accelerator just
   because it sounds on-brand.
9. **Never default to clipped sentence fragments as a house style.** "Six months. Free. Built for
   residents." reads as generic startup-marketing rhythm and appears nowhere in the audit. Cambio
   writes in full sentences. Use fragments sparingly and deliberately, not as a default cadence.

---

## Sparky's voice — an open question flagged for Leadership

The audit found "CAN I HELP YOU?" voiced as Sparky (Row 3) — playful, distinct from Cambio-the-org's
register. **Unresolved:** is Sparky's voice meant to be a distinct character (warmer, more casual) or
simply Cambio's voice in a chat window? This guide currently assumes Sparky follows all rules above
plus a lighter, more conversational tone. Leadership should confirm before v2.

See [`../../sparky/conversation-log.md`](../../sparky/conversation-log.md) for a real eval log of
Sparky's in-product tutor behavior — a distinct consumer of Cambio's voice from anything
`/cambio-voice` drafts, since Sparky runs live inside the Journey platform rather than producing
reviewed copy.

*End of guide v1.*
