---
name: scout-profile
description: "Use when building, updating, or reviewing your job search profile, master CV, or job preferences. Activates when user wants to set up or modify their professional profile for job hunting."
---

# Scout Profile

Builds and maintains your master professional profile at `~/.scout/profile/`. This profile is the foundation that `/scout-vet` and `/scout-apply` depend on.

## Data Directory

All profile data lives at `~/.scout/profile/`. Create the directory if it doesn't exist.

## Input Mode Detection

Determine mode from context:
- **Profile exists** (`~/.scout/profile/master-profile.md` found) → update mode
- **User provides a file or pasted CV** → parse-and-build mode
- **Nothing provided, no profile exists** → interview mode

## Update Mode

If `master-profile.md` already exists:
1. Read and present the current profile summary to the user.
2. Ask what they want to update: skills, experience, preferences, or something else.
3. Make targeted updates. Never overwrite the entire profile without confirmation.
4. For contact field changes (name, email, phone, URLs), follow the propagation rules in [reference/cascading-updates.md](reference/cascading-updates.md).

## Parse-and-Build Mode

If the user provides an existing CV (file path or pasted text):
1. Parse the document and extract structured data: name, contact info, skills, work history, education.
2. Present what was extracted and ask the user to confirm accuracy.
3. Ask follow-up questions for any gaps — one at a time, prefer multiple choice.
4. Write the three output files per schemas in [reference/profile-schema.md](reference/profile-schema.md).

## Interview Mode

If no profile exists and no document is provided, conduct an interview — one question at a time, prefer multiple choice where possible:

1. **Professional summary** — What is your current or most recent role? How many years of experience do you have? Give me a 2-3 sentence elevator pitch.
2. **Skills** — What are your top technical skills? Rate each as beginner/intermediate/advanced/expert. What are your key soft skills?
3. **Work history** — For each role (starting with most recent): company, title, dates, 3-5 key achievements. Push for quantified achievements (numbers, percentages, revenue, team size).
4. **Education & certifications** — Degrees, institutions, graduation years. Relevant certifications with dates.
5. **Job preferences** — What roles are you targeting? What industries? What seniority level?
6. **Deal-breakers** — What is your minimum acceptable salary? (Ask for currency and whether annual/monthly.) Location requirements? Remote/hybrid/onsite preference? Company size constraints? Visa sponsorship needs?
7. **Optional extras** — Portfolio URL? LinkedIn? GitHub? Publications? Languages spoken?

## Output Files

Write three files per schemas in [reference/profile-schema.md](reference/profile-schema.md).

## Completion

After writing all three files, inform the user:

> "Profile created at `~/.scout/profile/`. You can now use `/scout-find` to discover jobs, `/scout-vet` to evaluate them against your profile, or `/scout-apply` to generate tailored application materials."

## History Append

Append per [../shared/history-format.md](../shared/history-format.md).
