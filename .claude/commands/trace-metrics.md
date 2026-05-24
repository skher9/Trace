# /trace metrics

Usage: `/trace metrics [module]`

View or update the Performance Baseline section of a module's trace file.

---

## STEP 1 — Read config and trace file

Read `.trace/config.json` in parallel with `src/{module}/{module}-trace.md`.

From config, check:
- `monitoring.tool` — which tool is configured (if any)
- `monitoring.configured` — whether an API key was found during init

If trace file doesn't exist → tell user to run `/trace init` first.

---

## STEP 2 — Branch based on monitoring config

### IF monitoring tool is configured (`monitoring.configured: true`):

Tell the user:
> "{tool} detected and configured.
> Metrics must be pulled manually from your {tool} dashboard — paste them below and I'll update the trace file."

Then ask each metric one at a time:

**API Response Time:**
> "API response time — expected value (e.g. 200ms):"
> "API response time — alert threshold (e.g. 500ms):"

**Error Rate:**
> "Error rate — expected value (e.g. 0.1%):"
> "Error rate — alert threshold (e.g. 1%):"

**Throughput:**
> "Throughput — expected value (e.g. 500 req/min):"
> "Throughput — alert threshold (e.g. 100 req/min):"

**DB Query Time:**
> "DB query time — expected value (e.g. 50ms):"
> "DB query time — alert threshold (e.g. 200ms):"

**Memory Usage:**
> "Memory usage — expected value (e.g. 256MB):"
> "Memory usage — alert threshold (e.g. 512MB):"

**Dashboard link (optional):**
> "Paste your {tool} dashboard URL for this module (press Enter to skip):"

If URL provided → save to `.trace/config.json` under `monitoring.dashboardUrl`.

Set source to `{tool name}` and Last measured to today.

---

### IF monitoring tool detected but NOT configured (`monitoring.tool` set, `monitoring.configured: false`):

> "⚠️ {tool} found in dependencies but no API key detected.
> Add your key to .env:
> {show the correct env var name for the detected tool}
> Then re-run /trace init to activate auto-detection.
>
> Falling back to manual entry."

Then run the same manual questions as above. Set source to `manual`.

---

### IF no monitoring tool (`monitoring.tool: null`):

> "No monitoring tool configured for this project.
> Entering metrics manually. Consider adding Datadog, New Relic, or similar for auto-pull.
>
> Run /trace metrics {module} anytime to update these values."

Then run the same manual questions. Set source to `manual`.

---

## STEP 3 — Update the trace file

Replace the Performance Baseline section (Section 9) with:

```
## 9. Performance Baseline

> Last measured: {today's date} | Source: {manual or tool name}
{if dashboard URL exists}: > Dashboard: {url}
{if tool configured}: > Monitoring: {tool} configured.
{if tool detected but not configured}: > ⚠️ {tool} detected but not configured. Add API key to .env.
{if no tool}: > ⚠️ No monitoring tool detected. Update manually with /trace metrics {module}.

### Metrics
| Metric | Expected | Alert Threshold | Last Measured |
|--------|----------|-----------------|---------------|
| API Response Time | {value} | {value} | {today's date} |
| Error Rate | {value} | {value} | {today's date} |
| Throughput | {value} | {value} | {today's date} |
| DB Query Time | {value} | {value} | {today's date} |
| Memory Usage | {value} | {value} | {today's date} |

### Monitoring Tool
{tool name and dashboard link, or "Not configured"}
```

Also update `> Last Updated:` at top of trace file with today's date and `git config user.name`.

---

## STEP 4 — Confirm

> "✅ Performance baseline updated in src/{module}/{module}-trace.md
> Source: {manual or tool name} | Measured: {today's date}
>
> Run /trace metrics {module} anytime to refresh these values."
