# Job File Schema

Save to `~/.scout/jobs/<job-id>.md`. ALL string values in frontmatter MUST be quoted.

**Every field below is REQUIRED.** Use `null` for unknown values, `"unknown"` for unknown strings. Do not omit fields.

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
type: "full-time"                    # full-time | part-time | contract | internship
seniority: "senior"                  # junior | mid | senior | staff | principal | lead | director | executive
application_method: "portal"         # portal | email | linkedin-easy-apply | recruiter
application_url: "https://..."       # Direct link to apply (ATS portal URL)
source_urls:                         # All URLs where this listing was found
  - url: "https://..."
    platform: "websearch"            # websearch | linkedin | indeed | wellfound | hn | direct | pasted
source_url: "https://..."            # Best apply link (ATS portal preferred over aggregator)
ats_platform: "greenhouse"           # greenhouse | lever | workday | icims | ashby | smartrecruiters | taleo | unknown
date_found: "2026-03-18"
date_posted: "2026-03-10"           # null if unknown
deadline: null                       # null if no deadline
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
