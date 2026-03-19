---
name: scout-track
description: "Use when checking job application status, updating pipeline stages, tracking follow-ups, viewing contacts, or reviewing job search statistics. Activates when user wants to manage their application pipeline."
---

# Scout Track

Manages the job application pipeline — status updates, follow-up reminders, contacts, and search statistics. This is the sole owner of pipeline transitions beyond `materials-ready`.

## Pipeline State

There is no tracker file. Pipeline state is derived at query time by scanning YAML frontmatter across all `~/.scout/jobs/*.md` files. Each job file is the single source of truth for its own status.

To get pipeline state: read all files in `~/.scout/jobs/`, extract the `status` field from each file's YAML frontmatter.

## Pipeline Stages

See [reference/pipeline-stages.md](reference/pipeline-stages.md) for stage definitions and ownership rules.

> **IMPORTANT:** Other scout skills (especially `scout-apply`) must NOT update job status to any stage owned by `scout-track`. When a user confirms they applied (e.g., "I applied!", "Applied!", "Done", "Submitted") during a `scout-apply` session, the agent must invoke `/scout-track` to handle the transition rather than directly editing the job file's status field. This ensures the update flow prompts for metadata (application method, date, notes) and appends to history consistently.

## Mode: Dashboard (default)

When invoked with no arguments or `/scout-track`, show the dashboard:

1. Scan all `~/.scout/jobs/*.md` files.
2. Group by status and present counts:
   ```
   Pipeline Summary:
   - Discovered: 5
   - Vetted: 3
   - Materials Ready: 2
   - Applied: 4
   - Interviewing: 1
   - Offers: 0
   - Archived: 8
   ```
3. **Action items:**
   - Jobs needing follow-up (apply cadence rules from [reference/follow-up-cadence.md](reference/follow-up-cadence.md))
   - Stale listings: `discovered` or `vetted` jobs where `date_posted` is 30+ days ago
   - Approaching deadlines: jobs with a `deadline` within 5 days
4. **Recent activity:** Last 10 entries from `~/.scout/history.md`
5. **Quick stats:** Response rate (applied → interviewing), average time in each stage

## Mode: Update (`/scout-track update`)

1. If user specifies a job ID, use it. Otherwise, present jobs grouped by stage for selection.
2. Show current status and ask for the new status.
3. Prompt for additional information based on new status — see [reference/status-prompts.md](reference/status-prompts.md).
4. Update the job file's frontmatter with new status and any additional fields.
5. Append to `~/.scout/history.md` using the format in [../shared/history-format.md](../shared/history-format.md).

## Mode: Contacts (`/scout-track contacts`)

1. If user specifies a job ID, show/edit contacts for that job. Otherwise, ask which job.
2. Add or update contacts in the job file's `contacts` frontmatter array — see [reference/contacts-schema.md](reference/contacts-schema.md) for format.
3. Track who referred you for thank-you follow-ups.

## Mode: Follow-ups (`/scout-track followups`)

1. Scan all active jobs.
2. Apply cadence rules from [reference/follow-up-cadence.md](reference/follow-up-cadence.md).
3. Present a list of jobs due for follow-up action.

## Mode: History (`/scout-track history`)

Display the contents of `~/.scout/history.md`. If the user asks for a specific job's history, filter entries by job ID.

### History Rotation

Before appending to `history.md`, check its line count. If it exceeds 500 lines:
1. Move all entries except the most recent 200 to `~/.scout/history-archive-YYYY.md` (using the current year).
2. If the archive file exists, append to it.

## Mode: Stats (`/scout-track stats`)

Scan all job files and compute:

- **Total by status:** count per stage
- **Response rate:** % of `applied` jobs that reached `interviewing` or later
- **Average time per stage:** mean days spent in each status
- **Rejection analysis:** count by `rejection_stage` (screening/phone/onsite/final)
- **Application method effectiveness:** response rate broken down by `application_method`
- **Score correlation:** average vet score for jobs that progressed vs. jobs that didn't
- **Weekly/monthly activity:** applications submitted per week/month

Present as a formatted report.

## Append to History

All status updates append to `~/.scout/history.md` using the format in [../shared/history-format.md](../shared/history-format.md).
