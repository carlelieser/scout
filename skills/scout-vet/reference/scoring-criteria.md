# Scoring Criteria

## Deal-Breaker Check (Pass/Fail)

Check the job against every item in `preferences.md → dealbreakers`. ANY match = automatic **Skip** regardless of score. Common deal-breakers:
- Location violates `location.dealbreaker_if`
- Salary below `salary.minimum` (if salary is disclosed)
- Company size violates `company_size.dealbreaker_if`
- Any custom dealbreaker string matched against the job description

If a deal-breaker triggers, still calculate the score for informational purposes but set recommendation to **Skip** with the deal-breaker reason.

## Weighted Scoring (100 points)

| Category | Weight | How to Score |
|----------|--------|-------------|
| Skills match | 30% | Compare job's Requirements and Nice-to-haves against `master-profile.md` skills list. Score higher for required skill matches, partial credit for nice-to-have matches. Penalize for required skills the user lacks. |
| Experience match | 15% | Compare job's seniority level and implied years of experience against user's `years_experience` and `title`. Exact match = full score. One level off = partial. Two+ levels off = low. |
| Compensation fit | 15% | Compare job's `salary.min`/`salary.max` against `preferences.md → salary.minimum` and `salary.preferred`. Normalize currencies if different. Full score if job range overlaps preferred. Partial if above minimum. Zero if below minimum. Null salary = 50% (neutral). |
| Preferences match | 15% | Check location, remote policy, company size, industry against preferences. Each match = proportional credit. |
| Red flags | 15% | Start at full score, deduct for each red flag found (see Red Flag Checklist below). |
| Opportunity signal | 10% | Use `WebSearch` to check company reputation, recent news, growth signals. Score based on findings. If WebSearch is unavailable, score 50% (neutral) and note "insufficient data." |

## Red Flag Checklist

Deduct points for each flag found:

- Vague job description with no concrete responsibilities (-3)
- Unrealistic requirements / "unicorn" listing requiring 10+ technologies for mid-level (-4)
- Excessive requirements for stated seniority (e.g., "5 years" for junior) (-3)
- No salary information disclosed (-2)
- Listing age > 30 days based on `date_posted` (-3)
- Reposted listing (same job appearing frequently — check via WebSearch if possible) (-4)
- "Confidential" company or no company name (-4)
- "Urgently hiring" + below-market compensation (-3)
- "Other duties as assigned" dominating description (-2)
- Equity-heavy compensation with no salary floor (-3)
- Company currently undergoing layoffs (check via WebSearch) (-3)
