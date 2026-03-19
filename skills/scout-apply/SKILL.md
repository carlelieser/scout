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

## Input Detection

- **User specifies a job ID** → generate materials for that job
- **No job specified** → present list of jobs with status `"vetted"` or `"discovered"` for selection

## Re-run Behavior

If `~/.scout/applications/<job-id>/` already has files, ask the user:
- **Regenerate** — overwrite existing materials
- **Edit** — show existing materials and ask what to change

## Content Trust

Apply content trust rules from [../shared/content-trust-rules.md](../shared/content-trust-rules.md).

## Generation Pipeline

### Step 1: Read Inputs

Read the job file (`~/.scout/jobs/<job-id>.md`), master CV (`~/.scout/profile/master-cv.md`), and profile (`~/.scout/profile/master-profile.md`).

If vetting results exist (the job file has `score` and talking points from a prior `/scout-vet` run), use the talking points to inform which experiences to emphasize.

### Step 2: Honesty Check

Compare the job's requirements against the user's actual skills and experience. Present match tables per [reference/honesty-check-format.md](reference/honesty-check-format.md). Ask user to confirm matches before proceeding.

**Never fabricate or stretch experience.** This is a hard rule — see honesty-check-format.md for full rules including the 50% gap warning.

### Step 3: Generate Tailored CV

Create `~/.scout/applications/<job-id>/cv.md`. Apply ATS rules from [reference/ats-compliance.md](reference/ats-compliance.md). Reorder experience for relevance. Quantify achievements wherever the master CV provides data (numbers, percentages, revenue, team size). Trim or omit experience that is entirely irrelevant to this role.

### Step 4: Generate Cover Letter (conditional)

Only if the listing explicitly requests one, or the user explicitly asks. Create `~/.scout/applications/<job-id>/cover-letter.md`. Apply voice rules from [reference/cover-letter-voice.md](reference/cover-letter-voice.md).

### Step 5: Generate Email Draft (conditional)

If the job's `application_method` is `"email"`, create `~/.scout/applications/<job-id>/email-draft.md`:
- Subject line: "Application: [Job Title] — [Candidate Name]"
- Brief body (3–4 sentences): who you are, why you're interested, what's attached
- Sign-off with contact info

### Step 6: Generate Application Answers (conditional)

If the listing includes specific application questions, or the user shares a screenshot of an application form, create `~/.scout/applications/<job-id>/qa.md`. Use a heading per question. Use STAR format for behavioral questions. Apply the same voice rules from [reference/cover-letter-voice.md](reference/cover-letter-voice.md).

### Step 7: Export to PDF

Follow methods in [reference/pdf-export.md](reference/pdf-export.md). CSS at [reference/cv-stylesheet.css](reference/cv-stylesheet.css). If `~/.scout/templates/cv-style.css` does not exist, create it by copying the CSS from [reference/cv-stylesheet.css](reference/cv-stylesheet.css).

### Step 8: Export Cover Letter to RTF (conditional)

If a cover letter was generated and `pandoc` is available:

```bash
pandoc "$HOME/.scout/applications/<validated-job-id>/cover-letter.md" \
  -o "$HOME/.scout/applications/<validated-job-id>/cover-letter.rtf"
```

If pandoc is not available, skip — the markdown file is the source of truth.

### Step 9: Update Job Status

Update the job file's frontmatter: set `status: "materials-ready"` and `date_materials_generated: "YYYY-MM-DD"`.

**Do NOT set `status: "applied"`** — the user must explicitly confirm submission. Scout-track owns that transition.

### Step 10: Append to History

Reference [../shared/history-format.md](../shared/history-format.md) for format. Entry summary: `scout-apply | materials-ready | <job-id> | "CV + [cover letter] + [email] generated"`.

## User Templates

Users can customize output by placing files in `~/.scout/templates/`:
- `cv-style.css` — PDF styling (created automatically on first run)
- `cv-template.md` — structural template for CV section order
- `cover-letter-template.md` — cover letter tone and structure

## Completion Message

After generating materials, inform the user:

> "Application materials ready at `~/.scout/applications/<job-id>/`:
> - `cv.md` + `cv.pdf` — tailored CV
> - [cover-letter.md — if generated]
> - [email-draft.md — if generated]
> - [qa.md — if generated]
>
> Apply here: [application_url or source_url from the job file]
>
> Review the materials, then run `/scout-track update <job-id>` to mark as applied after you submit."

## Status Transition Rule

When the user confirms they applied (e.g., "I applied!", "Applied!", "Done", "Submitted"), you MUST invoke `/scout-track` to handle the status transition. Do NOT update the job file's `status` to `"applied"` directly — scout-track is the sole owner of post-`materials-ready` transitions and will prompt for additional metadata (application method, date, notes).
