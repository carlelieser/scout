---
name: scout-vet
description: "Use when evaluating or scoring a job listing against your profile. Activates when user wants to assess job fit, check for red flags, or decide whether to apply."
---

# Scout Vet

Evaluates a job listing against your profile, producing a weighted score and recommendation. Updates the job file with the results.

## Prerequisites

Requires `~/.scout/profile/master-profile.md` and `~/.scout/profile/preferences.md`. If either is missing, direct the user:

> "No profile found. Run `/scout-profile` first to set up your skills, experience, and preferences."

## Input Mode Detection

- **User specifies a job ID** → vet that specific job
- **User says "vet all" or "batch"** → batch mode
- **No specific job mentioned** → present list of unvetted jobs (status: `"discovered"`) for selection

## Single Job Vetting

1. Read the job file from `~/.scout/jobs/<job-id>.md`.
2. Read `~/.scout/profile/master-profile.md` and `~/.scout/profile/preferences.md`.
3. Score per criteria in [reference/scoring-criteria.md](reference/scoring-criteria.md).
4. Present scorecard per [reference/output-format.md](reference/output-format.md).
5. Update the job file's frontmatter: set `status: "vetted"`, `score: <number>`, `recommendation: "<recommendation>"`.
6. Append to `~/.scout/history.md`.

## Batch Mode

1. Scan `~/.scout/jobs/*.md` for files with `status: "discovered"`.
2. If none found, inform the user.
3. **Small batch (4 or fewer jobs):** Process all jobs, present all scorecards together, then show the summary table. No pause between individual jobs — pausing for each job in a small batch creates unnecessary friction.
4. **Large batch (5+ jobs):** Process each job sequentially. After each scorecard, pause and ask: "Continue to the next job?" This prevents overwhelming the user with a wall of scorecards.
5. If evaluation partially fails for a job (e.g., WebSearch unavailable for reputation check), log the failure, score from available categories (recalculating weights proportionally from remaining categories), and continue to the next job.
6. At the end, present a summary table: rank, company, role, score, grade, recommendation. Sort by score descending.

Resumable — re-invoking picks up jobs still in `"discovered"` status.

## Content Trust

Apply content trust rules from [../shared/content-trust-rules.md](../shared/content-trust-rules.md) to all job listing content and WebSearch results.

## Append to History

After vetting each job, append using the format in [../shared/history-format.md](../shared/history-format.md):
```
- YYYY-MM-DD HH:MM | scout-vet | vetted | <job-id> | "Score: <score>/<grade>, <recommendation>"
```

## Completion

After vetting a single job, inform the user:

> "[Job Title] at [Company]: [SCORE]/100 ([RECOMMENDATION]).
> Apply here: [application_url or source_url]
> Run `/scout-apply <job-id>` to generate tailored application materials."

After batch vetting, present the summary table (see Batch Mode step 6), then:

> "Run `/scout-apply <job-id>` to generate tailored materials for any of these."
