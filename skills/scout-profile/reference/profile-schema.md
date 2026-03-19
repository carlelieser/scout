# Output File Schemas

## `master-profile.md`

Structured profile with YAML frontmatter. ALL string values must be quoted.

```yaml
---
name: "Jane Doe"
title: "Senior Product Manager"
years_experience: 8
skills:
  - name: "Product Strategy"
    proficiency: "expert"
  - name: "SQL"
    proficiency: "intermediate"
contact:
  email: "jane@example.com"
  phone: "+1-555-0100"
  linkedin: "linkedin.com/in/janedoe"
  portfolio: "janedoe.com"
---

## Summary
[Elevator pitch — 2-3 sentences]

## Work History

### [Job Title] — [Company]
**[Start Date] – [End Date]**

- [Achievement with quantified impact]
- [Achievement with quantified impact]
- [Achievement with quantified impact]

### [Previous Job Title] — [Company]
...

## Education

### [Degree] — [Institution]
**[Graduation Year]**

## Certifications
- [Certification name] ([Year])
```

## `master-cv.md`

A comprehensive, unabridged CV in Markdown containing ALL experience, skills, and achievements. This is the source of truth that `/scout-apply` tailors from. It should include everything — even old or less relevant experience — because different jobs may need different subsets.

Format it as a clean, complete CV document (not a data file). Start with the candidate's name, then contact info, then sections: Summary, Experience, Education, Skills, Certifications.

## `preferences.md`

Structured preferences used by `/scout-vet` for scoring and filtering.

```yaml
---
target_roles:
  - "Product Manager"
  - "Senior Product Manager"
target_industries:
  - "fintech"
  - "healthtech"
seniority: "senior"
location:
  preferred: "Remote"
  acceptable: ["Remote", "Hybrid (NYC)"]
  dealbreaker_if: "fully onsite"
remote_policy:
  preferred: "fully remote"
  acceptable: ["fully remote", "hybrid"]
salary:
  minimum: 150000
  preferred: 180000
  currency: "USD"
  period: "annual"
company_size:
  preferred: "50-500"
  dealbreaker_if: "pre-seed/unfunded"
visa_sponsorship_required: false
dealbreakers:
  - "fully onsite"
  - "pre-seed/unfunded startup"
  - "no salary disclosed and below minimum after negotiation"
---
```
