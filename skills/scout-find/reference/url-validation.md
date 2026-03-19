# URL Validation

## Fetch-time Validation

Before fetching any URL:

- Protocol MUST be `https://`. Reject `http://`, `file://`, `javascript:`, `data:`, and any other scheme.
- Hostname must be a public domain. Reject:
  - `localhost`
  - `127.0.0.1`, `0.0.0.0`, `::1`
  - `169.254.*` (link-local)
  - `10.*` (RFC 1918)
  - `172.16.*` – `172.31.*` (RFC 1918)
  - `192.168.*` (RFC 1918)
  - Any other private or reserved IP range

This is SSRF prevention — do not fetch internal network resources.

## Storage-time Validation

Before storing `application_url`, `source_url`, or any URL in `source_urls` in the job file:

- Must use `https://` protocol.
- Must be a valid public URL (no private IPs, no `localhost`).
- Store the URL exactly as validated — do not reconstruct it from user input or page content after validation.
- If a URL fails validation, set the field to `null` and add a note in the Description section explaining the original URL was invalid.
