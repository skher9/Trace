# /trace impact

Usage: `/trace impact [module]`

Show full blast radius if this module breaks or changes.

---

## STEP 1 — Read the target module's trace file

Read `src/{module}/{module}-trace.md`. Note its upstream and downstream dependencies.

---

## STEP 2 — Read ALL other trace files

Run:
```
find src/ -name "*-trace.md" | sort
```

Read every file found.

---

## STEP 3 — Cross-reference

Find every module whose trace file lists the target module in its **"This Module Depends On"** section.
These are direct dependents (downstream impact).

Then find every module that depends on THOSE modules.
These are indirect dependents.

---

## STEP 4 — Return impact analysis

```
💥 Impact Analysis — {module}

If {module} breaks right now:

🔴 Direct Impact (depends directly on {module}):
- {module} → {what happens if it breaks, from their trace file}
- {module} → {impact}

🟡 Indirect Impact (depends on affected modules):
- {module} → {downstream consequence}
- {module} → {downstream consequence}

⚠️ Critical Path:
{reconstruct the chain from trace files — example: auth → payments → orders → notifications}
(If any one breaks — {describe the user-facing consequence})

✅ Safe To Change Without Impact:
- Internal retry logic
- Error messages
- Internal logging
- Any behaviour not referenced by downstream modules
```

If no downstream dependents found:
> "No modules depend on {module}. Safe to change without downstream impact."

If trace files are missing impact details (⚠️ markers in dependency tables), note:
> "⚠️ Some impact estimates are incomplete — dependency tables have unreviewed sections. Run /trace update [module] to fill them in."
