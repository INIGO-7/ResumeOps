# HTML template preferences

Layout and typography rules for `resume-template.html`. Content rules live in
`../master-template-preferences.md` — do not restate them here.

This backend is the **modern, engineer-flavoured** one: for a personal site, an
in-browser share link, or a recruiter who asked for something less flat. The LaTeX
backend remains the default for ATS portal uploads.

## Non-negotiables

Whatever the styling ambition, the document underneath stays boringly parseable:

- **Single-column semantic flow.** One `<h1>`, one `<h2>` per section, entries as `<div>`s
  in reading order. No CSS grid or flex that reorders content visually away from DOM
  order — a parser reads the DOM, a recruiter reads the pixels, and the two must agree.
- **No `<table>` for layout.** The only flex use is `.entry-head`, which puts a title and
  its date on one line; it wraps to two lines on narrow screens and never reorders.
- **No JavaScript.** Nothing here needs it and headless parsers do not run it. If a
  future feature seems to need JS, it is a feature the CV does not need.
- **No external requests.** All CSS inline in a single `<style>` block, no web fonts, no
  CDN, no remote images. The file must render identically offline, when emailed as an
  attachment, and when opened from `file://`.
- **No text in images, no icon fonts, no pseudo-element content that carries meaning.**
  The `//` prefix on section headings is CSS `::before` precisely so it never lands in
  extracted text.
- **No skill bars, rings, or rating graphics.**

## Type and colour

- System font stack for prose (`--sans`); a monospace stack (`--mono`) for the tagline,
  contact line, section headings, skill-group labels and dates. That mono/sans split is
  the whole "engineer" gesture — resist adding more.
- One accent colour (`--accent`), used for links, list markers, the `//` heading prefix,
  role names and the bold skill-group labels. Never for body text.
- Dark by default; light mode via `prefers-color-scheme`. Both are defined as CSS variable
  overrides on `:root` — restyle by editing variables, not rules.
- Body 14px / 1.5 on screen. Do not go below 13px.

## Print / PDF export

The `@media print` block is the PDF renderer. It flips to a light, ink-friendly palette,
drops to ~10.2px / 1.42, removes the page chrome, and sets 12–13mm page margins.

- Export: Cmd/Ctrl-P → Save as PDF, Letter or A4, margins "Default". Nothing on the page
  carries a background any more, so the "background graphics" toggle no longer matters.
- `.entry` and `dl.skills` carry `break-inside: avoid`; `h2` carries `break-after: avoid`.
  Add the same to any new block-level construct, or it will split across a page break.
- Always open the print preview before shipping. A page that looks like a tidy one-pager
  on screen and spills three lines onto page 2 in print is the default failure mode here.

## Editing

- Placeholders are `&lt;ANGLE BRACKETS&gt;` — HTML-escaped, so they render literally.
  Escape `<`, `>` and `&` in any content you write in (company names especially).
- Delete unused sections wholesale; do not leave empty `<section>` elements, which still
  emit a heading into extracted text.
- Set `<title>` to `<Name> — <Target Role>`; it becomes the browser tab and the default
  PDF filename.
- Verify extraction the same way as LaTeX: save to PDF, then
  `pdftotext cv.pdf - | head -40`, and confirm the order and accents survive.
