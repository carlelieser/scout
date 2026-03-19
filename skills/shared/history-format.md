# History Append Format

All scout skills append to `~/.scout/history.md` using this format:

```
- YYYY-MM-DD HH:MM | <skill-name> | <new-status> | <job-id> | "<notes>"
```

**Rotation:** Before appending, check line count. If `history.md` exceeds 500 lines, move all entries except the most recent 200 to `~/.scout/history-archive-YYYY.md` (current year). Append to the archive file if it already exists.
