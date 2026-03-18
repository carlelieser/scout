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

```
discovered → vetted → materials-ready → applied → interviewing → offer → accepted/rejected
                                                                      ↘ withdrawn
                                    (any stage) → archived
```

### Status Ownership

Only `scout-track` can set these statuses:
- `applied` — user confirms they submitted the application
- `interviewing` — user has an interview scheduled or completed
- `offer` — user received an offer
- `accepted` — user accepted an offer
- `rejected` — user was rejected (at any stage)
- `withdrawn` — user withdrew their application
- `archived` — user wants to remove from active pipeline

Other skills set: `discovered` (scout-find), `vetted` (scout-vet), `materials-ready` (scout-apply).

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
   - Jobs needing follow-up (see Follow-up Cadence below)
   - Stale listings: `discovered` or `vetted` jobs where `date_posted` is 30+ days ago
   - Approaching deadlines: jobs with a `deadline` within 5 days
4. **Recent activity:** Last 10 entries from `~/.scout/history.md`
5. **Quick stats:** Response rate (applied → interviewing), average time in each stage

## Mode: Update (`/scout-track update`)

1. If user specifies a job ID, use it. Otherwise, present jobs grouped by stage for selection.
2. Show current status and ask for the new status.
3. Based on the new status, prompt for additional information:

| New Status | Prompt For |
|------------|-----------|
| `applied` | Application method used, date submitted, any notes |
| `interviewing` | Interview date, interviewer name/title, interview type (phone/video/onsite), follow-up date |
| `offer` | Offer details (salary, equity, benefits, deadline to respond) |
| `accepted` | Start date, final package details |
| `rejected` | Rejection stage (screening/phone/onsite/final), reason if given |
| `withdrawn` | Reason for withdrawing |
| `archived` | Reason for archiving |

4. Update the job file's frontmatter with new status and any additional fields.
5. Append to `~/.scout/history.md`.

## Mode: Contacts (`/scout-track contacts`)

1. If user specifies a job ID, show/edit contacts for that job. Otherwise, ask which job.
2. Add or update contacts in the job file's `contacts` frontmatter array:

```yaml
contacts:
  - name: "Jane Smith"
    role: "Engineering Manager"
    linkedin: "linkedin.com/in/janesmith"
    relationship: "referral"
    last_interaction: "2026-03-15"
    notes: "Met at conference, offered to refer"
```

3. Track who referred you for thank-you follow-ups.

## Mode: Follow-ups (`/scout-track followups`)

Scan all active jobs and apply stage-aware follow-up cadence:

| Stage | Follow-up Rule |
|-------|---------------|
| `applied` | Follow up after 7-10 business days if no response |
| `interviewing` | Send thank-you within 24 hours. Follow up after 5 business days if no response. |
| `offer` | Respond within stated deadline (default 3-5 business days) |
| `materials-ready` | Remind to submit after 3 days |

For each job, check:
- Does it have a `follow_up_date` in the past? → due for follow-up
- Does the stage imply a follow-up based on the rules above? → calculate from `date_applied`, interview dates, etc.

Present as a list:
```
Follow-ups Due:
- [Job Title] at [Company] (applied 2026-03-08) — follow up on application (10 days)
- [Job Title] at [Company] (interviewed 2026-03-15) — send thank-you note (overdue!)
```

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

All status updates append to `~/.scout/history.md`:
```
- YYYY-MM-DD HH:MM | scout-track | <new-status> | <job-id> | "<notes>"
```
