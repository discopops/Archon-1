---
description: Parse each job-alert email into structured job listings via LLM
argument-hint: (no arguments — reads raw_emails.json)
---

# Job Alerts — Extract Structured Jobs

**Workflow ID**: $WORKFLOW_ID

---

## Load

```bash
cat $ARTIFACTS_DIR/raw_emails.json
```

---

## Extract jobs from each email

For each email in the array, use the LLM to extract all individual job listings mentioned.

**Extraction schema per job:**

```json
{
  "title": "exact job title",
  "company": "organisation name",
  "location": "city/state as listed",
  "location_type": "onsite|hybrid|remote|unknown",
  "salary": "salary range or 'Not stated'",
  "link": "apply URL if present, else null",
  "source": "seek|linkedin|indeed|jora|adzuna|ethicaljobs|other",
  "recently_posted": true|false,
  "email_id": "parent email id"
}
```

**Extraction rules:**
- Extract ALL jobs listed in each email, not just the first
- If an email has no extractable jobs (e.g. newsletter, non-job content), skip it and log
- For salary: parse ranges like `$140k–$160k`, `$90,000–$110,000 + super`, `$55/hr`; normalise to string
- For location_type: "remote" / "work from anywhere" / "fully remote" = remote; "hybrid" if stated; otherwise "onsite"
- SEEK digest emails may contain 5–20 jobs each — extract all

## Deduplicate

After extracting all jobs from all emails, deduplicate on `(title.lower().strip(), company.lower().strip())`.
Keep first occurrence. Log dedupe count.

## Save output

Write to `$ARTIFACTS_DIR/jobs_extracted.json`:

```json
{
  "extracted_at": "ISO-8601 timestamp",
  "emails_processed": N,
  "total_before_dedup": N,
  "total_after_dedup": N,
  "jobs": [ ...job objects... ]
}
```

## Output

Report:
- Emails processed
- Jobs extracted (before and after dedup)
- Any parse failures
