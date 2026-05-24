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
3. Answer two setup questions (environments + security mode)
4. Review the generated files and fill in the ⚠️ sections
5. Install the pre-push hook (optional but recommended):

```bash
cp .trace/hooks/pre-push .git/hooks/pre-push
chmod +x .git/hooks/pre-push
```

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
| 9. Performance Baseline | Expected metrics, alert thresholds, monitoring tool |
| 10. Environment Differences | How this module behaves differently per environment |
| 11. Test Coverage | Detected test files, gaps, risk levels, last run dates |
| 12. Security & Compliance | Checklist, sensitive data, compliance status, vulnerabilities |

---

## Commands

### Core
| Command | What it does |
|---------|-------------|
| `/trace init` | Scan project, ask setup questions, generate all 12 sections |
| `/trace remember [module]` | Log a bug fix, decision, or gotcha |
| `/trace why [module or keyword]` | Retrieve context — what broke, what to watch out for |
| `/trace list` | Project health score + status of every module |
| `/trace update [module]` | Update docs when a module changes — generates PR template |
| `/trace impact [module]` | Full blast radius if this module breaks |

### Ownership
| Command | What it does |
|---------|-------------|
| `/trace owner [module]` | View ownership, auto-pulled from git history |
| `/trace owner set [module]` | Manually assign owner and escalation chain |

### Decisions
| Command | What it does |
|---------|-------------|
| `/trace decide [module]` | Log a decision — Revisit When is mandatory |

### Runbooks
| Command | What it does |
|---------|-------------|
| `/trace runbook [module]` | View emergency runbook — clean, no noise |
| `/trace runbook update [module]` | Create or update the runbook |

### Performance
| Command | What it does |
|---------|-------------|
| `/trace metrics [module]` | View or update performance baseline metrics |

### Environments
| Command | What it does |
|---------|-------------|
| `/trace env [module]` | View environment differences table |
| `/trace env update [module]` | Update environment rows |
| `/trace env add` | Add a new environment column to all trace files |

### Test Coverage
| Command | What it does |
|---------|-------------|
| `/trace coverage [module]` | View test coverage — re-scans for new test files live |
| `/trace coverage update [module]` | Document confirmed coverage, gaps, and last run |

### Security
| Command | What it does |
|---------|-------------|
| `/trace security [module]` | View security & compliance section |
| `/trace security update [module]` | Walk through security checklist |
| `/trace security mode [basic/enterprise]` | Switch security mode project-wide |

---

## Project config

Trace stores project-level settings in `.trace/config.json`:

```json
{
  "traceVersion": "3.0",
  "project": "my-api",
  "stack": "NestJS",
  "environments": ["development", "staging", "production"],
  "securityMode": "basic",
  "monitoring": {
    "tool": "datadog",
    "configured": true,
    "dashboardUrl": null
  },
  "createdAt": "2026-05-24",
  "updatedAt": "2026-05-24"
}
```

`securityMode`: `basic` (5-item checklist) or `enterprise` (full compliance audit trail)
`environments`: drives the column count in Section 10 across all trace files

---

## Pre-push hook

Nudges developers to update trace files before pushing. Checks which modules changed, warns if trace files are missing or have unwritten runbooks.

Install once per developer:
```bash
cp .trace/hooks/pre-push .git/hooks/pre-push
chmod +x .git/hooks/pre-push
```

Non-blocking — always lets the push through.

---

## Monitoring tool detection

Trace auto-detects monitoring tools from `package.json` during `/trace init`:

| Package | Tool |
|---------|------|
| `dd-trace`, `datadog` | Datadog |
| `newrelic` | New Relic |
| `elastic-apm-node` | Elastic APM |
| `@opentelemetry/api` | OpenTelemetry |
| `prom-client` | Prometheus |

If a tool is detected and an API key is found in `.env`, `/trace metrics` prompts for manual paste from the dashboard and stores the values.

---

## Rules

1. Memory Log and Decision Log are append-only — never delete entries
2. Every decision needs a Revisit When date — even "Never — this is permanent"
3. Runbooks are for 3am emergencies — every word must count
4. Mark auto-generated content with ⚠️ so humans know what needs review
5. Never invent business context — only write what is known from code and git
6. `/trace env add` updates ALL trace files — always confirm scope first

---

## Example

See `TRACE-EXAMPLE.md` for a complete trace file with all 12 sections filled.
