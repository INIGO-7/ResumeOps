---
name: setup
description: Set up ResumeOps for a new candidate — take a dump of raw documents, rename them into the repo convention, and populate the knowledge/generated/ files, interviewing the user to fill whatever the documents miss. Use when the user runs /setup, asks to set up or initialise this repo, or wants to fill in their candidate profile. Use also, unprompted, whenever a task needs a fact from knowledge/generated/ and the files are still carrying the empty sentinel.
---

# Setup

Turn an empty repo into a populated knowledge store. The output of this skill is the fixed
set of files in `knowledge/generated/` filled with everything true about the candidate,
each claim traceable to a document in `knowledge/raw/` or to something the user said here.

Read `CLAUDE.md` and `config.yml` before starting.

## When this fires on its own

A file in `knowledge/generated/` is unpopulated when its `## Record` section still
contains the line `_(empty — run /setup to populate)_`. Check with:

```
grep -l "(empty — run" knowledge/generated/*.md
```

If another task needs a fact from a file that is still empty, stop that task, say why in
one sentence, and offer to run setup — either in full, or scoped to just the file that is
blocking. Never invent the fact, and never quietly work around the gap.

## Workflow

Five steps, in order. Do not skip ahead: the interview in step 5 is only worth the user's
time once the documents have been mined, and asking about something already written in a
document they handed over is the fastest way to waste their patience.

**This skill is idempotent — a second run is a top-up, not a redo.** Before doing anything,
take stock of what already exists:

- **Which generated files are already populated** — those without the
  `_(empty — run /setup to populate)_` sentinel. Their contents are kept and only added to,
  never regenerated.
- **Which raw documents are already ingested.** A file already in the
  `<category>-<what-it-is>[-<year>]` naming convention was renamed and mined on a previous
  run; a file still bearing an arbitrary name (`Documento1.pdf`, `CV_final.docx`) is new.
  The rename is the ingestion marker.

From that, pick the mode. A **first run** — sentinels everywhere, nothing renamed yet — is
the full flow below. A **re-run** touches only the delta: rename and mine the new documents,
merge their facts into the existing files, and interview only about gaps still open. If
there are no new documents and no thin spots left, say so and stop — do not re-rename,
re-mine or re-interview what is already done.

### 1. Present the repo — briefly

Two or three lines, no headings, no bullet tour: ResumeOps is a durable store of
everything true about the candidate, filled once so a CV is never written from scratch
again — every future application draws on it. Then move straight to step 2 in the same
message. Do not wait for acknowledgement.

