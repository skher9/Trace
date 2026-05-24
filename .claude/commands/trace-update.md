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

Wait for answer. Save this — it is used later for the changelog and PR template.

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

**DO NOT touch the Memory Log or Decision Log.** Those sections are append-only.

Remove ⚠️ markers from sections that are now filled in.

Update the `> Last Updated:` line at the top with today's date and `git config user.name`.

---

## STEP 6 — Changelog prompt

Ask:
> "Is this change worth logging in the changelog?
> → Yes — give me a one-line summary
> → No, minor change"

If Yes — append to the top of Section 8 — Changelog (newest first):

```
### {today's date} — {one-line summary}
- {what changed, from STEP 2}
```

If the change is breaking, also ask:
> "Is this a breaking change? If yes, describe it in one line."

Add `- Breaking change: {description}` under that changelog entry.

---

## STEP 7 — PR template prompt

Ask:
> "Are you raising a PR for this change?
> → Yes
> → No"

If Yes — read the trace file and generate this PR description template, pre-filled with real context:

```
## What changed
{answer from STEP 2}

## Why
{most recent Decision Log entry if one exists — otherwise leave blank}

## Modules affected
Upstream: {from Dependency Map — This Module Depends On}
Downstream: {from Dependency Map — Modules That Depend On This}

## Trace files updated
- [ ] src/{module}/{module}-trace.md updated
- [ ] Ownership is current
- [ ] Decision log updated if architectural change
- [ ] Runbook updated if incident response changed

## Watch out
{Intentional Absences table content}
```

Print this to the terminal so the developer can copy-paste into GitHub.

---

## STEP 8 — Confirm

> "✅ src/{module}/{module}-trace.md updated."
