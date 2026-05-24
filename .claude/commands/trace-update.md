# /trace update

Usage: `/trace update [module]`

Run when a feature changes and the trace file needs updating.

---

## STEP 1 — Show current state

Read `src/{module}/{module}-trace.md`.

Display the current **Feature Understanding** section to the user so they can see what is documented.

---

## STEP 2 — Ask what changed

Ask one question:
> "What changed in this module? Describe briefly."

Wait for answer.

---

## STEP 3 — Ask dependency check

> "Did any dependencies change?"

Options: Yes / No / Not sure

If Yes:
> "What was added or removed?"

Update the Dependency Map section accordingly.

---

## STEP 4 — Ask intentional absence check

> "Does this change mean something will intentionally NOT happen? For example — no notification, no email, no logging?"

If Yes:
- Ask: "What will not happen?"
- Ask: "Why is this intentional?"
- Add a row to the Intentional Absences table with today's date and `git config user.name`.

---

## STEP 5 — Update the file

Update these sections only:
- **Feature Understanding → What This Module Does** — incorporate what changed
- **Feature Understanding → Current Behaviour** — update if behaviour changed
- **Feature Understanding → Recent Changes** — add a new line at top: `{date}: {what changed}`
- **Dependency Map** — update if dependencies changed
- **Expected Behaviour** — update if absences or conditionals changed

**DO NOT touch the Memory Log.** That section is append-only and only modified by `/trace remember`.

Remove ⚠️ markers from sections that are now filled in.

Update the `> Last Updated:` line at the top with today's date and `git config user.name`.

---

## STEP 6 — Confirm

> "✅ src/{module}/{module}-trace.md updated."
