# /trace init

You are initialising the Trace knowledge system on this project. Follow these steps exactly, in order.

---

## STEP 1 — Read the project

Run these in parallel:
- Read `package.json` (or `pyproject.toml` / `requirements.txt` / `composer.json` if no package.json exists)
- Read `README.md` if it exists
- Run `find src/ -type f | sort` to scan all files in src/

If no README exists, ask the user ONE question only:
> "What does this project do? One sentence."

Wait for their answer before continuing.

Detect the tech stack from dependencies:
- `@nestjs/core` → NestJS (look for *.module.ts, *.service.ts, *.controller.ts)
- `next` or `react` → Next.js/React (look for pages/, app/, components/)
- `express` → Express (look for routes/, controllers/, middleware/)
- `django` or `fastapi` → Python (look for views.py, models.py, urls.py)
- `laravel` → Laravel (look for Controllers/, Models/, Services/)

Detect monitoring tools from package.json dependencies:
- `dd-trace` or `datadog` → Datadog
- `newrelic` → New Relic
- `elastic-apm-node` → Elastic APM
- `@opentelemetry/api` or `@opentelemetry/sdk-node` → OpenTelemetry
- `prom-client` or `prometheus-client` → Prometheus

If a monitoring tool is detected, also check for API keys in `.env` or environment:
- Datadog: `DATADOG_API_KEY` or `DD_API_KEY`
- New Relic: `NEW_RELIC_LICENSE_KEY`
- Elastic APM: `ELASTIC_APM_SECRET_TOKEN`

Set `monitoring.configured: true` in config only if both the package AND a key are found.

---

## STEP 2 — Detect real modules

A real module is any folder inside `src/` that contains at least one of:
- `*.service.ts` or `*.service.js`
- `*.module.ts`
- `*.controller.ts` or `*.controller.js`
- `index.ts` or `index.js`

**Ignore these folders:** `utils/`, `types/`, `constants/`, `helpers/`, `shared/`, `common/`, `config/`, `lib/`, `interfaces/`

List detected modules and ask:
> "I found these modules: [list]. Are these correct? Any to add or remove?"

Wait for confirmation before continuing.

---

## STEP 2B — V3 config questions (ask one at a time)

**Question 1 — Environments:**
> "What environments does your project have?
> Press enter for defaults (development, staging, production)
> Or type your own comma-separated list — e.g. development, qa, uat, staging, production"

If user presses enter with no input → use `["development", "staging", "production"]`
Otherwise → parse their comma-separated list into an array.

**Question 2 — Security mode:**
> "What security mode do you need?
> → basic — lightweight checklist (recommended for startups)
> → enterprise — full compliance and audit trail"

Wait for answer: `basic` or `enterprise`. Default to `basic` if unclear.

Save both answers immediately to `.trace/config.json`:
```json
{
  "traceVersion": "3.0",
  "project": "{project name from package.json or README}",
  "stack": "{detected stack}",
  "environments": [{parsed environment list}],
  "securityMode": "{basic or enterprise}",
  "monitoring": {
    "tool": "{detected tool name or null}",
    "configured": {true or false},
    "dashboardUrl": null
  },
  "createdAt": "{today's date}",
  "updatedAt": "{today's date}"
}
```

---

## STEP 3 — Read git history

For each detected module folder, run:
```
git log --since="90 days ago" --name-only --pretty=format:"%h %ad %s" --date=short -- src/{module}/
```

Also run:
```
git log --since="90 days ago" --pretty=format:"%h %ad %s" --date=short -- src/{module}/ | grep -iE "fix|bug|hotfix|patch|issue|revert|broke"
```

Identify:
- Files that changed most frequently (hot files)
- Commits with fix/bug/hotfix/patch keywords → candidates for Memory Log

For ownership, run these for each module:
```
# Primary owner — most commits
git log --format="%an" src/{module}/ | sort | uniq -c | sort -rn | head -1

# Last modified by
git log --format="%an" -1 src/{module}/

# On-call chain — top 3 by commit count
git log --format="%an" src/{module}/ | sort | uniq -c | sort -rn | head -3
```

If git history is empty or module is brand new — mark ownership as ⚠️ Not yet assigned.

---

## STEP 4 — Analyse imports and test files for each module

**Imports:**
For each module, read its main service file. Detect all import statements that reference other internal src/ paths. Example:
```
import { AuthService } from '../auth/auth.service'
```
This means this module depends on `auth`.

Build a dependency map: which modules import from which other modules.

**Test files:**
For each module, scan for test files matching any of:
- `src/{module}/*.spec.ts`
- `src/{module}/*.test.ts`
- `src/{module}/*.spec.js`
- `src/{module}/*.test.js`
- `src/{module}/*.spec.tsx`
- `src/{module}/*.test.tsx`
- `src/{module}/__tests__/`

For each test file found, read it and extract `describe()` and `it()` / `test()` block names. These become the "What It Tests" column in Section 11.

---

## STEP 5 — Generate trace files

For each detected module, create `src/{module}/{module}-trace.md`.

Read `.trace/config.json` to get: environments list, securityMode, monitoring tool.

