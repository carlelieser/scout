# Field Processing Reference

## Salary Normalization

- If salary is a range (e.g., "$150k-$180k"), set `min` and `max` accordingly.
- If salary is a single number, set `min` and `max` equal.
- If salary is "Competitive" or undisclosed, set all numeric fields to `null` and put the raw text in `notes`.
- Normalize currency and period. If the listing says "EUR 80,000", set `currency: "EUR"`, `period: "annual"`.
- If hourly/monthly, convert to annual equivalent and note the original in `notes`.

## Application Method Detection

Determine `application_method` from the listing:

- ATS portal with "Apply" button → `"portal"`
- "Send your CV to email@..." → `"email"`
- LinkedIn Easy Apply → `"linkedin-easy-apply"`
- Recruiter contact → `"recruiter"`
- Default → `"portal"`
