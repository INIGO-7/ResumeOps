---
name: apply
description: Apply to a specific job — turn a job description into a tailored, ATS-clean CV PDF and a cover letter PDF in their own applications/ folder, drawn entirely from knowledge/. Use when the user runs /apply, pastes or links a job description, names a company and role they want to apply to, or asks to tailor, adapt or write a CV, resume or cover letter for a job.
---

# Apply

Turn one job description into one application: a tailored CV PDF and a cover letter PDF,
plus the JD and the reasoning behind the choices, all in a self-contained folder under
`applications/`.

Nothing here is written from scratch. Every line is a **selection** from `knowledge/`,
framed in the JD's vocabulary. If a claim cannot be traced to `knowledge/`, it does not go
on the page — it becomes a question for the user.

Read before starting, in this order: `config.yml`, `CLAUDE.md`,
`templates/master-template-preferences.md`, `templates/cover-letter-preferences.md`, and
the `template-preferences.md` of the backend in `drafting.default_template`.

## 0. Preflight

**The knowledge store must be populated.** Check:

```
grep -l "(empty — run" knowledge/generated/*.md
```

If a file this application needs is still carrying the sentinel, stop and run `/setup` —
in full if most files are empty, scoped to the blocking files if only a few are. Never
draft around an empty store; the result is invented.

Confirm the toolchain for the chosen backend before drafting, not after: `pdflatex` and
`pdftotext` for `latex`; a browser print-to-PDF step for `html`, which the user has to
perform themselves. If the backend's toolchain is missing, say so now and offer the other.

## 1. Ingest the JD

Accept it however it arrives: pasted text, a file, a URL, or just "apply to the ML
engineer role at Acme". Pasted text is the most reliable — job boards render badly and
expire. If given only a URL, fetch it; if the fetch returns a login wall or an empty
shell, ask for the pasted text rather than guessing at the role.

If the user gives only a company and role with no JD, ask for the posting. Without a JD
there is nothing to tailor to, and a generic CV is what this repo exists to avoid.

Create the application folder:

```
applications/<YYYY-MM>-<company-slug>-<role-slug>/
```

lowercase, hyphenated, month of application. Write the JD verbatim into `jd.md` with a
short front matter block: source URL, date captured, company, role title, location,
work model, and salary range if stated. Verbatim means verbatim — never summarise the JD
into the file, since later passes re-read it for exact vocabulary.

Detect the JD's language and resolve the drafting language against `config.yml`
(`match-jd`, or a forced `en`/`es`). State which language you are drafting in before you
draft, so a wrong detection is caught early rather than after two PDFs.

## 2. Analyse the fit — before writing any CV line

Write `notes.md` in the application folder. This is the reasoning layer, and doing it
first is what stops the CV from being a lightly reworded copy of the last one.

- **Requirements, ranked.** Pull every requirement out of the JD and rank it by how much
  the posting leans on it: what appears in the title, first, repeatedly, or under
  "must have" outranks a bullet buried under "nice to have".
- **Evidence map.** For each requirement, the specific item in `knowledge/` that proves it
  — file and entry, not a vibe. Mark each as **strong** (a quantified achievement),
  **partial** (adjacent or dated experience), or **absent**.
- **Keyword harvest.** The exact terms the ATS will scan for, in the JD's own spelling.
  Only terms the candidate can defend in an interview survive into the CV.
- **Angle.** One or two sentences: which version of this candidate the CV is presenting,
  and what gets demoted to make room. Everything downstream follows from this.

Then handle the gaps per `guardrails.missing_evidence` in `config.yml`. With `ask`: bring
the user every **absent** requirement in one batch — one message, not a drip — asking
whether they have something unrecorded that covers it. Often they do; it never made it
into the store. Whatever comes back gets written into `knowledge/generated/` first, then
used. Never soften an absence into a claim, and never round a partial up to a strong.

## 3. Draft the CV

Copy the backend template into the application folder (`cv.tex` or `cv.html`) and fill it.
Follow `master-template-preferences.md` for content — section order, bullet structure,
summary shape, skills groups, what never appears — and the backend file for mechanics.

The three that decide whether the CV gets read:

- **The summary** is the highest-leverage block on the page. It presents the candidate —
  profile, academic history, then intent capped with an extremely concise fit clause —
  rather than reciting the JD's requirements back at the recruiter.
- **Bullet selection is the tailoring.** Pick the achievements from
  `4-achievement-bank.md` that map to the ranked requirements, order them by that ranking
  rather than by how proud the candidate is of them, and reframe each in the JD's
  vocabulary. Reframing means the same fact, different emphasis — never a different fact.
