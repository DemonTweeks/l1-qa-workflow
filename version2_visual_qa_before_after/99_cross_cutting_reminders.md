# Cross‑Cutting Reminders

- [ ] Always prefer structured JSON output from `analyze_image`; retry if text is returned
- [ ] Use N_A generously when details are not visible; FAIL requires clear evidence
- [ ] Bbox coordinates must precisely cover the defect for FAIL items
- [ ] Legitimacy check is separate from quality checks; only the former can FAIL the whole section for staging
- [ ] Severity assignment: Critical for show‑stoppers, Major for important quality issues, Minor for cosmetic or low‑impact items
