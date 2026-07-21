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

### 1. Present the repo — briefly

Six lines at most, no headings, no bullet-point tour. What it is: a durable store of
everything true about them, so a CV is never written from scratch again. What is about to
happen: they dump documents, those documents get renamed and mined, and then a
conversation fills the gaps. What they get: a knowledge store that every future
application draws on.

Then move straight to step 2 in the same message. Do not wait for acknowledgement.

### 2. Ask for the document dump

Ask the user to drop everything they have into `knowledge/raw/`, in any format, in no
order, with no thought about naming — that gets handled for them.

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

Once files are in `knowledge/raw/`, read enough of each to know what it actually is —
never rename from the existing filename alone, which is usually `Documento1.pdf` or
`CV_final_final.docx`.

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

Read every file in `raw/`, then write the generated files.

- **Read each generated file's opening paragraphs before writing into it.** They state what
  belongs there and what belongs in a sibling instead. Route by them, and follow the
  "How to record it" format so files written in different sessions stay consistent.
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
range with a gap in it, a `TODO — ask candidate`, an entirely empty file. That list is the
agenda for step 5 — but it is an agenda, not a script.

### 5. The interview

Report what got populated and what is thin, then start asking.

**Open-ended first.** Begin with the two files that unlock the others:
`10-career-narrative.md` and `3-work-experience.md`. Ask the user to walk you through
their career, then through each role in turn — what the job actually was, what they built,
what was hard, what changed because they were there. Five minutes of someone talking about
a job surfaces certifications, skills, awards, numbers and constraints all at once. Only
once that vein is exhausted do you ask narrowly about what is still missing.

**Style — this is the part that determines how much you get:**

- **One question at a time.** Never a numbered list of questions, never a form.
- **Ask for the story, not the field.** "Walk me through what the first six months at
  Acme actually looked like" beats "what were your responsibilities at Acme". "Tell me
  about the thing you built there that you are proudest of" beats "list your achievements".
- **Invite the tangent.** Say plainly, more than once, that rambling is useful and that
  detail they think is irrelevant is often the thing that ends up on the CV. Let them
  overshare; sorting it is your job, not theirs.
- **Follow the energy.** When they get animated about something, stay there and go
  deeper. That is where the strongest material is.
- **Chase the number, gently, and only after the story.** "Do you have a rough sense of
  how much faster it got?" — and accept "no idea" without pushing. An approximate number
  stays approximate: record it with `**Confidence:** approximate` and never round it into
  something that looks precise.
- **Reflect back.** Periodically restate what you heard as the achievement you intend to
  record, and let them correct it. Corrections are information.
- **Never ask what a document already answered.** It signals you did not read what they
  gave you, and it is the fastest way to lose their patience.
- **Narrow questions come last**, and only for genuine holes: exact dates, credential IDs,
  spelling of a title, work authorisation, whether a client project is safe to name.

Write to the generated files **as you go**, not at the end — a long interview whose output
is lost to a crash or a context limit is worse than no interview.

Cover the awkward things too, because a recruiter will: employment gaps, a short tenure, a
pivot, a step sideways. Ask neutrally and record the explanation in
`10-career-narrative.md` under "Things a recruiter will ask about". This is a store, not a
judgement — say so if the user seems defensive.

Stop when the agenda is covered or the user is done. Do not grind through a checklist for
completeness; a tired user gives worse answers than a fresh one, and setup can resume
later. If you stop early, say exactly which files are still thin.

## Finishing

Report:

- which generated files are populated, and which are still empty or thin
- any `TODO — ask candidate` left in the files
- any raw document you could not read
- that facts learned in future conversations get written back automatically, so the store
  improves with every application rather than needing another setup

Do not offer to draft a CV in the same breath. Setup is its own deliverable; drafting
needs a job description.
