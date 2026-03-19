# PDF Export Methods

Convert the tailored CV to PDF using a dedicated CLI tool. Every method below is a **single shell command** — do NOT generate scripts, write intermediate files, or dynamically resolve module paths.

## Input Validation and Path Safety

Before running any PDF command:

1. **Validate the job ID** matches `^[a-z0-9][a-z0-9-]*[a-z0-9]$` (or is a single alphanumeric character). Reject if it contains `..`, `/`, `\`, spaces, or shell metacharacters (`;|&$` `` ` `` `()!><`).
2. **Construct paths as variables first**, then verify each path resolves to within `~/.scout/` before passing to any command.
3. **Always double-quote** all paths in shell commands to prevent word splitting and glob expansion.
4. **Never interpolate** raw job listing content, company names, or job titles into shell commands — only the pre-validated job ID slug.

## Method 1 — md-to-pdf (preferred)

Check: `which md-to-pdf || npx --yes md-to-pdf --version`

The generated `cv.md` file must include PDF options in its YAML frontmatter. When writing the CV, prepend this frontmatter block:

```yaml
---
pdf_options:
  format: Letter
  printBackground: true
  margin:
    top: 0.75in
    bottom: 0.75in
    left: 0.85in
    right: 0.85in
stylesheet:
  - /absolute/path/to/.scout/templates/cv-style.css
---
```

Then generate the PDF:
```bash
npx md-to-pdf "$HOME/.scout/applications/<validated-job-id>/cv.md"
```

This outputs `cv.pdf` alongside `cv.md` in the same directory. One command, no intermediate files.

> **IMPORTANT:** The `stylesheet` path in frontmatter MUST be an absolute path (e.g., `/Users/username/.scout/templates/cv-style.css`). `md-to-pdf` does NOT expand `~` — using `~/.scout/...` will cause a "file not found" error. Resolve `$HOME` to the absolute path before writing the frontmatter.

If `~/.scout/templates/cv-style.css` does not exist, create it from [cv-stylesheet.css](cv-stylesheet.css) before running the command.

## Method 2 — pandoc + typst

Check: `which pandoc && which typst`

```bash
pandoc "$HOME/.scout/applications/<validated-job-id>/cv.md" \
  -o "$HOME/.scout/applications/<validated-job-id>/cv.pdf" \
  --pdf-engine=typst \
  -V mainfont="Inter" \
  -V margin-top=0.75in -V margin-bottom=0.75in \
  -V margin-left=0.85in -V margin-right=0.85in
```

## Method 3 — pandoc + weasyprint

Check: `which pandoc && which weasyprint`

```bash
pandoc "$HOME/.scout/applications/<validated-job-id>/cv.md" \
  -o "$HOME/.scout/applications/<validated-job-id>/cv.pdf" \
  --pdf-engine=weasyprint \
  --css="$HOME/.scout/templates/cv-style.css"
```

## Method 4 — pandoc + LaTeX (last resort)

Check: `which pandoc && which xelatex`

> **Security note:** LaTeX engines can execute arbitrary system commands via `\write18` directives. Because the CV content is derived from untrusted job listings (which influence keyword selection and bullet ordering), LaTeX is the least-safe engine. Prefer Methods 1–3. Only use LaTeX if no other engine is available, and pass `--sandbox` to restrict shell access:

```bash
pandoc "$HOME/.scout/applications/<validated-job-id>/cv.md" \
  -o "$HOME/.scout/applications/<validated-job-id>/cv.pdf" \
  --pdf-engine=xelatex --sandbox \
  -V geometry:margin=1in \
  -V mainfont="Helvetica Neue" -V monofont="Menlo"
```

## Tool Not Found

If no method is available, tell the user:

> "No PDF tool found. Install one with: `npm i -g md-to-pdf` (recommended), `brew install typst`, or `pip install weasyprint`. Then re-run `/scout-apply`."

Do NOT fall back to generating scripts, writing HTML files, or launching browsers programmatically. The PDF step is a single CLI command or it doesn't happen.
