---
description: Review toll reconciliation output, send admin report email, draft hirer charge emails
argument-hint: (no arguments — reads reconciliation output from data/)
---

# Nibble Tolls — Report and Email

**Workflow ID**: $WORKFLOW_ID

---

## Load reconciliation output

```bash
SCRIPT_DIR="/Users/BLW_M2_HOME/Scripts_&_Tools/Automation_Reporting/nibble_tolls"
# Find most recent reconciliation report
ls -t "$SCRIPT_DIR/data/"*.json 2>/dev/null | head -5
ls -t "$SCRIPT_DIR/logs/"*.log 2>/dev/null | head -1 | xargs tail -50 2>/dev/null
```

---

## Review reconciliation results

Read the reconciliation output JSON/log to extract:
- Total toll charges for the week (AUD)
- Number of reservations matched vs unmatched
- Per-hirer breakdown: hirer name, booking dates, toll charges to recover
- Any unmatched tolls (vehicle used outside a reservation window)
- Any reservations with zero tolls

---

## Send admin summary email

Use Composio `GMAIL_SEND_EMAIL` to send an internal admin report to `benowilliams@gmail.com`:

Subject: `Nibble Bikes — Toll Reconciliation [DD MMM YYYY]`

Body:
```
Weekly Toll Reconciliation — [date range]

Summary:
• Total toll charges: AUD $[total]
• Reservations with tolls: [N]
• Matched to hirers: [N] | Unmatched: [N]
• Recovery emails drafted: [N]

[Per-hirer table: Hirer | Dates | Toll Amount | Status]

[Unmatched tolls section if any]

Admin: See attached report for full detail.
```

---

## Draft hirer charge emails

For each hirer with toll charges to recover:

Use `GMAIL_CREATE_DRAFT` (or `create_gmail_drafts.py` if Composio unavailable):

Subject: `Nibble Bikes — Toll charges for your rental [DD MMM YYYY]`

Body (per hirer):
```
Hi [First Name],

Thank you for hiring with Nibble Bikes.

During your rental period ([start date] – [end date]), the bike you hired
incurred toll charges that are passed through at cost:

[Toll breakdown: date | location | amount]
Total: AUD $[amount]

Payment link: [use existing payment method or request EFT]

If you have any questions about these charges, please reply to this email.

Thanks,
Ben
Nibble Bikes
```

Save drafts (do not send automatically — Ben reviews before sending).

---

## Save report to Vault

```bash
mkdir -p ~/Vault/Claude-Code/01-analysis/nibble-tolls
```

Save summary markdown to:
`~/Vault/Claude-Code/01-analysis/nibble-tolls/$(date '+%Y-%m-%d')-toll-reconciliation.md`

## Output

Confirm: admin email sent, N hirer drafts created, Vault path.
