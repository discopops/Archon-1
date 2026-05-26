---
description: Fetch the position description from a URL and read Ben's CV from Google Drive
argument-hint: <job-listing-URL-or-pasted-details>
---

# Cover Letter — Fetch PD & CV

**Workflow ID**: $WORKFLOW_ID

---

## Input

Job listing: `$ARGUMENTS`

This may be:
- A URL to a job posting (SEEK, LinkedIn, company website)
- Pasted text from the Daily Job Alerts Tier 1 output

---

## Phase 1: Fetch position description

If `$ARGUMENTS` contains a URL, fetch it with WebFetch. Extract:
- Job title
- Organisation name
- Location
- Key responsibilities (bullet points)
- Key requirements / selection criteria
- Salary (if stated)
- Apply link

If the URL is behind a login or fails to load, use whatever text was pasted directly.

## Phase 2: Read CV

```bash
ls ~/Library/CloudStorage/GoogleDrive-benowilliams@gmail.com/My\ Drive/Personal/a4-cv/Ben-Williams-*.docx 2>/dev/null | sort | tail -1
```

Read the most recent CV file. Extract:
- Current/recent roles (last 3–4 positions)
- Key achievements and credentials
- Sector experience relevant to this role
- Qualifications (CA, degrees, etc.)

## Phase 3: Write context file

Write `$ARTIFACTS_DIR/application-context.md`:

```markdown
# Application Context

## Role
**Title:** [title]
**Organisation:** [org]
**Location:** [location]
**Salary:** [if stated]
**Apply link:** [URL]

## Position Description Summary
[Key responsibilities and requirements]

## CV Alignment
[Which of Ben's experiences/achievements directly address the PD requirements]

## Tone Notes
[Any sector-specific tone considerations — government, NFP, financial services, etc.]
```

Output: "PD fetched for [title] at [org]. CV read. Context written."
