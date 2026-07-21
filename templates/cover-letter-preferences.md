# Cover letter preferences

Format-agnostic rules for drafting any cover letter in this repo. Layout and typography
live in the backend's own `template-preferences.md`; content rules live here.

A cover letter is not a prose restatement of the CV. The CV proves the candidate *can* do
the job; the letter answers *why this company, why this role, why now* — the one question
a CV structurally cannot answer.

## Length and shape

One page, always — realistically 250–350 words, four paragraphs, never more than five.
A letter that fills the page is already too long: the reader is skimming for a reason to
keep reading the CV.

1. **The hook (2–3 sentences).** Name the role and where it was seen, then state the
   single strongest reason this candidate fits *this* posting. Lead with the specific,
   not the generic: a system built, a domain owned, a number. Never open with "I am
   writing to apply for" or "I was excited to see".
2. **The proof (4–6 sentences).** One or two achievements from
   `knowledge/generated/4-achievement-bank.md`, chosen because they map onto the JD's top
   requirements — not the same framing as the CV bullet, but the story behind it: the
   problem, the decision, the outcome. Depth on two things beats a tour of six.
3. **The fit (3–4 sentences).** Why this company specifically. Something concrete about
   their product, domain, stack or public work — and how the candidate's direction
   (`10-career-narrative.md`) meets it. This paragraph is where generic letters die; if
   nothing specific can be said truthfully, ask the user rather than padding it.
4. **The close (2 sentences).** Any hard constraint the recruiter must know (location,
   authorisation, notice period), then a plain, confident sign-off. No "I would be
   thrilled for the opportunity to", no begging.

## Voice

- First person, plain, declarative. The letter is the one document written as a person
  speaking, so it may say "I" freely — where the CV must not.
- Confident, never grandiose. State what happened and let it land; no "passionate",
  "dynamic", "results-driven", "world-class", "leverage", "synergy".
- Match the JD's register: a startup posting written casually gets a warmer letter; a
  bank or public institution gets a formal one.
- Same language as the CV (`config.yml` → `drafting.language`). Never mix languages.
- No em-dash-heavy, list-shaped prose. Paragraphs, not disguised bullets.

## Truth

- Every claim traces to `knowledge/`, exactly as on the CV. The letter's looser form is
  not licence to inflate.
- Enthusiasm about the company must be attributable to something real the user said or
  something verifiable about the company. Invented admiration reads as invented.
- Never restate the whole CV. If a paragraph could be cut and pasted into the CV summary,
  it is doing the CV's job, not its own.

## Addressing

- Name the hiring manager when the JD or the user gives one; otherwise "Dear Hiring
  Team," — never "To Whom It May Concern" or "Dear Sir/Madam".
- Company name and role title spelled exactly as in the JD.
- Include the date and the company line only when the letter is a standalone PDF; a
  letter pasted into an application form's text box drops both.

## Filename

`CoverLetter_<Name>_<Role>.pdf`, matching the CV's naming (`config.yml` → `output`).
