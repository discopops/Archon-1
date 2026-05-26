---
description: Fetch Medium newsletter emails from Gmail, extract article links, categorize, and append to Vault digest
argument-hint: (no arguments)
---

# Medium Weekly Digest — Fetch & Append

**Workflow ID**: $WORKFLOW_ID

---

## Phase 1: Search Gmail

Search for Medium newsletter emails received in the last 7 days:

```
from:@medium.com after:YYYY/MM/DD
```

Use Composio `GMAIL_FETCH_EMAILS` with `max_results: 100`, `verbose: false`, `include_payload: false`.

Collect message IDs. Identify the top 5 likely newsletter emails by subject.

## Phase 2: Hydrate

Fetch full bodies for the top 5 using `GMAIL_FETCH_MESSAGE_BY_MESSAGE_ID` with `format: full`.

Parse the MIME payloads:
- Prefer `text/html` parts
- Decode base64url: replace `-`→`+`, `_`→`/`, fix padding
- Fall back to `messageText` if needed

## Phase 3: Extract links

Keep only canonical Medium article URLs: `https://medium.com/@username/slug`

Drop profile pages, duplicates, and non-article URLs.

Clean titles: trim after first double-space, strip boilerplate.

## Phase 4: Categorize

Apply these keyword rules to each article title:

- **Claude Code & AI Agents**: `claude code`, `claude`, `/btw`, `bmad`, `speckit`, `mcp`
- **AI & LLMs — General**: `agent`, `multi-agent`, `crewai`, `langgraph`, `llm`, `gpt`, `gemini`, `anthropic`, `rag`, `notebooklm`, `ai image`, `minimax`
- **Apple & iOS**: `iphone`, `ios`, `ipad`, `apple watch`
- **Mac & macOS**: `macbook`, `mac app`, `macos`, `m4`, `m5`
- **Python & Data**: `python`, `dataviz`, `charts`, `programming language`
- **n8n & Automation**: `n8n`, `self-host`, `automation`
- **UI/UX & Design**: `ui design`, `design`, `landing page`, `whiteboard`, `canvas`
- **Productivity & Other**: everything else

## Phase 5: Render & append

Format plain markdown — no tables:

```markdown
## Medium Digest — Week of YYYY-MM-DD

### Claude Code & AI Agents
- [Title](URL)

### AI & LLMs — General
- [Title](URL)
...
```

Append to `~/Vault/Claude-Code/03-reference/medium-digest/medium-weekly-digest.md` with a `\n\n---\n\n` separator. Create the file if it doesn't exist.

Output a one-line summary: "N articles extracted across N categories."
