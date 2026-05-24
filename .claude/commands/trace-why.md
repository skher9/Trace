# /trace why

Usage: `/trace why [module]` OR `/trace why [keyword]`

Retrieve context instantly when a developer hits a problem.

---

## STEP 1 — Find the trace file

- If a module name is given → open `src/{module}/{module}-trace.md` directly
- If a keyword is given → search across ALL `*-trace.md` files with:
  ```
  grep -ril "{keyword}" src/ --include="*-trace.md"
  ```
  Then read each matching file and find sections containing the keyword.

Search in: memory log titles, root causes, watch out sections, what happened, fix descriptions, intentional absences.

---

## STEP 2 — Return relevant context

Format response exactly like this:

```
📁 {module} — Trace Context

🔴 Relevant Memory Entries:
{show matching memory log entries in full}

⚠️ Watch Out:
{show all "Watch Out" notes from memory entries}

❌ Intentional Absences:
{show the full Intentional Absences table}

🔗 Dependencies:
Depends on: {upstream modules}
Depended on by: {downstream modules}
Critical path: {critical path if documented}
```

If keyword search matched multiple modules, show context from each with a header per module.

---

## STEP 3 — If nothing found

> "No trace entries found for '{keyword}'.
> Want to add one? Run /trace remember [module]"
