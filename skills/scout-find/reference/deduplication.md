# Deduplication

Before saving ANY job file (in all modes), check for duplicates against existing jobs in `~/.scout/jobs/` AND against other results in the current batch.

## Primary Match — Normalized Company + Title

1. Normalize company name: lowercase, strip suffixes (Inc, LLC, Ltd, Co, Corp, GmbH).
2. Normalize title: lowercase, strip location suffixes like "(Remote)"/"(US)"/"(Hybrid)", collapse whitespace. Do NOT strip seniority levels — "Senior Software Engineer" and "Software Engineer" are different roles.
3. If normalized company + normalized title match, it's the same job.

## Secondary Match — Exact URL

If `source_url` or any entry in `source_urls` matches an existing job's URLs, it's the same job.

## On Duplicate Found

- **Merging with an existing saved job:** append the new URL to the existing job's `source_urls` array. If the new version has more complete data (salary disclosed > undisclosed, more requirements listed), update those fields on the existing job.
- **Merging within the current batch** (same job from two sources): keep the more complete version, collect all URLs in `source_urls`. Set `source_url` to the best apply link — any URL matching a known ATS platform (Greenhouse, Lever, Ashby, Workday, etc.) is preferred over aggregator links (LinkedIn, Indeed, Wellfound). If no ATS link exists, use the first URL found.
- Inform the user of merges: "Merged duplicate: [title] at [company] (found on LinkedIn + Ashby)."

## Known Limitation

The same job may appear with materially different titles across sources (e.g., "Senior Backend Engineer" on LinkedIn vs "Senior Software Engineer, Backend" on Greenhouse). These will not be matched. False negatives (showing a duplicate) are better than false positives (merging different roles).
