# /trace env

Three modes: view, update, or add a new environment.

---

## MODE 1 — View: `/trace env [module]`

### STEP 1 — Read config and trace file

Read `.trace/config.json` and `src/{module}/{module}-trace.md` in parallel.

### STEP 2 — Extract and display Section 10

Show the Environment Differences table cleanly. No extra commentary.

```
🌍 {module} — Environment Differences

| Behaviour | {env1} | {env2} | {env3} |
|-----------|--------|--------|--------|
{table rows}

⚠️ = not yet reviewed
Run /trace env update {module} to fill in missing values.
```

---

## MODE 2 — Update: `/trace env update [module]`

### STEP 1 — Read config and trace file

Read `.trace/config.json` to get the environments list.
Read `src/{module}/{module}-trace.md` to get the current table.

Show the current Environment Differences table so the developer knows what they're updating.

### STEP 2 — Ask what behaviour to update

> "Which behaviour row do you want to update?
> Existing rows: {list current row names}
> Or type a new behaviour name to add a row."

Wait for answer.

### STEP 3 — Ask values per environment

For each environment in the config list, ask one at a time:

> "What is the behaviour for {behaviour} in {env1}?"
> "What is the behaviour for {behaviour} in {env2}?"
> "What is the behaviour for {behaviour} in {env3}?"
{...repeat for all configured environments}

### STEP 4 — Ask if more rows to update

> "Update another row? Yes / No"

If Yes → go back to STEP 2.
If No → write to file.

### STEP 5 — Write to file

Update or add the row in Section 10. Remove the ⚠️ prefix from any row that is now fully filled.

Update `> Last Updated:` at top with today's date and `git config user.name`.

### STEP 6 — Confirm

> "✅ Environment differences updated in src/{module}/{module}-trace.md"

---

## MODE 3 — Add environment: `/trace env add`

This adds a new environment column to the project config AND updates every trace file.

### STEP 1 — Read current config

Read `.trace/config.json`. Show current environments:
> "Current environments: {list}
> What new environment do you want to add? (e.g. uat, qa, sandbox)"

Wait for answer.

### STEP 2 — Confirm scope

> "This will add '{new env}' as a column to the Environment Differences table in ALL trace files.
> Existing rows will get ⚠️ as the value for the new column until reviewed.
> Continue? Yes / No"

Wait for confirmation.

### STEP 3 — Update config

Add the new environment to the `environments` array in `.trace/config.json`.
Update `updatedAt` to today's date.

### STEP 4 — Update all trace files

Run:
```
find src/ -name "*-trace.md" | sort
```

For each trace file found:
- Find Section 10 — Environment Differences
- Add the new environment as a column header
- Add ⚠️ as the value for every existing row in the new column

### STEP 5 — Confirm

> "✅ Environment '{new env}' added to project config.
> Updated {n} trace files.
>
> Run /trace env update [module] to fill in the new column for each module."
