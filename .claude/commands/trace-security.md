# /trace security

Three modes: view, update checklist, or switch security mode project-wide.

---

## MODE 1 — View: `/trace security [module]`

### STEP 1 — Read trace file

Read `src/{module}/{module}-trace.md`. Extract Section 12 — Security & Compliance only.

### STEP 2 — Display cleanly

No extra commentary. Just the section content:

```
🔒 {module} — Security & Compliance

{full Section 12 content}

Run /trace security update {module} to update the checklist.
```

---

## MODE 2 — Update: `/trace security update [module]`

### STEP 1 — Read config and trace file

Read `.trace/config.json` to get `securityMode`.
Read `src/{module}/{module}-trace.md` to get current Section 12.

Show current checklist state to the developer.

### STEP 2 — Branch by security mode

---

#### IF securityMode is "basic":

Ask questions one at a time.

**Q1:**
> "What sensitive data does this module handle?
> Format: [data type] | [how stored] | [who can access]
> Example: Card numbers | Stripe vault — never stored locally | Payment service only
> Press Enter to skip."

**Q2 — Walk through the 5-item checklist:**

For each item, ask:
> "Authentication required on all endpoints? Yes / No / Partial"
> "Input validation implemented? Yes / No / Partial"
> "Sensitive data never logged? Yes / No / Partial"
> "Environment variables used for secrets? Yes / No / Partial"
> "Dependencies regularly updated? Yes / No / Partial"

Mark each item ✅ if Yes, ❌ if No, ⚠️ if Partial.

**Q3:**
> "Any known security considerations or open issues?
> Format: [issue] | [risk: High/Med/Low] | [status: Open/Resolved] | [ticket # or N/A]
> Press Enter to skip."

**Q4:**
> "Any compliance requirements relevant to this module? (GDPR, PCI, HIPAA, etc.)
> Press Enter to skip."

---

#### IF securityMode is "enterprise":

Ask questions one at a time, category by category.

**Sensitive Data:**
> "What sensitive data does this module handle?
> Format: [data type] | [classification: PII/PCI/PHI/Internal] | [storage] | [encryption] | [access control] | [retention period]
> Press Enter when done adding rows."

**Checklist — Authentication & Authorization:**
Walk through each item:
> "Authentication required on all endpoints? Yes / No / Partial"
> "Role based access control implemented? Yes / No / Partial"
> "JWT expiry configured correctly? Yes / No / Partial / N/A"
> "Refresh token rotation implemented? Yes / No / Partial / N/A"
> "Rate limiting on auth endpoints? Yes / No / Partial"

**Checklist — Data Protection:**
> "Data encrypted at rest? Yes / No / Partial / N/A"
> "Data encrypted in transit (TLS)? Yes / No / Partial"
> "PII never logged? Yes / No / Partial"
> "Secrets in environment variables only? Yes / No / Partial"
> "No sensitive data in git history? Yes / No / Unknown"

**Checklist — Input & Output:**
> "Input validation on all endpoints? Yes / No / Partial"
> "SQL injection prevention? Yes / No / N/A"
> "XSS prevention? Yes / No / N/A"
> "CSRF protection? Yes / No / N/A"
> "Output sanitization? Yes / No / Partial"

**Checklist — Dependencies & Infrastructure:**
> "Dependencies audited recently? Yes / No"
> "No known CVEs in dependencies? Yes / No / Unknown"
> "Dependency update policy documented? Yes / No"
> "Container security configured? Yes / No / N/A"

**Compliance Status:**
> "Which compliance standards apply? (GDPR / PCI DSS / HIPAA / SOC 2 / None / Other)"

For each standard that applies, ask:
> "Status for {standard}? compliant / partial / not applicable"
> "Last audit date for {standard}? (or press Enter to skip)"
> "Next audit date for {standard}? (or press Enter to skip)"
> "Owner for {standard} compliance?"

**Known Vulnerabilities:**
> "Any known security vulnerabilities to document?
> Format: [issue] | [severity: Critical/High/Med/Low] | [status: Open/In Progress/Resolved] | [ticket #]
> Press Enter to skip."

**Penetration Testing:**
> "Has this module been pen tested? Yes / No"

If Yes:
> "When? (date)"
> "By whom? (internal / firm name)"
> "Summary of findings? (one line or press Enter to skip)"
> "Next scheduled pen test? (date or press Enter to skip)"

---

### STEP 3 — Write to file

Update Section 12 with all answers. Mark each checklist item:
- `✅` if Yes / compliant
- `❌` if No
- `⚠️` if Partial / Unknown
- Strikethrough text `~~item~~` if N/A (or just mark N/A inline)

Update `> Last Updated:` at top with today's date and `git config user.name`.

### STEP 4 — Confirm

> "✅ Security & Compliance updated in src/{module}/{module}-trace.md"

If any checklist items are ❌ Critical or High severity vulnerabilities:
> "🔴 {n} critical/high issue(s) need attention — review before next deploy."

---

## MODE 3 — Switch mode: `/trace security mode [basic/enterprise]`

### STEP 1 — Confirm scope

Read `.trace/config.json` to show current mode.

> "Current security mode: {current mode}
> Switching to: {new mode}
>
> This will update ALL trace files. Existing security data is preserved.
> New fields in enterprise mode will be added as ⚠️ until reviewed.
> Continue? Yes / No"

Wait for confirmation.

### STEP 2 — Update config

Update `securityMode` in `.trace/config.json` to new mode.
Update `updatedAt` to today's date.

### STEP 3 — Update all trace files

Run:
```
find src/ -name "*-trace.md" | sort
```

For each trace file:
- If switching basic → enterprise: expand Section 12 to enterprise format, preserve existing data, add new fields as ⚠️
- If switching enterprise → basic: collapse to basic format, preserve sensitive data table and known issues, drop detailed compliance tables (warn user that data will be removed)

If switching enterprise → basic, warn first:
> "⚠️ Switching from enterprise to basic will remove detailed compliance tables, pen test history, and incident log from all trace files.
> Existing data cannot be recovered after this change.
> Are you sure? Type 'confirm' to proceed."

### STEP 4 — Confirm

> "✅ Security mode switched to {new mode}.
> Updated {n} trace files.
>
> Run /trace security update [module] to fill in new fields."
