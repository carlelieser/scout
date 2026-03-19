# Search Authentication

Job board search uses `agent-browser` to piggyback on the user's running Chrome session — no credential storage, no login automation. Install if needed: `npx skills add https://github.com/vercel-labs/agent-browser --skill agent-browser`.

## Persistent Profile (Preferred)

Use `agent-browser --profile ~/.scout/browser-profile` to maintain a persistent browser profile. On first use, the user logs into LinkedIn/Indeed/Wellfound in the headed browser. Subsequent runs reuse the saved session cookies automatically. Always use `--headed` when the user needs to log in so they can see the browser window.

## Connecting to Running Chrome (Alternative)

If the user wants to use their existing Chrome session, they must launch Chrome with `--remote-debugging-port=9222`. Then use `agent-browser --cdp 9222` to connect. Note: Chrome must be fully quit and relaunched with this flag — it won't work if Chrome is already running without it.

## Auth Status Detection

- If a search results page looks like a login page, auth wall, or CAPTCHA rather than job listings, the session has expired.
- Inform the user: "[Site] session expired — searching with public results only. Run `/scout-find` with `--headed` to re-authenticate."
- Fall back to unauthenticated search for that source.

> **IMPORTANT:** When searching job boards with `agent-browser`, you MUST actually use the `agent-browser` skill. Do NOT substitute WebFetch or WebSearch for job board searches — those tools cannot access authenticated sessions or JS-rendered content. `agent-browser` is the tool for LinkedIn, Indeed, and Wellfound. WebFetch/WebSearch are only for the WebSearch and HN Who's Hiring sources.
