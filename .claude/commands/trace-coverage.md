# /trace coverage

Two modes: view test coverage or update it manually.

---

## MODE 1 — View: `/trace coverage [module]`

### STEP 1 — Read the trace file

Read `src/{module}/{module}-trace.md`. Extract Section 11 — Test Coverage only.

### STEP 2 — Also re-scan for test files live

Run:
```
find src/{module}/ -name "*.spec.ts" -o -name "*.test.ts" -o -name "*.spec.js" -o -name "*.test.js" -o -name "*.spec.tsx" -o -name "*.test.tsx" | sort
```

Also check for `src/{module}/__tests__/` directory.

If new test files found that are NOT in the trace file → flag them:
> "⚠️ New test files detected not yet in trace: {list}"

### STEP 3 — Display cleanly

```
🧪 {module} — Test Coverage

Auto-detected Test Files:
{table}

✅ Confirmed Covered:
{list}

❌ Intentionally Not Tested:
{table}

⚠️ Known Gaps:
{table}

Last Test Run:
  Unit:        {date} — {pass/fail}
  Integration: {date} — {pass/fail}
  E2E:         {date} — {pass/fail}
  Load:        {date} — {pass/fail}

{if new files flagged}
⚠️ New test files found since last update — run /trace coverage update {module}
```

---

## MODE 2 — Update: `/trace coverage update [module]`

### STEP 1 — Re-scan for test files

Run the same find command as MODE 1 STEP 2.

For each test file found, read it and extract `describe()` and `it()` / `test()` block names.

Show what was detected:
> "Found these test files: {list with extracted names}"

### STEP 2 — Ask questions one at a time

**Q1 (required):**
> "What scenarios are confirmed covered by these tests?
> List them one per line. Press Enter twice when done."

**Q2 (optional):**
> "Is anything intentionally NOT tested? Format: [what] | [why]
> Example: Stripe webhook raw body | Tested in Stripe's own test suite
> Press Enter to skip."

If answer given → parse into rows for the Intentional Not Tested table. Ask for each:
- "Documented by:" (default: `git config user.name`)

**Q3 (optional):**
> "Any known gaps — things that SHOULD be tested but aren't yet?
> Format: [what] | [risk: High/Med/Low] | [ticket # or N/A]
> Press Enter to skip."

**Q4 (required):**
> "When was the last test run? Enter results or press Enter to skip each type.
>
> Unit tests: (date + pass/fail + coverage % if known, e.g. '2026-05-24 — pass — 87%')"

Then ask for each test type separately:
> "Integration tests:"
> "E2E tests:"
> "Load tests:"

### STEP 3 — Write to file

Update Section 11 — Test Coverage:

```
## 11. Test Coverage

### Auto-detected Test Files
| File | What It Tests |
|------|---------------|
{list each file with extracted describe/it names}

### ✅ Confirmed Covered
{Q1 answers as bullet list}

### ❌ Intentionally Not Tested
| What | Why | Documented By | Date |
|------|-----|---------------|------|
{Q2 answers as table rows, or "⚠️ None documented yet | — | — | —" if skipped}

### ⚠️ Known Gaps — Not Tested Yet
| What | Risk Level | Ticket | Documented By |
|------|-----------|--------|---------------|
{Q3 answers as table rows, or "⚠️ None documented yet | — | N/A | —" if skipped}

### Last Test Run
- Unit: {Q4 unit answer or "Never"}
- Integration: {Q4 integration answer or "Never"}
- E2E: {Q4 e2e answer or "Never"}
- Load: {Q4 load answer or "Never"}
```

Update `> Last Updated:` at top with today's date and `git config user.name`.

### STEP 4 — Confirm

> "✅ Test coverage updated in src/{module}/{module}-trace.md"
>
> {if high risk gaps documented}:
> "⚠️ {n} high risk gap(s) documented. Consider prioritising these."
