# /trace runbook

Two modes: view an existing runbook or create/update one.

---

## MODE 1 — View: `/trace runbook [module]`

### STEP 1 — Read the trace file

Read `src/{module}/{module}-trace.md`. Extract Section 7 — Runbook only.

### STEP 2 — Return it clean

No extra text. No commentary. Just the runbook content, formatted clearly:

```
🚨 {Module Name} — Runbook

SYMPTOMS
{symptoms list}

CHECK FIRST
{checklist}

COMMON FIXES
{fixes table}

ESCALATE IF
{escalation conditions}

ESCALATION PATH
{name1} → {name2} → {name3}

Last Tested: {date}
```

If runbook is not yet written (still has ⚠️ placeholder):
> "⚠️ No runbook written for {module} yet.
> Run /trace runbook update {module} to create it now."

---

## MODE 2 — Create/Update: `/trace runbook update [module]`

### STEP 1 — Read current state

Read `src/{module}/{module}-trace.md`.

Pull escalation path automatically from Section 5 — Ownership:
- On Call Escalation field → use this as the Escalation Path in the runbook

Show user the current runbook section if it exists, so they know what they're updating.

### STEP 2 — Ask questions one at a time

**Q1 (required):**
> "What are the symptoms when this module breaks? List them — one per line."

**Q2 (required):**
> "What should a developer check first, before touching anything? (Think: logs, dashboards, dependencies)"

**Q3 (required):**
> "What are the most common failure scenarios and their fixes? Format: [symptom] | [fix] | [time to fix]
> Example: DB connection timeout | Restart connection pool, check DB CPU | 5 min"

**Q4 (optional):**
> "When should a developer escalate instead of fixing themselves? (Press Enter to skip)"

### STEP 3 — Write to file

Replace the entire Section 7 content with:

```
## 7. Runbook — When {Module Name} Breaks

### Symptoms
{Q1 answers as bullet list}

### Check First (before touching anything)
{Q2 answers as checkbox list: - [ ] item}

### Common Fixes
| Symptom | Fix | Time to Fix |
|---------|-----|-------------|
{Q3 answers parsed into table rows}

### Escalate If
{Q4 answer, or "Escalate if fix is not working within 30 minutes."}

### Escalation Path
{pulled from Ownership section — On Call Escalation field}

### Last Tested: {today's date}
> Update this date every time you verify the runbook still works.
```

### STEP 4 — Confirm

> "✅ Runbook saved to src/{module}/{module}-trace.md
> Last Tested set to today: {date}
>
> Run /trace runbook {module} to view it anytime."