On a re-run, skip the pitch entirely — one line ("topping up your store with the new
documents") is enough, then straight to step 2.

### 2. Ask for the document dump

Ask the user to drop everything they have into `knowledge/raw/`, in any format, in no
order, with no thought about naming — that gets handled for them. On a re-run, ask only for
what is new since last time; they need not re-add documents already in `knowledge/raw/`.

Give them the prompt list, because people systematically forget most of these:

- old CVs and cover letters, in any language, however outdated
- their LinkedIn export, profile PDF, and any recommendations received
- per-employer material: the internal job description of roles they held, offer and
  promotion letters, work journals, standup or retro notes, handover docs
- performance reviews, 360 or peer feedback, awards, testimonials, screenshots of praise
- side projects, repos, portfolio pieces, hackathons, client work
- degrees, transcripts, thesis, certifications, language certificates
- papers, patents, talks, blog posts
- notes from past interviews, wherever they wrote out or rehearsed how they describe
  their own work

Two things to say explicitly, because they change how much the user hands over:

- **More is strictly better.** Nothing is too rough, too old, too fragmentary or too
  self-congratulatory. Recognition material in particular feels like bragging to dump and
  is the single richest source of quantified achievements.
- **Nothing here is published.** It is a local store; what reaches a CV is chosen later,
  one application at a time.

Then wait. If the user has nothing to give, say that setup still works — it just means the
interview in step 5 carries the whole load — and go to step 5.

### 3. Propose the renames as one batch

Consider only files not already in the naming convention — a file already conventioned was
ingested on a previous run, so leave it untouched. For each of the rest, read enough to
know what it actually is — never rename from the existing filename alone, which is usually
`Documento1.pdf` or `CV_final_final.docx`.

Propose every rename in a **single table** for one approve-or-adjust round: current name,
proposed name, and a few words on what the file turned out to be. Target convention, from
`CLAUDE.md`:

```
<category>-<what-it-is>[-<year>].<ext>
```

lowercase, hyphenated, category first. Categories: `cv`, `profile`, `linkedin`,
`experience`, `project`, `education`, `certification`, `recognition`, `publication`,
`interview`, `misc`. For per-employer material put the employer in the descriptor:
`experience-acme-handover-doc.md`, `recognition-acme-360-feedback-2024.pdf`.

Apply the batch only after the user approves it. Flag in the table, rather than silently
resolving: files whose content you could not read (scans, images, unusual formats — ask
what they are), files that appear to be duplicates or near-duplicates of each other, and
anything that looks like it does not belong in the repo at all.

Never edit the contents of a file in `raw/`. Renaming is the only permitted change.

### 4. Populate `knowledge/generated/`

Read the existing generated files first, then the raw documents, then write. On a first run
the generated files are empty and you write them fresh; on a re-run you fold the facts from
the new documents into files that already hold content.

- **Read each generated file's opening paragraphs before writing into it.** They state what
  belongs there and what belongs in a sibling instead. Route by them, and follow the
  "How to record it" format so files written in different sessions stay consistent.
- **Merge, never clobber.** Preserve everything a generated file already holds — including
  facts added through earlier conversations that appear in no raw document — and fold new
  facts in alongside them. Add, deduplicate and correct; never regenerate a file from
  scratch, and never drop a fact because you cannot see its source document on this run.
- **Write long.** This is a store, not a CV. Compression happens at drafting time; a
  detail dropped here is gone for every future application.
- **Every claim carries its source.** In `4-achievement-bank.md` that is the explicit
  `**Evidence:**` and `**Confidence:**` lines; elsewhere, name the raw file when a fact
  would otherwise be unattributable.
- **Never infer a number.** If a document implies impact without quantifying it, record
  the concrete consequence and mark the number as something to ask about in step 5.
- **Remove the `_(empty — run /setup to populate)_` sentinel** from any file you populate.
  Leave it in place in files that are still genuinely empty.
- A fact that belongs in a file you are not currently writing goes into that file
  immediately. Do not batch it up and do not leave it only in your reasoning.

Then audit coverage, file by file, and write down what is missing or thin: a role with no
achievements attached, an achievement with no number, a skill with no evidence, a date
range with a gap in it, a `TODO — ask candidate`, an entirely empty file. Each gap becomes
one specific question in step 5, so make the list concrete: not "role thin" but "Acme
2020–2023 has no achievement recorded" — the wording of the gap is most of the wording of
the question.

Treat the **fit-critical facts** as gaps too, and expect documents to be silent on them —
they are subjective and almost never written down, so their absence is a gap to raise in
step 5, not a sign the candidate has none. In `1-identity-and-constraints.md`: hard
deal-breakers and the location/relocation stance. In `10-career-narrative.md`: what
motivates the candidate and their career goals. These are what the fit score and the
cover-letter angle-inference read from, so an empty subheading for any of them is a gap
worth a question.

### 5. The interview

Report what got populated and what is thin, then work through the gap list from step 4 as
specific, targeted questions — one per gap, one at a time.

**Specific, not general.** Each question names the exact hole and asks for exactly what is
missing. A broad prompt like "walk me through your career" pushes all the sorting work onto
the user and leaves them unsure what you actually need; a pointed question tells them
precisely what to answer and takes a fraction of the effort. Compare:

- Instead of "tell me about your time at Acme" → "You have Acme listed from 2020–2023 but
  no achievement recorded for it — what is one thing you shipped there that you are proud
  of?"
- Instead of "what are your skills" → "You mention Kubernetes in the Acme role but it is
  not in the skills inventory — how would you rate it, and where did you use it?"
- Instead of "any gaps in your history" → "There is a gap between the Acme role ending in
  March 2023 and Globex starting in September 2023 — what were you doing then?"

Order the gaps so the ones that unlock the most come first: usually a role in
`3-work-experience.md` with no achievements attached, since one answer there often fills
skills, numbers and narrative at once.

**Style:**

- **One question at a time.** Never a numbered list of questions, never a form.
- **Anchor every question to its gap.** Name the role, the file, the missing field, the
  date. The user should never have to guess what you are after or do the categorising
  themselves.
- **Keep the answer bounded.** Ask for the one missing thing, not a general essay. If an
  answer opens a richer vein than expected, follow it — but you open with the narrow
  question, not a broad one.
- **Chase the number, gently.** Where a gap needs a figure, ask for it directly — "roughly
  how much faster did it get?" — and accept "no idea" without pushing. An approximate
  number stays approximate: record it with `**Confidence:** approximate` and never round it
  into something that looks precise.
- **Reflect back.** Restate what you heard as the achievement or fact you intend to record,
  and let them correct it. Corrections are information.
- **Never re-ask what is already answered** — in a document they gave you or already
  recorded in a generated file. It signals you did not read what you already have, and it is
  the fastest way to lose their patience. On a re-run especially, only open gaps are on the
  table.

Write to the generated files **as you go**, not at the end — a long interview whose output
is lost to a crash or a context limit is worse than no interview.

Cover the awkward things too, because a recruiter will: employment gaps, a short tenure, a
pivot, a step sideways. Ask neutrally and record the explanation in
`10-career-narrative.md` under "Things a recruiter will ask about". This is a store, not a
judgement — say so if the user seems defensive.

Elicit the **fit-critical facts** when they are absent, one question at a time, framing each
as helping tailor future applications rather than as a screening test:

- **Hard deal-breakers** — "Is there anything that would make you turn a role down outright,
  no matter how good the rest is — no relocation, a salary you won't go below, no on-call?"
  → `1-identity-and-constraints.md` under "Deal-breakers".
- **Location / relocation stance** — where they are, and whether they will relocate or need
  remote → `1-identity-and-constraints.md` under "Location and mobility".
- **Motivation** — "What actually drives you at work — what makes a job worth doing?" →
  `10-career-narrative.md` under "Motivation".
- **Career goals** — where they want to be in a few years →
  `10-career-narrative.md` under "Career goals".

Accept "no strong view" or "prefer not to say" without pushing, and record that answer as
given rather than leaving the subheading looking unasked.

Stop when the agenda is covered or the user is done. Do not grind through a checklist for
completeness; a tired user gives worse answers than a fresh one, and setup can resume
later. If you stop early, say exactly which files are still thin.

## Finishing

Report:

- which generated files are populated, and which are still empty or thin
- on a re-run, which new documents were ingested and what they added — and if nothing new
  was found, that the store was already up to date and nothing was changed
- any `TODO — ask candidate` left in the files
- any raw document you could not read
- that facts learned in future conversations get written back automatically, so the store
  improves with every application rather than needing another setup

Do not offer to draft a CV in the same breath. Setup is its own deliverable; drafting
needs a job description.
