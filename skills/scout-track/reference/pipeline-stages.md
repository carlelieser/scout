# Pipeline Stages

```
discovered → vetted → materials-ready → applied → interviewing → offer → accepted/rejected
                                                                      ↘ withdrawn
                                    (any stage) → archived
```

## Status Ownership

Only `scout-track` can set these statuses:
- `applied` — user confirms they submitted the application
- `interviewing` — user has an interview scheduled or completed
- `offer` — user received an offer
- `accepted` — user accepted an offer
- `rejected` — user was rejected (at any stage)
- `withdrawn` — user withdrew their application
- `archived` — user wants to remove from active pipeline

Other skills set: `discovered` (scout-find), `vetted` (scout-vet), `materials-ready` (scout-apply).

## Implicit Status Updates

When `scout-track` is invoked as a result of a casual user confirmation (e.g., "I applied!" rather than `/scout-track update`), it is acceptable to streamline the prompts:
- If the context makes the job ID obvious (e.g., materials were just generated for a specific job), use that job ID without asking.
- If the new status is obvious from context (e.g., "I applied" = `applied`), set it without asking.
- Still prompt for any non-obvious metadata: application method, notes.
- If the user provides no additional detail and seems eager to move on, use reasonable defaults: `application_method` from the job file, `date_applied` as today, no notes.
