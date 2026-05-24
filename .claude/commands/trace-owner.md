# /trace owner

Two modes: view ownership or manually set it.

---

## MODE 1 — View: `/trace owner [module]`

### STEP 1 — Read the trace file

Read `src/{module}/{module}-trace.md`. Find Section 5 — Ownership.

### STEP 2 — Also pull live from git

Run these in parallel:
```
git log --format="%an" src/{module}/ | sort | uniq -c | sort -rn | head -1
git log --format="%an" -1 src/{module}/
git log --format="%an" src/{module}/ | sort | uniq -c | sort -rn | head -3
```

### STEP 3 — Return formatted output

```
👤 {module} — Ownership

Primary:      {name} (most commits)
Last touched: {name} ({date of last commit})
On call:      {name1} → {name2} → {name3}

Last reviewed: {date from trace file}

To update manually: /trace owner set {module}
```

If git history is empty:
```
👤 {module} — Ownership

⚠️ No git history found for this module.
Ownership cannot be auto-assigned.

Run: /trace owner set {module} to assign manually.
```

---

## MODE 2 — Set manually: `/trace owner set [module]`

### STEP 1 — Show current ownership

Display the current Ownership section from the trace file.

### STEP 2 — Ask questions one at a time

**Q1:**
> "Who is the primary owner of this module?"

**Q2:**
> "Who is the secondary contact (first escalation)?"

**Q3:**
> "Who is the final escalation?"

### STEP 3 — Update the trace file

Replace the Ownership table in Section 5 with:

```
| Role | Name | How Assigned |
|------|------|--------------|
| Primary Owner | {Q1 answer} | Manually set |
| Last Modified By | {from git: git log --format="%an" -1 src/{module}/} | Last git commit |
| On Call Escalation | {Q1} → {Q2} → {Q3} | Manually set |

> Ownership manually set. Auto-assignment overridden.
> Last Reviewed: {today's date}
```

### STEP 4 — Confirm

> "✅ Ownership updated in src/{module}/{module}-trace.md"
