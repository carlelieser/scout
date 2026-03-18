# Scout

A job search copilot. Find, vet, apply, prep, and track — from listing to offer.

## Install

```bash
npx skills add carlelieser/scout -y
```

## Skills

| Skill | Command | What it does |
|-------|---------|-------------|
| **scout-profile** | `/scout-profile` | Build your master profile, CV, and preferences |
| **scout-find** | `/scout-find` | Discover jobs from URLs, pasted text, or web search |
| **scout-vet** | `/scout-vet` | Score jobs against your profile with weighted criteria |
| **scout-apply** | `/scout-apply` | Generate ATS-compliant tailored CVs, cover letters, and PDFs |
| **scout-prep** | `/scout-prep` | Interview prep — company research, practice questions, debriefs |
| **scout-track** | `/scout-track` | Pipeline management, follow-ups, and search analytics |

## Quick Start

1. **Set up your profile** — Run `/scout-profile` to create your master CV and preferences. You can paste an existing CV or answer questions from scratch.

2. **Find jobs** — Run `/scout-find` with a URL, pasted job description, or search query like "senior PM roles in fintech, remote".

3. **Vet them** — Run `/scout-vet` to score each job against your profile. Checks skills match, compensation fit, red flags, and deal-breakers.

4. **Apply** — Run `/scout-apply` to generate a tailored CV and PDF. Keywords are woven in naturally, formatting is ATS-safe, and an honesty check prevents fabricated skills.

5. **Prep for interviews** — Run `/scout-prep` for company research briefs, STAR-format practice answers, and post-interview debrief capture.

6. **Track everything** — Run `/scout-track` for a pipeline dashboard, follow-up reminders, contact management, and response rate stats.

## How It Works

All skills read and write to `~/.scout/`:

```
~/.scout/
  profile/          # Your master CV, profile, and preferences
  jobs/             # One file per job listing (normalized format)
  applications/     # Tailored materials per job (CV, cover letter, prep notes)
  templates/        # Optional custom CV/cover letter templates
  history.md        # Activity log
```

Each job is a Markdown file with YAML frontmatter. Pipeline state lives in the job files themselves — no separate database. Everything is human-readable and greppable.

### Pipeline

```
discovered → vetted → materials-ready → applied → interviewing → offer → accepted/rejected
```

### Vetting Criteria

Jobs are scored on a 100-point scale:

- **Skills match** (30%) — required and preferred skills vs your profile
- **Experience match** (15%) — seniority and years of experience
- **Compensation fit** (15%) — salary range overlap with your preferences
- **Preferences match** (15%) — location, remote policy, company size
- **Red flags** (15%) — ghost jobs, vague descriptions, unrealistic requirements
- **Opportunity signal** (10%) — company reputation and growth potential

Deal-breakers (fully onsite, below minimum salary, etc.) override the score.

### ATS Compliance

Generated CVs follow strict ATS rules:

- Standard section headers (Summary, Experience, Education, Skills)
- Single-column, no tables or images
- Both acronyms and expanded forms (e.g., "Machine Learning (ML)")
- Keyword density limits to avoid spam detection
- ASCII-safe encoding
- PDF export via `md-to-pdf`, Pandoc + Typst, or Pandoc + WeasyPrint

## Prerequisites

- A supported AI agent ([Claude Code](https://claude.ai/download), Cursor, Cline, Codex, etc.)
- A PDF tool (one of the following, in order of preference):
  - [`md-to-pdf`](https://github.com/simonhaenisch/md-to-pdf) — `npm i -g md-to-pdf` (recommended, single command)
  - [Pandoc](https://pandoc.org/) + [Typst](https://typst.app/) — `brew install pandoc typst`
  - [Pandoc](https://pandoc.org/) + [WeasyPrint](https://weasyprint.org/) — `brew install pandoc && pip install weasyprint`
  - [Pandoc](https://pandoc.org/) + LaTeX — `brew install pandoc && brew install --cask mactex` (heaviest option)

## Security

Scout processes untrusted external content (job listings, web search results) and generates files. The skills include the following security measures:

- **Content boundary isolation** — All untrusted external content is wrapped in `<UNTRUSTED_EXTERNAL_CONTENT>` markers that instruct the agent to treat it as data only, preventing indirect prompt injection from crafted job listings
- **Input validation** — Job IDs are validated against `^[a-z0-9][a-z0-9-]*[a-z0-9]$` before use in file paths or shell commands. Path traversal sequences (`..`, `/`, `\`) are rejected
- **URL validation** — URLs are validated as HTTPS-only with public hostnames before fetching (SSRF prevention)
- **Shell safety** — All shell commands use double-quoted paths with pre-validated inputs. No dynamic script generation or code execution
- **LaTeX sandboxing** — When LaTeX is used for PDF generation (last-resort fallback), `--sandbox` is passed to prevent `\write18` command execution
- **No fabrication** — Honesty checks prevent the agent from adding skills or experience the user doesn't have

Security audits are run by [Gen Agent Trust Hub](https://www.gendigital.com/), [Socket](https://socket.dev/), and [Snyk](https://snyk.io/) via [skills.sh](https://skills.sh/carlelieser/scout).

## Compatible Agents

Works with any agent that supports the [open skills standard](https://skills.sh): Claude Code, Cursor, Cline, Codex, Continue, Gemini CLI, Goose, Junie, Kiro, OpenCode, and others.

## License

MIT
