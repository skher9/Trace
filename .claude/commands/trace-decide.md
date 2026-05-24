# /trace decide

Usage: `/trace decide [module]`

Log an architectural or product decision to a module's Decision Log. Questions asked one at a time — never all at once.

---

## STEP 1 — Identify the module

- If user typed `/trace decide payments` → use `payments`
- If no module given → ask:
  > "Which module does this decision belong to? Run /trace list to see all modules."
  Wait for answer.

Verify `src/{module}/{module}-trace.md` exists. If not → tell user to run `/trace init` first.

---

## STEP 2 — Ask questions one at a time

Ask each question, wait for the answer, then ask the next.

**Q1 (required):**
> "What was decided? Give it a short title."

**Q2 (required):**
> "Why was this decision made?"

**Q3 (required):**
> "What alternatives did you consider?"

**Q4 (required):**
> "What tradeoffs did you accept? What did you give up?"

**Q5 (required):**
> "Who made this decision? (Name or team)"

**Q6 — MANDATORY. Cannot be skipped:**
> "Revisit when? This field is required.
>
> Examples:
> → When UPI transactions exceed 30% of volume
> → In 90 days
> → When team size exceeds 10 engineers
> → Never — this is permanent"

If the user tries to skip Q6 or leaves it blank, refuse and re-ask:
> "Revisit When is required — every decision needs a review trigger.
> Even 'Never — this is permanent' is acceptable. When should this be reviewed?"

Do not proceed until Q6 has a non-empty answer.

**Q7 (required):**
> "What is the current status?
> → Active
> → Superseded
> → Pending Review"

---

## STEP 3 — Write to file

Get today's date and author from `git config user.name`.

Append this block at the TOP of the Decision Log section (newest first), directly after the `> Never delete. Always append. Newest at top.` line:

```
### [{date}] — {Q1 title}
**Decision:** {Q1 answer}
**Why:** {Q2 answer}
**Alternatives Considered:** {Q3 answer}
**Tradeoffs Accepted:** {Q4 answer}
**Decided By:** {Q5 answer}
**Revisit When:** {Q6 answer}
**Status:** {Q7 answer}
```

Remove the `⚠️ No decisions logged yet.` placeholder if it exists.

---

## STEP 4 — Confirm

> "✅ Decision saved to src/{module}/{module}-trace.md"
>
> Revisit trigger: {Q6 answer}
