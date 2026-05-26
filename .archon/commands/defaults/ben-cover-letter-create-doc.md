---
description: Save the cover letter draft as a Google Doc with PD and apply link
argument-hint: (no arguments — reads cover-letter-draft.md and application-context.md)
---

# Cover Letter — Create Google Doc

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat $ARTIFACTS_DIR/cover-letter-draft.md
cat $ARTIFACTS_DIR/application-context.md
```

---

## Create Google Doc

Use Composio `GOOGLEDOCS_CREATE_DOCUMENT` to create a new doc in:
```
~/Library/CloudStorage/GoogleDrive-benowilliams@gmail.com/My Drive/Personal/Cover Letters/
```

Document title: `Cover Letter — [Role Title] — [Organisation] — YYYY-MM-DD`

Document content (in order):
1. The full cover letter text
2. `---` separator
3. **Position Description**
   [Full PD text from application-context.md]
4. `---` separator
5. **Apply:** [apply link URL]

## Share settings

Leave as default (private — only Ben). No sharing needed.

## Output

Return:
- Google Doc URL (for opening/editing)
- Document title
- One-line confirmation: "Cover letter for [title] at [org] saved to Google Drive. [URL]"
