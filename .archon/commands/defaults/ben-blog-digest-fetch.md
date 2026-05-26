---
description: Fetch RSS feeds from Anthropic, OpenAI, Google AI and LangChain and format last-24h articles into a digest
argument-hint: (no arguments)
---

# AI Blog Digest — Fetch & Format

**Workflow ID**: $WORKFLOW_ID

---

Fetch all four RSS feeds and output a clean digest. Run date: $get-date.output

## Feeds to fetch

Use WebFetch on each URL. Do all four — if one fails, note it and continue.

1. **Anthropic** — `https://www.anthropic.com/rss.xml`
2. **OpenAI** — `https://openai.com/blog/rss.xml`
3. **Google AI** — `https://blog.google/technology/ai/rss/`
4. **LangChain** — `https://blog.langchain.dev/rss/`

## Filtering

Include only items published within the last **24 hours** relative to the run date above.

Handle both RSS 2.0 (`pubDate`) and Atom (`published`) formats. Parse dates carefully — strip timezone offsets if needed.

## Output format

```
## AI Blog Digest — [DATE]

### Anthropic
- [Title](URL) — [date & time]
(or "No new articles in the last 24 hours")

### OpenAI
...

### Google AI
...

### LangChain
...

---
X new articles across Y sources today.
```

No commentary beyond the digest. No markdown beyond the format above.