Use exactly this format — all 12 sections, in this order:

```
# {Module Name} — Trace
> Last Updated: {today's date} | Updated By: {git config user.name}
> Auto-generated by Trace v3 | Review and update as needed

## 1. Feature Understanding
### What This Module Does
⚠️ Auto-detected from file names and imports — review and describe actual business purpose.

### Files In This Module
{list every file detected in this folder}

### Current Behaviour
⚠️ Review and fill in — describe step by step what happens when this module runs.

### Known Limitations
⚠️ Review and fill in — what can this module not do yet?

### Recent Changes
{paste relevant git log output for this folder, last 90 days}

---

## 2. Dependency Map

### This Module Depends On (Upstream)
| Module | Why | What Happens If It Breaks |
|--------|-----|--------------------------|
{fill from import analysis — mark unknown impacts with ⚠️}

### Modules That Depend On This (Downstream)
| Module | Why | What Happens If This Breaks |
|--------|-----|----------------------------|
{fill from reverse import analysis — mark unknown impacts with ⚠️}

### Critical Path
⚠️ Review and fill in — describe the chain this module sits in.

---

## 3. Expected Behaviour

### ✅ What Should Happen
⚠️ Review and fill in — list expected behaviours.

### ❌ Intentional Absences — What Will NOT Happen & Why
| What | Why Intentional | Documented By | Date |
|------|----------------|---------------|------|
| ⚠️ None documented yet | — | — | — |

### ⚠️ Conditional Behaviours
| Condition | Behaviour | Reason |
|-----------|-----------|--------|
| ⚠️ None documented yet | — | — |

---

## 4. Memory Log
> Never delete entries. Always append. Newest at top.

⚠️ No memories logged yet. Run /trace remember {module} to add the first one.

---

## 5. Ownership

| Role | Name | How Assigned |
|------|------|--------------|
| Primary Owner | {top committer from git log, or ⚠️ Not yet assigned} | Most commits to this module |
| Last Modified By | {last committer from git log, or ⚠️ Not yet assigned} | Last git commit to this module |
| On Call Escalation | {1st} → {2nd} → {3rd} | By commit frequency |

> Auto-assigned from git history.
> Update manually if incorrect.
> Last Reviewed: {today's date}

---

## 6. Decision Log
> Every architectural or product decision made in this module.
> Never delete. Always append. Newest at top.

⚠️ No decisions logged yet. Run /trace decide {module} to add one.

---

## 7. Runbook — When {Module Name} Breaks

> ⚠️ Runbook not yet written. Run /trace runbook update {module} to create it.
> High priority — document this before the first incident.

### Symptoms
⚠️ Fill in — what does broken look like?

### Check First (before touching anything)
- [ ] ⚠️ Fill in

### Common Fixes
| Symptom | Fix | Time to Fix |
|---------|-----|-------------|
| ⚠️ Fill in | ⚠️ Fill in | — |

### Escalate If
⚠️ Fill in — when is this beyond one person to fix?

### Escalation Path
{pulled from Ownership section above}

### Last Tested: Never
> Update this date every time you verify the runbook still works.

---

## 8. Changelog
> Append only. Newest at top.
> Only log meaningful changes — not every commit.

### {today's date} — Initial trace file created
- Trace v3 initialized
- Auto-generated from codebase scan

---

## 9. Performance Baseline

> Last measured: {today's date} | Source: manual
[IF monitoring tool detected AND configured]:
> Monitoring: {tool name} detected and configured. Run /trace metrics {module} to auto-populate.
[IF monitoring tool detected but NOT configured]:
> ⚠️ {tool name} detected in dependencies but no API key found. Add key to .env to enable auto-pull.
[IF no monitoring tool]:
> ⚠️ No monitoring tool detected. Consider adding Datadog, New Relic, or similar. Document metrics manually for now.

### Metrics
| Metric | Expected | Alert Threshold | Last Measured |
|--------|----------|-----------------|---------------|
| API Response Time | ⚠️ | ⚠️ | Never |
| Error Rate | ⚠️ | ⚠️ | Never |
| Throughput | ⚠️ | ⚠️ | Never |
| DB Query Time | ⚠️ | ⚠️ | Never |
| Memory Usage | ⚠️ | ⚠️ | Never |

### Monitoring Tool
{tool name if detected, otherwise "Not configured — run /trace metrics {module} to enter manually"}

---

## 10. Environment Differences

[Generate columns dynamically from .trace/config.json environments array]
[Example below uses default ["development", "staging", "production"] — adjust columns to match actual config]

| Behaviour | {env1} | {env2} | {env3} |
|-----------|--------|--------|--------|
| ⚠️ API Keys | ⚠️ | ⚠️ | ⚠️ |
| ⚠️ Rate Limiting | ⚠️ | ⚠️ | ⚠️ |
| ⚠️ Notifications | ⚠️ | ⚠️ | ⚠️ |
| ⚠️ Database | ⚠️ | ⚠️ | ⚠️ |
| ⚠️ Logging | ⚠️ | ⚠️ | ⚠️ |
| ⚠️ Feature Flags | ⚠️ | ⚠️ | ⚠️ |

> ⚠️ All rows are assumptions — review and correct each one.
> Run /trace env update {module} to fill in properly.

---

## 11. Test Coverage

### Auto-detected Test Files
[IF test files found]:
| File | What It Tests |
|------|---------------|
{list each test file and extracted describe/it names}

[IF no test files found]:
⚠️ No test files detected in this module.
This is a HIGH RISK gap. Run /trace coverage update {module} to document manually or add tests.

### ✅ Confirmed Covered
⚠️ Review and fill in — list confirmed tested scenarios.

### ❌ Intentionally Not Tested
| What | Why | Documented By | Date |
|------|-----|---------------|------|
| ⚠️ None documented yet | — | — | — |

### ⚠️ Known Gaps — Not Tested Yet
| What | Risk Level | Ticket | Documented By |
|------|-----------|--------|---------------|
| ⚠️ None documented yet | — | N/A | — |

### Last Test Run
- Unit: Never
- Integration: Never
- E2E: Never
- Load: Never

---

## 12. Security & Compliance

[IF securityMode is "basic" — generate BASIC section]:

### Sensitive Data Handled
| Data Type | How Stored | Who Can Access |
|-----------|-----------|----------------|
| ⚠️ Review and fill in | ⚠️ | ⚠️ |

### Basic Security Checklist
- [ ] Authentication required on all endpoints
- [ ] Input validation implemented
- [ ] Sensitive data never logged
- [ ] Environment variables used for secrets
- [ ] Dependencies regularly updated

### Known Security Considerations
| Issue | Risk | Status | Ticket |
|-------|------|--------|--------|
| ⚠️ None documented yet | — | — | — |

### Compliance Notes
⚠️ Document any relevant compliance requirements — GDPR, PCI, HIPAA etc.

---

[IF securityMode is "enterprise" — generate ENTERPRISE section instead]:

### Sensitive Data Handled
| Data Type | Classification | Storage | Encryption | Access Control | Retention |
|-----------|---------------|---------|------------|----------------|-----------|
| ⚠️ Review and fill in | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ |

### Security Checklist
#### Authentication & Authorization
- [ ] Authentication required on all endpoints
- [ ] Role based access control implemented
- [ ] JWT expiry configured correctly
- [ ] Refresh token rotation implemented
- [ ] Rate limiting on auth endpoints

#### Data Protection
- [ ] Data encrypted at rest
- [ ] Data encrypted in transit (TLS)
- [ ] PII never logged
- [ ] Secrets in environment variables only
- [ ] No sensitive data in git history

#### Input & Output
- [ ] Input validation on all endpoints
- [ ] SQL injection prevention
- [ ] XSS prevention
- [ ] CSRF protection
- [ ] Output sanitization

#### Dependencies & Infrastructure
- [ ] Dependencies audited (npm audit / pip check)
- [ ] No known CVEs in dependencies
- [ ] Dependency update policy documented
- [ ] Container security configured

### Compliance Status
| Standard | Status | Last Audit | Next Audit | Owner |
|----------|--------|-----------|------------|-------|
| GDPR | ⚠️ | ⚠️ | ⚠️ | ⚠️ |
| PCI DSS | ⚠️ | ⚠️ | ⚠️ | ⚠️ |
| HIPAA | ⚠️ | ⚠️ | ⚠️ | ⚠️ |
| SOC 2 | ⚠️ | ⚠️ | ⚠️ | ⚠️ |

### Known Security Vulnerabilities
| Issue | Severity | Status | Discovered | Ticket | Owner |
|-------|----------|--------|-----------|--------|-------|
| ⚠️ None documented yet | — | — | — | — | — |

### Penetration Testing
Last pen test: ⚠️ Not yet conducted
Conducted by: —
Findings: —
Next scheduled: ⚠️ Schedule required

### Incident History
| Date | Type | Severity | Resolution | RCA Link |
|------|------|----------|-----------|----------|
| ⚠️ None documented yet | — | — | — | — |
```

Fill in everything detectable from code and git. Mark anything needing human review with ⚠️. Never invent business context.

---

## STEP 6 — Output a summary

After creating all files, show:

```
🔍 Scanning project...
📦 Stack detected: {stack}
🌍 Environments: {list from config}
🔒 Security mode: {basic or enterprise}
📡 Monitoring: {tool name or "not configured"}
📁 Found {n} modules: {list}
🧠 Analysing imports, git history, ownership, and test files...
✅ Created: src/{module}/{module}-trace.md
✅ Created: src/{module}/{module}-trace.md
{etc}
💾 Config saved to .trace/config.json

🎉 Trace v3 initialised.

Next steps:
1. Review each trace file and fill in the ⚠️ sections
2. Run /trace metrics [module] to document performance baselines
3. Run /trace env update [module] to correct environment differences
4. Run /trace coverage update [module] to document test gaps
5. Run /trace security update [module] to complete security checklist
6. Run /trace runbook update [module] to write emergency runbooks
7. Run /trace list to see full project health score
```
