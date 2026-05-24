# Trace

Trace is a knowledge system for software teams. It lives inside your codebase as markdown files — one per module — and captures what your code does, why decisions were made, and what broke before.

No external tools. No dashboards. Just Claude and markdown.

---

## The problem Trace solves

New developer joins. Payments module is broken. They dig through git history for an hour, find nothing helpful, then ask the person who originally wrote it — who barely remembers.

Trace fixes this. Every bug fix, weird decision, and "don't touch this" warning gets logged in the module it belongs to. Next time someone hits the same wall, they run `/trace why payments` and get the answer in seconds.

---

## Setup (2 minutes)

1. Open Claude Code in your project
2. Run `/trace init`
3. Review the generated files and fill in the ⚠️ sections

That's it.

---

## Commands

| Command | What it does |
|---------|-------------|
| `/trace init` | Scan your project and generate trace files for every module |
| `/trace remember [module]` | Log a bug fix, decision, or gotcha to a module |
| `/trace why [module or keyword]` | Retrieve context fast — what broke here before, what to watch out for |
| `/trace list` | See all trace files and their status at a glance |
| `/trace update [module]` | Update a trace file when a module's behaviour changes |
| `/trace impact [module]` | See the full blast radius if this module breaks |

---

## How a trace file is structured

Each module gets one file: `src/{module}/{module}-trace.md`

| Section | What goes here |
|---------|---------------|
| Feature Understanding | What this module does, what files it has, how it behaves |
| Dependency Map | What this module needs, what needs this module, the critical path |
| Expected Behaviour | What should happen, what intentionally won't happen, conditional behaviours |
| Memory Log | Every bug, fix, and gotcha ever logged — append only, never deleted |

---

## The Memory Log rule

Memory Log entries are never edited or deleted. Always append. Newest at top.

This is intentional. The history of what broke and why is more valuable than a clean file.

---

## Example

See `TRACE-EXAMPLE.md` for a fully filled payments module trace file.
