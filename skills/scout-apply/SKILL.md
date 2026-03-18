---
name: scout-apply
description: "Use when generating tailored application materials (CV, cover letter, email) for a specific job. Activates when user wants to prepare or create application documents."
---

# Scout Apply

Generates tailored, ATS-compliant application materials for a specific job listing, using your master profile as the source of truth.

## Prerequisites

Requires:
- `~/.scout/profile/master-profile.md` — if missing, direct user to run `/scout-profile`
- `~/.scout/profile/master-cv.md` — if missing, direct user to run `/scout-profile`
- Target job file in `~/.scout/jobs/` — if missing, direct user to run `/scout-find`

If the job has not been vetted (no `score` in frontmatter), offer: "This job hasn't been vetted yet. Want me to evaluate it first? (Run `/scout-vet <job-id>`)" Proceed regardless of answer.

## Input

- **User specifies a job ID** → generate materials for that job
- **No job specified** → present list of jobs with status `"vetted"` or `"discovered"` for selection

## Re-run Behavior

If `~/.scout/applications/<job-id>/` already has files, ask the user:
- **Regenerate** — overwrite existing materials
- **Edit** — show existing materials and ask what to change

## Generation Pipeline

### Step 1: Read Inputs

Read the job file (`~/.scout/jobs/<job-id>.md`) and the master CV (`~/.scout/profile/master-cv.md`) and profile (`~/.scout/profile/master-profile.md`).

If vetting results exist (the job file has `score` and talking points from a prior `/scout-vet` run), use the talking points to inform which experiences to emphasize.

### Step 2: Honesty Check

Compare the job's requirements against the user's actual skills and experience. Categorize each requirement as:
- **Strong match** — user clearly has this skill/experience
- **Partial match** — user has related experience
- **Gap** — user does not have this skill

Rules:
- Only include keywords/skills the user's profile actually supports
- Never fabricate experience or skills
- If the job requires skills the user lacks, these become "stretch" items — do not include them in the CV as if the user has them
- If more than 50% of required skills are gaps, warn the user this may be a stretch application

### Step 3: Generate Tailored CV

Create `~/.scout/applications/<job-id>/cv.md`. Restructure the master CV for this specific role:

**ATS Compliance Rules (mandatory):**

Section headers — use ONLY these standard headers:
- "Summary" or "Professional Summary"
- "Experience" or "Work Experience"
- "Education"
- "Skills"
- "Certifications" (if applicable)

Formatting:
- No columns, tables, text boxes, icons, or images
- No content in headers or footers — all content in document body
- Single-column layout only
- Dates in consistent format: "Mon YYYY – Mon YYYY" (e.g., "Jan 2022 – Mar 2024")
- Contact info at the top, in the body (not a header)
- First line of the file is the candidate's full name

Keywords:
- Include both acronyms AND expanded forms (e.g., "Machine Learning (ML)")
- List skills in a dedicated Skills section AND contextually in experience bullets
- No skill/keyword should appear more than 3 times across the entire CV
- Only include keywords the candidate genuinely possesses (from honesty check)

Encoding:
- ASCII-safe characters only
- Straight quotes (`'` and `"`), regular hyphens (`-`), standard asterisks (`*`) for bullets
- No curly quotes, em dashes, or special bullet symbols

Job title normalization:
- If the user's actual title was non-standard (e.g., "Growth Ninja"), include the industry-standard equivalent: "Growth Ninja (Marketing Manager)"

Length:
- 1 page for < 5 years experience
- 2 pages for 5-15 years
- 3 pages only for executive/academic roles

Content prioritization:
- Reorder experience bullets to lead with achievements most relevant to this job
- Quantify achievements wherever the master CV provides data (numbers, percentages, revenue, team size)
- Trim or omit experience that is entirely irrelevant to this role
- Skills section lists the most relevant skills first

### Step 4: Generate Cover Letter (conditional)

Generate a cover letter ONLY if:
- The job listing explicitly requests one, OR
- The user explicitly asks for one

If generating, create `~/.scout/applications/<job-id>/cover-letter.md`:
- Addressed to the company (hiring manager name if available from the listing)
- Opening paragraph: why this specific role at this specific company
- Body: 2-3 most relevant achievements mapped to the job requirements
- Closing: enthusiasm + call to action
- Tone: professional but not generic. Reference specific things about the company.
- Length: under 400 words

### Step 5: Generate Email Draft (conditional)

If the job's `application_method` is `"email"`, create `~/.scout/applications/<job-id>/email-draft.md`:
- Subject line: "Application: [Job Title] — [Candidate Name]"
- Brief body (3-4 sentences): who you are, why you're interested, what's attached
- Sign-off with contact info

### Step 6: Generate Application Answers (conditional)

If the job listing includes specific application questions (e.g., "Why do you want to work here?", "Describe a time you led a team"), create `~/.scout/applications/<job-id>/qa.md`:
- Each question as a heading
- Tailored answer drawing from the user's profile and experience
- Use STAR format for behavioral questions

### Step 7: Export to PDF

Convert the tailored CV to PDF:

**Primary method — Pandoc:**
```bash
pandoc ~/.scout/applications/<job-id>/cv.md -o ~/.scout/applications/<job-id>/cv.pdf --pdf-engine=xelatex -V geometry:margin=1in -V mainfont="Helvetica Neue" -V monofont="Menlo"
```

**Fallback — if Pandoc is not available**, use Playwright to print-to-PDF:
1. Render the Markdown as HTML
2. Use Playwright to open the HTML and print to PDF

If neither method works, inform the user and skip PDF generation.

### Step 8: Update Job Status

Update the job file's frontmatter: set `status: "materials-ready"` and `date_materials_generated: "YYYY-MM-DD"`.

Do NOT set `status: "applied"`. The user must explicitly confirm submission via `/scout-track update`.

### Step 9: Append to History

```
- YYYY-MM-DD HH:MM | scout-apply | materials-ready | <job-id> | "CV + [cover letter] + [email] generated"
```

## User Templates

If `~/.scout/templates/cv-template.md` exists, use it as the structural template for the CV (section order, formatting preferences). If `~/.scout/templates/cover-letter-template.md` exists, use it for cover letter tone and structure.

## Completion

After generating materials, inform the user:

> "Application materials ready at `~/.scout/applications/<job-id>/`:
> - `cv.md` + `cv.pdf` — tailored CV
> - [cover-letter.md — if generated]
> - [email-draft.md — if generated]
> - [qa.md — if generated]
>
> Review the materials, then run `/scout-track update <job-id>` to mark as applied after you submit."
