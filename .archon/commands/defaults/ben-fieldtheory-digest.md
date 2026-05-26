---
description: Generate a themed digest of newly synced X/Twitter bookmarks and save report to Vault
argument-hint: (no arguments — reads ft stats/categories output from upstream nodes)
---

# Fieldtheory — Digest & Report

**Workflow ID**: $WORKFLOW_ID

---

## Inputs

**Sync result:**
```
$sync.output
```

**Stats & categories:**
```
$stats.output
```

---

## Phase 1: Get new bookmarks

Parse `$sync.output` to find how many new bookmarks were added this run.

```bash
source ~/.nvm/nvm.sh && nvm use 22 --silent 2>/dev/null
ft list --limit <N_NEW> 2>&1
```

Where `N_NEW` is the count from sync output. If sync added 0 new items, skip digest generation and write a brief "no new bookmarks" report.

## Phase 2: Generate digest

Group new bookmarks by theme/category. For each bookmark write:

```markdown
### @handle — Short title phrase

2–4 lines: what the post is about, why it matters, category label, link.
```

Skip bookmarks with insufficient context (link-only, very short).

## Phase 3: Write Vault report

Save to:
```
~/Vault/MacOS/Bookmarks/X-Bookmarks/daily-x-bookmark-sync-YYYY-MM-DD.md
```

Report structure:
```markdown
# X Bookmark Sync — YYYY-MM-DD

## Summary
- Sync status: success/partial/failed
- New bookmarks: N
- Total bookmarks: N
- Category breakdown: (from ft categories)

## New Bookmarks by Theme

### [Theme A]
[digest entries]

### [Theme B]
...

## Errors & Notes
[Any sync errors or fallbacks used]

---
*Saved: ~/Vault/MacOS/Bookmarks/X-Bookmarks/daily-x-bookmark-sync-YYYY-MM-DD.md*
```

Output: "Sync complete. N new bookmarks. Report saved to Vault."
