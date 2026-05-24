# /trace list

Usage: `/trace list`

Show full project knowledge status and health score.

---

## STEP 1 — Read config

Read `.trace/config.json`. Extract:
- `project` name
- `stack`
- `securityMode`
- `environments` list
- `monitoring.tool` and `monitoring.configured`

---

## STEP 2 — Find all trace files

Run:
```
find src/ -name "*-trace.md" | sort
```

---

## STEP 3 — For each file, extract

- **Module name** → from filename
- **Owner** → Section 5, Primary Owner row. Show ⚠️ if still "Not yet assigned"
- **Last updated** → `> Last Updated:` line at top. Show "Never" if never manually updated
- **Memory count** → count `### [` inside Section 4 only
- **Decision count** → count `### [` inside Section 6 only
- **Runbook status** → ✅ if Section 7 has no ⚠️ placeholder, ❌ otherwise
- **Test status** → ✅ if Section 11 has at least one confirmed covered item and no "Never" in Last Test Run, ⚠️ if test files found but gaps undocumented, ❌ if no test files detected
- **Security status** → ✅ if Section 12 checklist has no ❌ items, ⚠️ if any ⚠️ items, ❌ if checklist is entirely unchecked
- **Unreviewed count** → total `⚠️` occurrences in the file

---

## STEP 4 — Calculate health score

Score out of 100, across all modules. Each category worth 20 points total, divided equally across modules.

**Category 1 — Ownership (20 pts)**
Every module has a named owner (not ⚠️) → full 20 pts
Partial → proportional

**Category 2 — Runbook (20 pts)**
Every module has a written runbook (✅) → full 20 pts
Partial → proportional

**Category 3 — Test Coverage (20 pts)**
Every module has test coverage documented (✅ or ⚠️, not ❌) → full 20 pts
Partial → proportional

**Category 4 — Security Checklist (20 pts)**
Every module has security checklist filled (✅ or ⚠️, not entirely unchecked) → full 20 pts
Partial → proportional

**Category 5 — Memory Log (20 pts)**
Every module has at least one memory log entry → full 20 pts
Partial → proportional

Total = sum of all 5 categories.

Score bands:
- 90–100 → 🟢 Excellent
- 70–89  → 🟡 Good
- 50–69  → 🟠 Needs work
- 0–49   → 🔴 High risk

---

## STEP 5 — Return formatted output

```
📚 Trace v3 — Project Knowledge Base

{project name} | Stack: {stack} | Security: {basic/enterprise}
Environments: {comma-separated list}
Monitoring: {tool name and "configured" OR "not configured ⚠️"}

| Module | Owner | Memories | Decisions | Runbook | Tests | Security | ⚠️ |
|--------|-------|----------|-----------|---------|-------|----------|----|
| payments | Priya Sharma | 3 | 2 | ✅ | ✅ | ✅ | 0 |
| auth | Rahul | 1 | 0 | ❌ | ⚠️ | ✅ | 3 |
| notifications | ⚠️ | 0 | 0 | ❌ | ❌ | ❌ | 7 |

Project Health Score: {score}/100 {band emoji} {label}

Risks:
{list only if present}
❌ Runbook missing — {module list}
❌ No owner — {module list}
❌ No tests documented — {module list}
❌ Security checklist empty — {module list}
❌ No memory entries — {module list}

💡 Next actions:
{show only for gaps that exist}
  /trace runbook update [module]   — write missing runbooks
  /trace owner set [module]        — assign missing owners
  /trace coverage update [module]  — document test gaps
  /trace security update [module]  — complete security checklist
  /trace remember [module]         — log first memory entry
```

Rules:
- Show Risks section only if gaps exist — omit entirely if score is 100
- Show only relevant Next actions — don't list commands for things already done
- Use "1 entry" / "N entries" (not "1 entries")
- Owner column shows ⚠️ if unassigned, name if assigned

If no trace files found:
> "No trace files found. Run /trace init to get started."