- **The skills groups** carry the ATS harvest: bold abilities, each listing its tools and
  keywords in verbatim JD spelling, every term defensible.

Cut to the page cap in `guardrails.max_pages` by cutting the weakest content first, per
the backend's fitting order. A CV that fits by shrinking type has not been tailored, it
has been compressed.

## 4. Render and verify

Both backends end in the same place: a PDF **the agent produced**, that the agent then
inspects. Only the render command differs — the verification below is identical for both,
run on the final PDF at the end. Never hand a manual export back to the user as the normal
path; produce the PDF here so the same checks always run.

### Produce the PDF

For `latex`:

```bash
pdflatex -interaction=nonstopmode cv.tex && pdflatex -interaction=nonstopmode cv.tex
```

For `html`, render it headlessly — the template's `@media print` block *is* the renderer,
so the output matches a real Cmd/Ctrl-P export:

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --disable-gpu \
  --no-pdf-header-footer --print-to-pdf=cv.pdf "file://$PWD/cv.html"
```

Use whatever Chromium-family binary the machine has (`chromium`, `chrome-headless-shell`).
Only if none is present, fall back to asking the user to Cmd/Ctrl-P → Save as PDF (Letter/A4,
margins "Default") and hand the file back — but that is the fallback, not the default.

### The text-layer checks

Run these every time, no exceptions. They read the PDF as data — what the ATS sees — and
none of them can see the page's layout:

- **It compiled clean** *(latex)*. Grep the log for `^!` and for overfull boxes that push
  into the margin. A PDF that renders despite an error is the common failure, not the rare
  one. (The html render has no log; its layout faults surface in the visual pass below.)
- **Page count.** `pdfinfo cv.pdf | grep Pages` against `guardrails.max_pages`.
- **The text layer.** `pdftotext cv.pdf - | head -60` must come back in reading order,
  with accents and ligatures intact and no mojibake. This is what the ATS sees; a PDF
  that looks perfect and extracts as noise is a rejected application.
- **The keyword check.** Confirm the harvested keywords actually appear in the extracted
  text. If a term only exists inside a graphic or a ligature-broken word, it is not there.

### The visual-inspection loop

The checks above pass on a PDF whose text is correct but whose *layout* is broken — a
heading stranded at the foot of a page, an entry title split from its body, a lake of
whitespace. The extractor cannot see any of it, so **Read the produced `cv.pdf`** (the file
tool renders the pages as images) and inspect it against this checklist — the same list for
either backend:

- **Page count** is within `guardrails.max_pages` — confirm visually, not only via `pdfinfo`.
- **No orphaned heading.** No section heading or entry heading (latex `\resumeExpHeading` /
  `\resumeProjectHeading`; html `h2` / `.entry` head) sits alone at the foot of a page with
  its content pushed to the next.
- **No split entry.** No role/entry title is separated from the first line of its body.
- **No isolated heading** left dangling with nothing beneath it.
- **No large whitespace pool** — no dead band of empty space mid-page or a page ending far
  short of the others.
- **No content breaking into the margin** — text or rules running past the text block.

On a defect, apply the **named rescue for the active backend**, then **re-render and Read the
PDF again**. Fix one defect per pass so each rescue's effect is legible:

- **latex** — from `templates/latex/template-preferences.md` ("Visual-defect rescues"):
  `\needspace{N\baselineskip}` before an orphaned/isolated/split heading, `\enlargethispage`
  for a one-or-two-line near-miss overflow, a content cut per the fitting order for a
  structural overflow or whitespace pool.
- **html** — from `templates/html/template-preferences.md` ("Print / PDF export"):
  `break-inside: avoid` / `break-after: avoid` on the block that split, a tweak to the
  `@media print` sizing for a near-miss overflow, a content cut for a structural one.

**Cap the loop at 3 passes.** If the page is clean, proceed. If defects remain after the
third pass, **stop** — do not ship silently and do not keep looping. Report the specific
unresolved defects to the candidate (which heading, which page, what was tried), so they can
decide between a further content cut and accepting the layout.

Rename the final PDF per `output.filename` in `config.yml`, resolving `<Name>` from
`knowledge/generated/1-identity-and-constraints.md` and `<Role>` from the JD's title.
Keep the source `.tex`/`.html` in the folder — the next revision starts from it.

## 5. Draft the cover letter

Per `drafting.cover_letter` in `config.yml` (`always` / `ask` / `never`). Draft it after
the CV, never before: the letter's proof paragraph should tell the story behind a bullet
the CV already carries.

Follow `templates/cover-letter-preferences.md` for structure and voice, and render with
`templates/latex/cover-letter-template.tex` into `cover-letter.tex` →
`output.cover_letter_filename`. Same verification as the CV: clean compile, one page,
clean extraction. Only the `latex` backend has a cover letter template — if the CV was
rendered through `html`, say so and render the letter through LaTeX rather than
improvising a layout.

The "why this company" paragraph is the one that cannot be generated from `knowledge/`
alone. Ask the user what draws them to this company before writing it — a single question
gets a more genuine paragraph than any amount of inference from the JD. If they have
nothing specific, keep the paragraph short and about the work rather than manufacturing
admiration.

Also produce a **plain-text version** of the letter body in `cover-letter.md` — most
application forms take a text box, not an attachment, and reformatting a PDF by hand at
submission time is where the typos come from.

## 6. Critique — put on the recruiter hat

Do not skip this because the draft reads well. Take the CV critique role from the parent
`CLAUDE.md`: a hard-to-impress recruiter for this exact role at a top company in the
domain, who rejects most CVs in fifteen seconds.

Write `review.md`, and be specific rather than kind:

- The fifteen-second verdict: reading top-down, does the fit land before the fold?
- The three weakest lines on the page, quoted, with why each is weak — unquantified,
  generic, or a responsibility posing as an achievement.
- What the JD asks for that the CV fails to show even though `knowledge/` has it. This is
  the most common and most fixable failure.
- What a recruiter would probe or doubt in a screen: a gap, a short tenure, a claim that
  sounds inflated, a keyword with thin backing.
- The cover letter: does it say anything the CV could not?

Then apply the fixes worth applying and note what was deliberately left alone and why.
One critique pass, applied, is worth more than three drafts.

## 7. Write back and record preferences

All of these, before reporting done:

- **The tracker.** Update `applications/tracker.md`, a single plain-markdown table at the
  root of `applications/` indexing every application produced. Create it on first use if
  absent, with this header:

  ```markdown
  # Applications

  | Date | Company | Role | Fit | Link |
  | --- | --- | --- | --- | --- |
  ```

  Then add exactly one row for this application:
  - **Date** — month of application, the `<YYYY-MM>` from the folder name.
  - **Company** / **Role** — as on the application.
  - **Fit** — the overall `/100` score plus its verdict tier from `notes.md` (e.g.
    `78/100 — strong`). If no fit score was computed, leave the cell blank rather than
    inventing one.
  - **Link** — a relative markdown link to the application folder, e.g.
    `[folder](2026-07-acme-ml-engineer/)`.

  The folder path is the key: if a row for this application's folder already exists (a
  re-run), update that row in place — never append a duplicate. This is a pure index —
  no status/outcome column and no calibration; do not add columns beyond the five above.

- **New facts** surfaced in this application — anything the user said while filling a gap
  — go into the right `knowledge/generated/` file, per `auto_write_back_to_knowledge` in
  `config.yml`. Say which file was updated. A fact that lives only in the application
  folder is a fact the next application has to re-elicit.
- **New preferences** get recorded where they belong, and this applies to anything the
  user states or corrects about style, not just things they call preferences. Classify:
  - format-agnostic CV rule → `templates/master-template-preferences.md`
  - cover letter rule (tone, length, structure, salutation) →
    `templates/cover-letter-preferences.md`
  - typography, spacing, layout → that backend's `template-preferences.md`
  - a switch (language, backend, page cap, filename) → `config.yml`
  - something true about the candidate → `knowledge/generated/`

  Mention the file you updated. If the classification is genuinely ambiguous, ask rather
  than guessing — a preference filed in the wrong place silently stops applying.

## 8. Report

Short. What was produced and where, the drafting language, the fit summary in a line or
two, any requirement still unevidenced, the row added to `applications/tracker.md`, and
the files updated in `knowledge/` and `templates/`. Offer the next move — revise a
section, adjust the angle, or a mock screen against this JD — rather than declaring the
application finished.

## Application folder, when done

```
applications/2026-07-acme-ml-engineer/
  jd.md              # the posting, verbatim, with capture metadata
  notes.md           # ranked requirements, evidence map, keywords, angle
  cv.tex             # source, so the next revision is an edit not a rewrite
  CV_<Name>_<Role>.pdf
  cover-letter.tex
  cover-letter.md    # plain-text body for web forms
  CoverLetter_<Name>_<Role>.pdf
  review.md          # the recruiter critique and what was done about it
```

Alongside the per-application folders, `applications/tracker.md` is a single index table
spanning every application — one row each, maintained by step 7.
