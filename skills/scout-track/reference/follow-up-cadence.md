# Follow-up Cadence

## Stage-Aware Rules

| Stage | Follow-up Rule |
|-------|---------------|
| `applied` | Follow up after 7-10 business days if no response |
| `interviewing` | Send thank-you within 24 hours. Follow up after 5 business days if no response. |
| `offer` | Respond within stated deadline (default 3-5 business days) |
| `materials-ready` | Remind to submit after 3 days |

## Checking Logic

For each job, check:
- Does it have a `follow_up_date` in the past? → due for follow-up
- Does the stage imply a follow-up based on the rules above? → calculate from `date_applied`, interview dates, etc.

Present as a list:
```
Follow-ups Due:
- [Job Title] at [Company] (applied 2026-03-08) — follow up on application (10 days)
- [Job Title] at [Company] (interviewed 2026-03-15) — send thank-you note (overdue!)
```
