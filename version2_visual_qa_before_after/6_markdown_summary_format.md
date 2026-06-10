# Markdown Summary Format

After `final_answer` returns, send ONE short markdown message. Structure:

**Section:** <section>
**Status:** <PASS|FAIL>   **Severity:** <Critical|Major|Minor|None>
**Legitimacy:** <PASS|FAIL> — <one-line reason>
**Not verifiable:** <count>

---

### 📋 Compliance Table

| Category | Requirement (To Do / To Don't) | Status | Evidence / Observation |
|----------|--------------------------------|--------|------------------------|

For each finding (in the order returned), add one row:

- **Category**: the `check` value from the finding
- **Requirement**: the full `requirement` text (should already include "To Do:" or "To Don't:" prefix)
- **Status**: show ✅ for PASS, ❌ for FAIL, ⚠️ for N/A (if any)
- **Evidence / Observation**:
  - For PASS: a brief note like "Compliant" or the description
  - For FAIL: the `description` plus new lines `*Annotated:* <file>` and `*Source:* <file>` when present
  - For N/A: "Not verifiable"

Stop after the table. Do not add bullet lists or extra sections.