# /trace list

Usage: `/trace list`

Show all trace files in the project with their status.

---

## STEP 1 — Find all trace files

Run:
```
find src/ -name "*-trace.md" | sort
```

---

## STEP 2 — For each file, extract

- **Module name** → from filename
- **Owner** → read Section 5 — Ownership, Primary Owner row
- **Last updated date** → read the `> Last Updated:` line at the top
- **Memory count** → count `### [` occurrences inside the Memory Log section (Section 4)
- **Decision count** → count `### [` occurrences inside the Decision Log section (Section 6)
- **Runbook status** → check if Section 7 still has the ⚠️ placeholder → ❌ if yes, ✅ if written
- **Needs review count** → count total `⚠️` occurrences in the file

---

## STEP 3 — Return formatted table

```
📚 Trace — Project Knowledge Base

| Module | Owner | Last Updated | Memories | Decisions | Runbook | ⚠️ |
|--------|-------|-------------|----------|-----------|---------|-----|
| payments | Priya Sharma | 2026-05-24 | 3 entries | 2 | ✅ | 0 |
| auth | Rahul | 2026-05-20 | 1 entry | 0 | ❌ | 2 |
| notifications | ⚠️ | Never | 0 entries | 0 | ❌ | 5 |

Risks:
❌ Runbook missing — {list modules with no runbook}
⚠️ No owner — {list modules with unassigned ownership}

💡 Run /trace runbook update [module] for missing runbooks.
💡 Run /trace owner set [module] for unassigned owners.
💡 Run /trace update [module] to clear ⚠️ sections.
```

Rules:
- Owner shows ⚠️ if still "Not yet assigned" or has ⚠️ prefix
- Use "Never" for last updated if file was only auto-generated
- Use "1 entry" / "N entries" (not "1 entries")
- Runbook is ✅ only if Section 7 has no ⚠️ placeholder remaining

If no trace files found:
> "No trace files found. Run /trace init to get started."
