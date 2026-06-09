# Markdown Summary Format

After `final_answer` returns, send ONE short markdown message:

```
**Section:** <section>
**Status:** <PASS|FAIL>   **Severity:** <Critical|Major|Minor|None>
**Legitimacy:** <PASS|FAIL> — <one-line reason>
**Not verifiable:** <count>

**Findings:**
- [PASS] <check> — <requirement>
 <description>
- [FAIL] <check> — <requirement>
 <description>
 *Annotated:* `<filename>`   (omit this line if no annotated_image)
 *Source:* `<filename>`   (omit this line if no source_image)
```

### To Do's
- [ ] Use exactly the above structure
- [ ] List findings in the same order as in `final_answer.findings`
- [ ] Stop after the list; do not add Summary or Recommendations

### To Don'ts
- [ ] Do NOT add extra narrative paragraphs
- [ ] Do NOT reorder findings
- [ ] Do NOT include N_A items in the markdown summary
