# ResumeOps

Headquarters for the candidate's job search: a durable store of everything true about the candidate, plus the machinery to turn that store into a CV tailored to a specific job description.

**Goal:** never rewrite a CV from scratch. Facts live once in `knowledge/`; each application is a targeted selection and framing of those facts, rendered through a template.

## Structure

- `config.yml` — switches only: CV language, rendering backend, drafting guardrails, output filename. **Read it first, before any drafting, tailoring or rendering.** An explicit instruction in conversation overrides it; it overrides everything else. No fact about the candidate ever lives here — those live in `knowledge/`.

- `knowledge/` — the candidate knowledge store, the source of truth for every claim on a CV.  
  - `raw/` — written by the user: brain dumps, old CVs, project notes, performance reviews, LinkedIn exports, certificates, anything else, in whatever format they have (pdf, docx, md, txt, images). Flat — no subfolders. Unstructured and authoritative: never edit the contents; they are the user's own words.
    The user dumps files in unordered. The only thing done to them is a **rename**, always proposed to the user and never applied silently, to `<category>-<what-it-is>[-<year>].<ext>` — lowercase, hyphenated, category first so the directory greps and sorts by kind. Categories: `cv`, `profile`, `linkedin`, `experience`, `project`, `education`, `certification`, `recognition`, `publication`, `misc`. Example: `recognition-acme-360-feedback-2024.pdf`.
  - `generated/` — written by AI: the structured synthesis of `raw/` plus whatever the user says in conversation. AI-owned, so safe to edit and extend — but merge into it rather than clobbering it: it accumulates facts from conversations that live in no raw document, so never regenerate a file from scratch and drop them. All organisation of the candidate's material lives here, never in `raw/`.
    The set of files here is **fixed**: always the same numbered files, no more and no fewer, whatever the candidate. Never add, remove or rename one — only their contents change. Each file opens with a **"What lives here"** paragraph stating what it holds, followed by what belongs in a sibling instead and how to format an entry. Read those paragraphs to know what a file contains or will contain, and route every fact by them.  An unpopulated file still carries the sentinel `_(empty — run /setup to populate)_` under its `## Record` heading; that sentinel is what makes `/setup` fire. Facts get added to these files whenever they surface, in any conversation, not only during setup.

- `applications/` — one folder per application, e.g. `2026-07-acme-ml-engineer/`, self-contained: the JD, the tailored CV, and any notes or review for that target.

- `templates/` — how a CV is written and rendered.
  - `master-template-preferences.md` — format-agnostic CV drafting guidelines: tone, bullet style, section order, length, what to include or avoid. Applies to every CV drafted.
  - `latex/`, `html/` — one directory per rendering backend, each holding the template source plus a `template-preferences.md` scoped strictly to that format (typography, spacing, layout mechanics). Never duplicate a master rule here; if a preference holds regardless of format, it belongs in the master file.

Read the master file plus the chosen backend's file before drafting or rendering.

## Working rules

- Every line on a CV must trace back to something in `knowledge/`. If a JD calls for something not recorded there, ask the user — do not invent or inflate it.
- New facts learned while tailoring belong in `knowledge/`, not only in the application folder, so the next application benefits.
- Tailor by selection and emphasis (which achievements, which framing, which keywords from the JD), not by changing what happened.
- CVs are read by both an ATS parser and a human recruiter; keep both satisfied.
- Preferences that are switches rather than prose (language, backend, page cap, write-back) belong in `config.yml`. Update it there instead of adding a duplicate rule elsewhere.  Anything true about the candidate — including name, contact details and constraints — is a fact, not a switch, and belongs in `knowledge/generated/`.
- Capture preferences whenever they surface, in any conversation — not only while tailoring.  When the user states or corrects a CV preference, classify it and append it to the right file: format-agnostic → `templates/master-template-preferences.md`, format-specific → that backend's `template-preferences.md`, a fact about the candidate → `knowledge/`.  Mention the file you updated; if the classification is genuinely ambiguous, ask.

## Agent skills

### Issue tracker

Issues and specs live as markdown files under `.scratch/<feature>/`. See `docs/agents/issue-tracker.md`.

### Triage labels

Default canonical labels (`needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`). See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: `CONTEXT.md` + `docs/adr/` at the repo root. See `docs/agents/domain.md`.

