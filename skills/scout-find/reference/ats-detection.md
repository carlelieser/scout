# ATS Platform Detection

Detect the ATS platform from the job URL:

| URL pattern | `ats_platform` value |
|---|---|
| `boards.greenhouse.io` or `job-boards.greenhouse.io` | `"greenhouse"` |
| `jobs.lever.co` | `"lever"` |
| `myworkdayjobs.com` or `wd5.myworkday.com` | `"workday"` |
| `icims.com` | `"icims"` |
| `ashbyhq.com` | `"ashby"` |
| `jobs.smartrecruiters.com` | `"smartrecruiters"` |
| URL containing `taleo` | `"taleo"` |
| No match | `"unknown"` |
