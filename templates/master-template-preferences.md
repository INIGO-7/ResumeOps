# Master template preferences

Format-agnostic rules for drafting any CV in this repo. They apply whether the CV is
rendered through `latex/` or `html/`. Anything about typography, spacing or layout
mechanics belongs in the backend's own `template-preferences.md`, not here.

## Section order

Fixed, in this order. Skip a section entirely rather than padding it:

1. **Summary**
2. **Skills** — bold ability groups, each carrying its tools and keywords
3. **Experience** (professional record)
4. **Key Awards**
5. **Projects** (and research)
6. **Education**
7. **Languages**

Rationale: a recruiter reads top-down for ~15 seconds. The summary states who the
candidate is, the skills block satisfies the ATS scan and the human skim at once, and
the professional record carries the proof. Education sits low because work evidence outranks it once
there is any; move Education above Experience only for a candidate with no professional
record yet.

## Length

One page. Two pages only with 8+ years of relevant experience, and never a half-empty
second page — either it is full or the content gets cut.

## Summary

A portrait of the candidate, not a paraphrase of the posting. The failure mode to avoid:
a summary that regurgitates the JD's requirements back with "…which I have" appended —
a recruiter recognises their own posting instantly and learns nothing from it.

- 3–5 lines, no heading fluff, no "results-driven professional".
- Sentence 1: the candidate's main profile — the role(s) they have actually operated in
  (research, engineering, product, a mix) and at what level.
- Sentence 2: academic history in one clause — degrees and, when they carry weight,
  institutions.
- Sentence 3: intent — what the candidate wants to do next and where (e.g. "looking to
  thrive in <city>'s rich AI ecosystem") — capped with the why-they-fit. The fit clause
  is **extremely concise** and finishes the intent sentence; it never becomes its own
  paragraph of JD echoes.
- Any hard constraint the recruiter must know up front: location, relocation intent,
  work authorisation, availability.
- No first-person pronouns except a deliberate "I" if the voice calls for it; never mix.

## Bullets

- Structure: **action → what you built → measured outcome**. The outcome is the point;
  a bullet without one is a job description, not an achievement.
- Quantify wherever a number exists in `knowledge/`: percentages, latency, cost, hours
  saved, dataset size, team size, users. If no number exists, state the concrete
  consequence instead — never invent a metric.
- Past tense for finished work, present tense only for the current role.
- One to two lines each. A bullet that wraps to three lines is two bullets.
- 3–5 bullets for the most relevant role, 1–2 for older or less relevant ones.
- Lead each bullet with the word that carries the most weight, not with "Responsible for".
- Bold sparingly, on the phrases you want a skimming eye to land on — roughly one or two
  per bullet. Bolding everything is bolding nothing.

## Skills

One block under the **Skills** heading: 4–6 main abilities, each **in bold**, followed
by the keywords and tools for that ability. Example:

> **Infrastructure & Languages:** Python (5+ yrs), C/C++, Scala, SQL; AWS (S3, Lambda),
> Docker, PostgreSQL, FastAPI, Apache Airflow, Git

No standalone "Keywords" line — the ATS harvest lives *inside* these groups. Terms the
ATS will scan for use the JD's exact spelling ("Kubernetes", not "K8s"; include both the
acronym and the expansion when the JD does). Every term must be true and evidenced
somewhere in `knowledge/`; a keyword you cannot defend in an interview is a liability,
not a match.

Reorder the groups so the ability the JD leads with comes first. Within a group,
most-relevant tool first. Add years only where the depth is a selling point.

Never list a skill on the CV that does not appear in `knowledge/`.

## Awards

Only awards with a real selection bar. Always state the bar itself (field size, ranking,
institutions represented) — "winner of X" means nothing to a recruiter who does not know
X. Link the proof (press release, announcement) when one exists.

## Projects

For depth the professional record does not show: theses, research, open source. Where
space allows, this section is **comprehensive** — list the projects that best match the
JD first, and cover for each:

- what was done and what was achieved, relative to the problem;
- the engineering techniques applied — never name the JD, but this is where the
  recruiter sees the candidate has done this kind of work before;
- the methodology, when one genuinely applies.

That order is not mandatory, and a project may take more than one bullet when its
relevance earns it. The balance to strike, per JD: deep detail on the few projects that
map squarely onto the posting, versus more projects covered briefly to show a richer,
more varied profile. Overexplaining a marginal project costs a relevant one its slot.

Name the stack explicitly and link the repo, paper or demo when there is one. Drop the
section before dropping a work bullet.

## Education

Reverse chronological. The **degree in bold, alongside the institution name** on the
entry's heading line; dates and location on the right. Below each entry, one line of
**relevant coursework** — the courses that map to the target role, and only those. Keep
the line short: coursework that merely restates the Skills section is wasted space, and
too many courses dilute the ones that matter. Include GPA/honours only if strong and
recent. No high school once there is a degree.

## Languages

Proficiency plus the evidence for it ("Fluent — partly schooled in Ireland", "B1 —
Erasmus semester in Germany"). Bare CEFR levels without context read as self-assessed.

## Tailoring

- Tailor by **selection and emphasis** — which achievements appear, in what order, framed
  in whose vocabulary. Never by changing what happened.
- Mirror the JD's job title in the header/tagline; it is the first keyword match.
- Every line must trace to `knowledge/`. If the JD asks for something not recorded there,
  ask the user — do not infer or inflate.
- New facts surfaced while tailoring go back into `knowledge/`, not just the application
  folder.

## ATS hygiene

- Standard section names ("Experience", "Education", "Skills"). No cute renaming.
- No text inside images, no headers/footers carrying content, no multi-column layout for
  body content, no tables used to lay out entries.
- Dates in a consistent `Mon YYYY – Mon YYYY` form throughout; use "Present" for current.
- Contact details as plain selectable text in the document body.
- Filename: `CV_<Name>_<Role>.pdf`.

## Never

- Claims with no backing in `knowledge/`.
- Photos, age, marital status, nationality (unless work authorisation makes it relevant).
- "References available on request".
- Skill bars, star ratings, percentage-proficiency graphics — unverifiable and unparseable.
- Buzzword strings with no achievement attached.
