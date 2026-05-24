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

## STEP 2 — For each file, check

- **Last updated date** → read the `> Last Updated:` line at the top
- **Memory count** → count occurrences of `### [` in the Memory Log section
- **Needs review count** → count occurrences of `⚠️` in the file

---

## STEP 3 — Return formatted table

```
📚 Trace — Project Knowledge Base

| Module | Last Updated | Memories | Needs Review |
|--------|-------------|----------|--------------|
| payments | 2026-05-24 | 3 entries | 0 ⚠️ |
| auth | 2026-05-20 | 1 entry | 2 ⚠️ |
| notifications | Never | 0 entries | 5 ⚠️ |

💡 Modules with ⚠️ have auto-generated sections that need human review.
Run /trace update [module] to complete them.
```

Use "Never" for last updated if the file was never manually updated (only auto-generated).
Use "1 entry" / "N entries" (not "1 entries").

If no trace files found:
> "No trace files found. Run /trace init to get started."
