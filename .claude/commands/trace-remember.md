# /trace remember

Usage: `/trace remember [module]`

Capture a memory entry and append it to the correct trace file. Ask questions one at a time — never dump all at once.

---

## STEP 1 — Identify the module

- If user typed `/trace remember payments` → use `payments`
- If no module given → ask:
  > "Which module does this memory belong to? Run /trace list to see all modules."
  Wait for answer.

Verify `src/{module}/{module}-trace.md` exists. If not → tell user to run `/trace init` first.

---

## STEP 2 — Ask questions one at a time

Ask each question, wait for the answer, then ask the next.

**Q1 (required):**
> "What happened?"

**Q2 (required):**
> "What was the root cause?"

**Q3 (required):**
> "How was it fixed?"

**Q4 (optional):**
> "What did you try that didn't work? (Press Enter to skip)"

**Q5 (optional):**
> "Any gotcha or warning for the next developer? (Press Enter to skip)"

**Q6 (optional):**
> "Did this affect any other modules? (Press Enter to skip)"

---

## STEP 3 — Check for intentional absence

After Q3, ask:
> "Does this change mean something will intentionally NOT happen in this module? For example — no notification, no email, no logging?"

If Yes:
- Ask: "What will not happen?"
- Ask: "Why is this intentional?"
- Add a row to Section 3 — Intentional Absences in the trace file.

---

## STEP 4 — Write to file

Get today's date and author from `git config user.name`.

Append this block at the TOP of the Memory Log section (newest first), directly after the `> Never delete entries. Always append. Newest at top.` line:

```
### [{date}] [{author}] — {title derived from Q1}
**What Happened:** {Q1 answer}
**Root Cause:** {Q2 answer}
**Fix:** {Q3 answer}
**Failed Attempts:** {Q4 answer or "None documented"}
**Watch Out:** {Q5 answer or "None documented"}
**Affects:** {Q6 answer or "None documented"}
```

---

## STEP 5 — Confirm

> "✅ Memory saved to src/{module}/{module}-trace.md"
