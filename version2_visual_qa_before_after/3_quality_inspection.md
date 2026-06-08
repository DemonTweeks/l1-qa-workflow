# Quality Inspection — Per Image Rules

### To Do's (per After image)
- [ ] Run `analyze_image` with `annotate=true` and the inspection `prompt` containing all checks
- [ ] For each check, report status: PASS, FAIL, or N_A (not verifiable)
- [ ] For FAIL items, provide bbox pixel coordinates (localizable defects) and severity (Critical/Major/Minor)
- [ ] For N_A items, do NOT add to `findings`; just increment `not_verifiable_count`
- [ ] Accept integrated labeling on waterproofed cable ends; label not required when encapsulation is complete
- [ ] Exclude watermarks, metadata overlays, on‑screen annotations, IEPMS stamps from valid labels
- [ ] Allow one visible end per photo; pair‑level consistency handled across Before/After

### To Don'ts (Critical Rules)
- [ ] Do NOT mark FAIL for items not visible due to angle, distance, or framing → always N_A
- [ ] Do NOT require both cable ends in same photo
- [ ] Do NOT penalize format variations on otherwise correct labels
- [ ] Do NOT fail for unverifiable items (missing bbox means N_A, not FAIL)
- [ ] Do NOT use non‑structured text results; if `analyze_image` returns text, call again to get JSON
