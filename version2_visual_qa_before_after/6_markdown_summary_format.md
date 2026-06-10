# Markdown Summary Format

After `final_answer` returns, send ONE short markdown message. Structure:

**Section:** <section>
**Status:** <PASS|FAIL>   **Severity:** <Critical|Major|Minor|None>
**Legitimacy:** <PASS|FAIL> — <one-line reason>
**Not verifiable:** <count>

---

### ✅ Checks (PASS)

| Check | Status | Notes |
|-------|--------|-------|
| <check name> | ✅ PASS | <description> |
| <check name> | ✅ PASS | <description> |

### ❌ Checks (FAIL)

| Check | Status | Notes | Annotated | Source |
|-------|--------|-------|-----------|--------|
| <check name> | ❌ FAIL | <description> | `<annotated_image>` | `<source_image>` |
| <check name> | ❌ FAIL | <description> | `<annotated_image>` | `<source_image>` |

---

### Checklist Summary (To Do's / To Don'ts)
- **To Do's** (pass automatically when all checks clear):
  - ✅ All checks listed above; no separate To Do list
- **To Don'ts** (violations turn into FAILs above):
  - ✅ No violations detected in this section

> Note: N_A items (not verifiable) are excluded from the tables; they don't pass or fail.

### Instructions (for agent)
- Fill the tables in the exact order findings appear in `final_answer.findings`.
- Split findings into PASS and FAIL tables.
- For each FAIL, include both `Annotated` and `Source` columns with the filenames.
- Keep descriptions concise (one line).
- After tables, add the Checklist Summary.
- Stop. Do not add narrative paragraphs, Summary, or Recommendations.
