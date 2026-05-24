# Trace

Trace is a knowledge system for software teams. It lives inside your codebase as markdown files — one per module — right next to the code it describes. Committed to git, owned by the team, queryable via slash commands inside Claude Code.

No backend. No cloud. No database. Just markdown files any AI tool can read.

---

## The problem Trace solves

New developer joins. Payments module is broken. They dig through git history for an hour, find nothing helpful, then ask the person who originally wrote it — who barely remembers.

Trace fixes this. Every bug fix, weird decision, and "don't touch this" warning gets logged in the module it belongs to. Next time someone hits the same wall, they run `/trace why payments` and get the answer in seconds.

---

## Setup (2 minutes)

1. Open Claude Code in your project
2. Run `/trace init`
3. Review the generated files and fill in the ⚠️ sections
4. Install the pre-push hook (optional but recommended):

```bash
cp .trace/hooks/pre-push .git/hooks/pre-push
chmod +x .git/hooks/pre-push
```

---

## Commands

### Core commands

| Command | What it does |
|---------|-------------|
| `/trace init` | Scan your project and generate trace files for every module |
| `/trace remember [module]` | Log a bug fix, decision, or gotcha |
| `/trace why [module or keyword]` | Retrieve context fast — what broke here, what to watch out for |
| `/trace list` | See all trace files, owners, runbook status, and review gaps |
| `/trace update [module]` | Update a trace file when a module changes — also generates PR template |
| `/trace impact [module]` | See the full blast radius if this module breaks |

### Ownership

| Command | What it does |
|---------|-------------|
| `/trace owner [module]` | View who owns this module, auto-pulled from git history |
| `/trace owner set [module]` | Manually assign ownership and escalation chain |

### Decisions

| Command | What it does |
|---------|-------------|
| `/trace decide [module]` | Log an architectural or product decision with a mandatory revisit date |

### Runbooks

| Command | What it does |
|---------|-------------|
| `/trace runbook [module]` | View emergency runbook — clean, no noise, just the steps |
| `/trace runbook update [module]` | Create or update the runbook for a module |

---

## What's in a trace file

Each module gets one file: `src/{module}/{module}-trace.md`

| Section | What goes here |
|---------|---------------|
| 1. Feature Understanding | What this module does, files, behaviour, limitations |
| 2. Dependency Map | Upstream deps, downstream deps, critical path |
| 3. Expected Behaviour | What should happen, what intentionally won't, conditionals |
| 4. Memory Log | Every bug, fix, and gotcha — append only, never deleted |
| 5. Ownership | Primary owner, last modified by, on-call escalation chain |
| 6. Decision Log | Architectural decisions with mandatory revisit dates |
| 7. Runbook | Emergency steps for when this module breaks at 3am |
| 8. Changelog | Human-readable history of meaningful changes |

---

## Pre-push hook

The pre-push hook nudges developers to update trace files before pushing. It checks which modules changed and reminds you if trace files are missing or incomplete.

Install once per developer:
```bash
cp .trace/hooks/pre-push .git/hooks/pre-push
chmod +x .git/hooks/pre-push
```

The hook is non-blocking — it shows warnings and prompts, but always lets the push through.

---

## Rules

1. Memory Log and Decision Log are append-only — never delete entries
2. Every decision needs a Revisit When date — even "Never — this is permanent"
3. Runbooks are for 3am emergencies — every word must count
4. Mark auto-generated content with ⚠️ so humans know what needs review
5. Never invent business context — only write what is known from code and git

---

## Example

See `TRACE-EXAMPLE.md` for a complete v2 trace file with all 8 sections filled.
