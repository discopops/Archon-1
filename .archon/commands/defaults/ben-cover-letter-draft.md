---
description: Draft a one-page cover letter aligned to the role and CV
argument-hint: (no arguments — reads application-context.md)
---

# Cover Letter — Draft

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat $ARTIFACTS_DIR/application-context.md
```

---

## Write the letter

Draft a **one-page** cover letter following these rules:

**Voice & tone:**
- Australian English (organisation not organization, programme not program, etc.)
- Professional but warm — not overly formal
- First person, active voice
- Concise — every sentence earns its place

**Structure (4 paragraphs max):**
1. Opening — the role, why this specific organisation interests Ben, one compelling hook
2. Core value proposition — 2–3 specific achievements from CV that directly address the PD's key requirements. Name the organisation and role context where possible.
3. Alignment paragraph — sector knowledge, credentials (CA), leadership style, or specific capability the PD emphasises
4. Close — enthusiasm, reference to attached CV, preferred contact

**Do not:**
- Use phrases like "I am writing to express my interest"
- Repeat the CV verbatim
- Exceed one page (approximately 350–400 words)
- Make claims not supported by the CV

Write the full letter to `$ARTIFACTS_DIR/cover-letter-draft.md`.

Output: the full letter text.
