# Findings Submission (final_answer)

### To Do's
- [ ] Call `final_answer` ONCE with structured payload:
  - [ ] `section`: section name (e.g., "2.1/2.2 IDU Installation")
  - [ ] `status`: FAIL if any finding is FAIL, else PASS
  - [ ] `severity`: highest severity among FAIL findings, or "None"
  - [ ] `legitimacy`: object with `status` (PASS/FAIL/N_A) and `reason`
  - [ ] `findings[]`: one entry per PASS or FAIL check actually performed; include:
    - `check`: short label (e.g., "Cable labeling")
    - `requirement`: full rule text from task
    - `status`: PASS or FAIL
    - `severity`: only for FAIL (Critical/Major/Minor)
    - `description`: one‑line observation
    - `annotated_image`: filename for FAIL findings with bbox; omit for PASS or non‑localizable FAIL
    - `source_image`: filename of the original After image analyzed; set on FAIL findings; omit only if the source image is unknown
  - [ ] `not_verifiable_count`: number of checks marked N_A (not added to `findings`)
- [ ] For every FAIL finding that can be localized, call `annotate_regions` with bbox and severity color (Critical=red, Major=orange, Minor=yellow)
- [ ] Capture annotated image filenames and include them in corresponding `findings`
- [ ] If a defect is visible but too small/unclear to bbox reliably, mark in findings as "N_A (defect observed, localization uncertain)" and do NOT include annotated image

### To Don'ts
- [ ] Do NOT call `final_answer` multiple times; only once
- [ ] Do NOT include N_A items in `findings`; they belong only in `not_verifiable_count`
- [ ] Do NOT omit `requirement` field; always include full rule text for every finding
- [ ] Do NOT use silent annotation; let annotated images appear in conversation
