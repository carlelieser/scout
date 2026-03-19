# Cascading Updates for Contact Information

When updating contact fields (name, email, phone, URLs), changes must propagate to all dependent files:

1. **Always update:** `master-profile.md` and `master-cv.md`.
2. **Update pending applications:** Glob `~/.scout/applications/*/cv.md` and update contact info in each. Then regenerate PDFs for any job whose status is `"materials-ready"` (not yet submitted). Reuse the existing PDF generation script if one was created in the current session.
3. **Warn about submitted applications:** For any job with status `"applied"` or later, inform the user: "Note: your application to [Company] was already submitted with the old [field]. The updated [field] will be used for future applications."
4. **Update cover letters and email drafts** in `~/.scout/applications/*/cover-letter.md` and `~/.scout/applications/*/email-draft.md` if they exist and contain the old value.
