# LaTeX template preferences

Layout and typography rules for `resume-template.tex` and `cover-letter-template.tex`.
Content rules live in `../master-template-preferences.md` and
`../cover-letter-preferences.md` — do not restate them here.

This backend is the **sober, standard** one: it is what gets sent to recruiters and
uploaded to ATS portals. When a choice is between "distinctive" and "boring and
parseable", pick boring.

## Base

- Derived from Jake Gutierrez's resume template (MIT) via sb2nov. Keep the attribution
  comment at the top of the file.
- `extarticle` at `9pt` for a dense one-pager; step up to `10pt`/`11pt` (plain `article`
  works at those sizes) when content is sparse rather than letting whitespace pool.
- `letterpaper`. Switch to `a4paper` only if the target market expects it — European
  recruiters rarely care, but the tighter A4 width changes line breaks, so re-check the
  page fit after switching.
- Build with `pdflatex` (twice when hyperlinks are added).

## Fonts and colour

- Body: Latin Modern (`lmodern`). The name in the header uses `helvet` via `\sffamily`.
  No other font switches.
- Colour is confined to hyperlinks (`linkblue`, RGB 0,0,139). Everything else is black
  on white. No coloured section rules, no accent bars, no shading.
- `fontawesome5` icons appear only in the header contact line (phone, envelope, GitHub,
  LinkedIn). Never inside body text.

## Structure

- Section headings: small caps, `\large`, followed by `\titlerule`. Do not restyle.
- Use the provided commands rather than hand-rolling tabulars:
  - `\resumeExpHeading{Company}{Role}{Dates}{Location}` — Experience and Awards.
  - `\resumeSubSubheading{Role}{Dates}` — a second role at the same employer, nested
    under one `\resumeExpHeading`.
  - `\resumeProjectHeading{Name (context)}{Stack}` — Projects.
  - `\resumeSubheading{Institution}{Location}{Degree}{Dates}` — Education.
  - `\resumeItem{...}` inside `\resumeItemListStart` / `\resumeItemListEnd` — bullets.
- Skills and Languages use a bare `itemize` with `label={}` and bold run-in labels, not
  the heading commands.

## Fitting to one page

Apply in this order; stop as soon as it fits:

1. Cut the weakest bullet. Content first, always.
2. Tune the inter-section `\vspace{-Npt}` values already present between sections.
3. Widen the text block via the `\addtolength` margin block (currently ≈0.6in of extra
   width and ≈1.5in of extra height).
4. `\linespread` down from 1.15, no lower than 1.05.
5. Drop the base font size last, and never below 9pt — an unreadable page is worse than
   a second one.

Never fake fit by shrinking a single section's font or by pushing content into the
margins beyond the values above.

## ATS mechanics

- `\pdfgentounicode=1` plus `cmap`, `glyphtounicode`, and `[T1]{fontenc}` must all stay:
  they are what makes the generated PDF's text layer extractable. Removing any one of
  them silently produces a PDF that reads fine to a human and returns mojibake to a
  parser.
- Load `cmap` **before** `fontenc` to avoid the "fontenc already loaded" warning.
- Verify extraction after every build: `pdftotext resume.pdf - | head -40` should return
  clean, correctly ordered text with intact accents. This check is not optional.
- Ligatures (`fi`, `ffi`) and accented characters (ñ, é) must survive that round trip.
- No `multicol`, no TikZ, no images, no content in `fancyhdr` headers/footers.

## Links

- `hyperref` with `colorlinks=true` and dark blue; underline link text with `\underline{}`
  so it is still identifiable when printed in greyscale.
- Show a readable label ("GitHub", "press rel.", "link"), not a raw URL, except in the
  contact line where the address itself is the information.

## Cover letter

`cover-letter-template.tex` is the CV's sibling and must keep looking like it: same
Latin Modern body, same `\sffamily` header name, same `linkblue`, same black-on-white.
Differences are deliberate and should stay:

- `article` at `10pt` (a letter is prose, not a dense grid), roomier margins, and
  `\parskip` spacing with `\parindent=0pt` — blank lines between paragraphs, no indents.
- No `fontawesome5`: the icons buy nothing in prose and leave stray glyphs in the text
  layer.
- A `\hrule` under the identity block is the only rule in the document.
- One page is not a target here, it is a hard limit. If it overflows, cut a sentence
  from the proof paragraph — never shrink the font or the margins.
- `\pdfgentounicode=1`, `cmap`, `glyphtounicode` and `[T1]{fontenc}` matter here for the
  same reason as on the CV: portals parse the letter too.
- Switch `babel` to `spanish` when drafting in Spanish, or hyphenation breaks words wrong.

## Escaping

`&`, `%`, `$`, `#`, `_`, `{`, `}` must be escaped in body text. Watch for `&` in company
names and `%` in every metric — an unescaped `%` silently comments out the rest of the
line, which is easy to miss in a rendered PDF that still looks plausible.
