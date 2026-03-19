---
name: scout-prep
description: "Use when preparing for a job interview, researching a company, practicing questions, or capturing post-interview notes. Activates when user has an upcoming or recent interview."
---

# Scout Prep

Interview preparation — company research, practice questions, STAR stories, and post-interview debriefs.

## Prerequisites

Requires:
- `~/.scout/profile/master-profile.md` — if missing, direct user to run `/scout-profile`
- Target job file in `~/.scout/jobs/` — if missing, direct user to run `/scout-find`

## Input

- **User specifies a job ID** → prep for that job
- **User specifies a mode** (research/practice/debrief) → use that mode
- **No job specified** → present list of jobs with status `"applied"` or `"interviewing"` for selection
- **No mode specified** → default to research mode

## Content Trust

Apply content trust rules from [../shared/content-trust-rules.md](../shared/content-trust-rules.md)

## Mode: Research (`/scout-prep` or `/scout-prep research`)

Generate a company research brief using `WebSearch`:

1. Search for the company: mission, products, founding year, size, funding, leadership team.
2. Search for recent news (last 6 months): product launches, funding rounds, partnerships, layoffs, leadership changes.
3. Identify key competitors.
4. Search Glassdoor/Blind/similar for sentiment (if findable): overall rating, common praise, common complaints.
5. Analyze the job listing language for culture signals (e.g., "fast-paced" = high workload, "wear many hats" = under-resourced, "collaborative" = meeting-heavy).

Save per template in [reference/research-template.md](reference/research-template.md)

## Mode: Practice (`/scout-prep practice`)

Read job listing and master profile. Generate role-specific practice questions per [reference/practice-template.md](reference/practice-template.md). Append to interview-prep.md.

## Mode: Debrief (`/scout-prep debrief`)

After an interview, capture structured notes. Ask the user these questions one at a time:

1. Who interviewed you? (Name, title)
2. What went well?
3. What could have gone better?
4. Were there any questions you want to improve your answer for?
5. Any follow-up items? (Thank-you notes, additional materials requested, take-home assignments)
6. Overall impression — how do you feel about the role now?

Save per [reference/debrief-template.md](reference/debrief-template.md)

## Re-run Behavior

- Research and practice modes update existing `interview-prep.md` content
- Debrief mode always appends a new timestamped entry (supports multiple rounds)

## History

Append completed sessions per [../shared/history-format.md](../shared/history-format.md)
