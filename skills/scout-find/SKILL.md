---
name: scout-find
description: "Use when discovering job listings from URLs, pasted text, web searches, or job boards (LinkedIn, Indeed, Wellfound, HN). Activates when user wants to find, save, or import job opportunities."
---

# Scout Find

Discovers and normalizes job listings from any source, saving them to `~/.scout/jobs/`.

> **Scope:** This skill is for DISCOVERING NEW jobs. If the user wants to view existing saved jobs, check pipeline status, or review what they already have, direct them to `/scout-track` instead.

## Data Directory

Job files are saved to `~/.scout/jobs/`. Create the directory if it doesn't exist.

## Input Mode Detection

Determine mode from context:
- **User provides a URL** → URL mode
- **User pastes job description text** → pasted text mode
- **User describes what they're looking for** (e.g., "senior PM roles in fintech") → search mode
- **No input provided** (bare `/scout-find`) → If `~/.scout/profile/preferences.md` exists, go directly to search mode using the profile preferences. Do NOT ask the user what mode they want or what they're looking for — the profile already has that information. If no profile exists, ask them to describe what they're looking for.
- **Re-invocation** → Each `/scout-find` invocation runs a fresh search. Do NOT re-show results from a previous run. If the user runs `/scout-find` again, they want new results — run new queries.

## URL Mode

1. **Validate the URL** per [reference/url-validation.md](reference/url-validation.md).
2. **Try `WebFetch` first** to retrieve the page content. If the result is empty or clearly missing the job description (JS-rendered pages return minimal HTML), fall back to the `agent-browser` skill to navigate to the URL and extract the rendered text. Install it if needed: `npx skills add https://github.com/vercel-labs/agent-browser --skill agent-browser`.
3. **Detect ATS platform** per [reference/ats-detection.md](reference/ats-detection.md).
4. Parse the extracted text into the normalized format (see [../shared/job-file-schema.md](../shared/job-file-schema.md)).
5. Generate job ID, deduplicate, and save (steps 10–12 below).

## Pasted Text Mode

1. Parse the pasted text directly into the normalized format.
2. Ask the user for the source URL if they have it (optional).
3. Deduplicate and save (steps 10–12 below). Populate `source_urls` with `platform: "pasted"`. If the user provided a source URL, include it; otherwise set `url: null`.

## Search Mode

1. **Always read `~/.scout/profile/preferences.md` first** if it exists. Use target roles, industries, skills, salary range, and remote preferences to construct search queries. Do NOT ask the user to describe what they're looking for when a profile already exists — the profile IS the description. If the user provides additional criteria, combine them with the profile.
2. **Select search sources.** If `search_sources` is defined in preferences, use it. Otherwise, prompt the user to select sources:
   - WebSearch (Google) — checked by default
   - LinkedIn (show auth status: `logged in` or `public only` — see [reference/search-authentication.md](reference/search-authentication.md))
   - Indeed (show auth status)
   - Wellfound (show auth status)
   - HN Who's Hiring

   Save the user's selection to `preferences.md` under `search_sources` for future runs. If the user explicitly specifies sources in their message (e.g., "search LinkedIn and Indeed"), treat as a one-time override — do not update saved `search_sources`.
3. **Search selected sources in parallel:**
   - **WebSearch** (if selected): 3-5 targeted queries constructed from profile. Example: `"senior full stack engineer" remote $180k+ 2026 hiring`.
   - **LinkedIn / Indeed / Wellfound** (if selected): Use `agent-browser` to search for roles matching the profile, extract rendered text from results pages, parse listings. See [reference/search-authentication.md](reference/search-authentication.md) for setup.
   - **HN Who's Hiring** (if selected): Find the current month's thread via `WebSearch` query: `site:news.ycombinator.com "Ask HN: Who is hiring" <month> <year>`. Fetch the thread URL with `WebFetch`. Parse top-level comments for role matches using profile keywords.

   Filter out staffing agencies (Toptal, Turing, Lemon.io, Proxify, Andela) from all results. If any source fails (CAPTCHA, blocked, timeout, site down), continue with results from successful sources. Report which sources failed and why. Do not retry automatically.
4. **Deduplicate** per [reference/deduplication.md](reference/deduplication.md).
5. **Present results as a numbered list** — title, company, location, salary (if available), source (e.g., "via LinkedIn", "via WebSearch"). **STOP HERE and wait for user selection.** Do NOT save any jobs yet.
6. User selects which ones to save (by number, "all", or a subset).
7. For each selected result, fetch the full listing via URL mode and save. Populate `source_urls` with the appropriate platform value for each source where the listing was found.

> **IMPORTANT:** In search mode, you MUST present candidates and wait for user input before saving any job files. Automatically saving all search results without user selection is a violation of this workflow.

## Saving a Job

10. **Generate job ID** per [reference/job-id-generation.md](reference/job-id-generation.md).
11. **Save per schema** in [../shared/job-file-schema.md](../shared/job-file-schema.md).
12. **Normalize salary** per [reference/salary-normalization.md](reference/salary-normalization.md).
13. **Apply content trust rules** from [../shared/content-trust-rules.md](../shared/content-trust-rules.md).

## Preferences Integration

Search mode reads and writes to `~/.scout/profile/preferences.md`:

**Read:** `target_roles`, `target_industries`, `seniority`, `remote_policy`, `salary` — used to construct search queries.

**Write:** `search_sources` — saved on first source selection:
```yaml
search_sources:
  - "websearch"
  - "linkedin"
  - "indeed"
```
Valid values: `"websearch"`, `"linkedin"`, `"indeed"`, `"wellfound"`, `"hn"`.

Reused on subsequent runs without re-prompting. User overrides (e.g., "search LinkedIn") are one-time — they do not update the saved preference.

## Append to History

After saving each job, append per [../shared/history-format.md](../shared/history-format.md).

## Completion

After saving job(s), inform the user:

> "Saved [N] job(s) to `~/.scout/jobs/`. Run `/scout-vet` to evaluate them against your profile, or `/scout-find` again to discover more."
