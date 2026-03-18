---
name: scout-find
description: "Use when discovering job listings from URLs, pasted text, or web searches. Activates when user wants to find, save, or import job opportunities."
---

# Scout Find

Discovers and normalizes job listings from any source, saving them to `~/.scout/jobs/`.

## Data Directory

Job files are saved to `~/.scout/jobs/`. Create the directory if it doesn't exist.

## Input Mode Detection

Determine mode from context:
- **User provides a URL** → URL mode
- **User pastes job description text** → pasted text mode
- **User describes what they're looking for** (e.g., "senior PM roles in fintech") → search mode

## URL Mode

1. Use the `browser-automation` skill to fetch the URL with Playwright. Navigate to the URL in a headless browser, wait for the main content to render, and extract the full text content of the page. This is necessary because most job boards render content via JavaScript.
2. Detect the ATS platform from the URL:
   - `boards.greenhouse.io` or `job-boards.greenhouse.io` → `"greenhouse"`
   - `jobs.lever.co` → `"lever"`
   - `myworkdayjobs.com` or `wd5.myworkday.com` → `"workday"`
   - `icims.com` → `"icims"`
   - `ashbyhq.com` → `"ashby"`
   - `jobs.smartrecruiters.com` → `"smartrecruiters"`
   - URLs containing `taleo` → `"taleo"`
   - Otherwise → `"unknown"`
3. Parse the extracted text into the normalized format (see below).
4. Generate the job ID and save the file.

## Pasted Text Mode

1. Parse the pasted text directly into the normalized format.
2. Ask the user for the source URL if they have it (optional).
3. Generate the job ID and save the file.

## Search Mode

1. If `~/.scout/profile/preferences.md` exists, read it to inform search queries.
2. Construct search queries from the user's description + profile preferences (if available). Use `WebSearch` to search major job boards.
3. Present results as a numbered list: title, company, location, salary (if available).
4. User selects which ones to save.
5. For each selected result, fetch the full listing via URL mode.
6. Deduplicate against existing files in `~/.scout/jobs/` by `source_url`.

## Job ID Generation

Format: `YYYY-MM-DD-<company>-<title>`

**Slugification rules:**
- Lowercase all characters
- Replace non-alphanumeric characters with hyphens
- Collapse multiple consecutive hyphens into one
- Strip leading/trailing hyphens
- Transliterate accented characters to ASCII (e.g., "Societe Generale" → `societe-generale`)
- Max 60 characters for the slug portion (after the date prefix)

**Collision handling:** If `~/.scout/jobs/<id>.md` already exists, append `-2`, `-3`, etc.

## Normalized Job File Format

Save to `~/.scout/jobs/<job-id>.md`. ALL string values in frontmatter MUST be quoted.

```yaml
---
id: "2026-03-18-acme-senior-pm"
title: "Senior Product Manager"
company: "Acme Corp"
location: "Remote (US)"
salary:
  min: 150000
  max: 180000
  currency: "USD"
  period: "annual"
  notes: "Plus equity and annual bonus"
type: "full-time"
seniority: "senior"
application_method: "portal"
application_url: "https://..."
source_url: "https://..."
ats_platform: "greenhouse"
date_found: "2026-03-18"
date_posted: "2026-03-10"
deadline: null
status: "discovered"
score: null
recommendation: null
contacts: []
follow_up_date: null
rejection_reason: null
rejection_stage: null
---

## Description
[Normalized job description]

## Requirements
[Extracted requirements — as a bulleted list]

## Nice-to-haves
[Extracted preferred qualifications — as a bulleted list]

## Benefits
[Extracted benefits/perks — as a bulleted list]

## How to Apply
[Application instructions extracted from listing]
```

### Salary Normalization

- If salary is a range (e.g., "$150k-$180k"), set `min` and `max` accordingly.
- If salary is a single number, set `min` and `max` equal.
- If salary is "Competitive" or undisclosed, set all numeric fields to `null` and put the raw text in `notes`.
- Normalize currency and period. If the listing says "EUR 80,000", set `currency: "EUR"`, `period: "annual"`.
- If hourly/monthly, convert to annual equivalent and note the original in `notes`.

### Application Method Detection

Determine from the listing:
- ATS portal with "Apply" button → `"portal"`
- "Send your CV to email@..." → `"email"`
- LinkedIn Easy Apply → `"linkedin-easy-apply"`
- Recruiter contact → `"recruiter"`
- Default → `"portal"`

## Append to History

After saving each job, append to `~/.scout/history.md`:
```
- YYYY-MM-DD HH:MM | scout-find | discovered | <job-id> | "<source description>"
```

## Completion

After saving job(s), inform the user:

> "Saved [N] job(s) to `~/.scout/jobs/`. Run `/scout-vet` to evaluate them against your profile, or `/scout-find` again to discover more."
