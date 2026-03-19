# Job ID Generation

## Format

`YYYY-MM-DD-<company>-<title>`

## Slugification Rules

- Lowercase all characters.
- Strip all characters that are NOT `[a-z0-9-]` (after lowercasing).
- Collapse multiple consecutive hyphens into one.
- Strip leading/trailing hyphens.
- Transliterate accented characters to ASCII (e.g., "Societe Generale" → `societe-generale`).
- Max 60 characters for the slug portion (after the date prefix).

## Path Safety (mandatory)

- The final slug MUST match the regex `^[a-z0-9][a-z0-9-]*[a-z0-9]$` (or be a single alphanumeric character).
- Reject any slug that contains `..`, `/`, `\`, or null bytes — these indicate path traversal.
- The complete file path MUST resolve to within `~/.scout/jobs/` — verify with path resolution before writing.
- If sanitization produces an empty slug, fall back to a hash of the company+title (e.g., first 12 chars of SHA-256).

## Collision Handling

If `~/.scout/jobs/<id>.md` already exists, append `-2`, `-3`, etc.
