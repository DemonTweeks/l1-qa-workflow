# Visual QA: Before/After Comparison — Agent Checklists

**Purpose:** Convert sub‑agent requirements into actionable, image‑driven checklists for visual QA.

---

## Table of Contents

1. [Pre‑Check: Build Composites](#pre-check-build-composites)
2. [Legitimacy Check (Before vs After)](#legitimacy-check)
3. [Quality Inspection — Per Image Rules](#quality-inspection)
4. [Labeling Policy (All Components)](#labeling-policy)
5. [Findings Submission](#findings-submission)
6. [Markdown Summary Format](#markdown-summary)

---

## Pre‑Check: Build Composites

### To Do's
- [ ] Build composite from all Before images
- [ ] Build composite from all After images
- [ ] Use composites for legitimacy assessment only; use individual images for quality inspection

### To Don'ts
- [ ] Do NOT use composites for quality inspection; use individual After photos
- [ ] Do NOT skip composite step; needed for legitimacy overview

---

## Legitimacy Check

### To Do's
- [ ] Analyze both composites to judge whether Before photo is legitimate (shows actual pre‑work site condition)
- [ ] Accept Before if it reasonably represents pre‑work state even if equipment appears new/replaced
- [ ] FAIL only on clear and convincing evidence of staging/fraud:
  - [ ] Lab/test environment (benches, test gear, no real installation)
  - [ ] Equipment in factory packaging or shipping boxes
  - [ ] Equipment appears unused/pristine inconsistent with 'before' state
  - [ ] Same equipment shown in both Before and After with no change
  - [ ] Suspicious date/time stamps implying improper sequencing
- [ ] If legitimacy questionable but not definitive, mark N_A with note rather than FAIL

### To Don'ts
- [ ] Do NOT reject solely because Before shows new or replaced equipment
- [ ] Do NOT fail based on mild suspicion; require clear evidence
- [ ] Do NOT treat poor photo quality as staging → use N_A for unverifiable aspects

---

## Quality Inspection

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

---

## Labeling Policy (All Components)

### To Do's
- [ ] For **non‑waterproofed cable ends** (exposed connector and jacket):
  - [ ] Require physical ID tag (yellow or white) attached near termination, or jacket printing/stamping
  - [ ] If label not visible in current image → N_A (may be visible elsewhere)
  - [ ] FAIL only if component is clearly shown without any label
- [ ] For **waterproofed cable ends** (fully encapsulated with heat shrink, tape, moulded boot, sealant, etc.):
  - [ ] No label required — focus on waterproofing completeness
  - [ ] Mark N_A for labeling if encapsulation is visible and complete
  - [ ] FAIL only if waterproofing itself is inadequate (exposed parts)
- [ ] For **antennas/equipment**:
  - [ ] Require alphabet stencil or stamped characters
  - [ ] If label not visible in current image → N_A
  - [ ] FAIL only if antenna/equipment clearly shown without label
- [ ] For **breaker tags** (IDU Power):
  - [ ] Yellow ID tag must be attached to breaker (any surface) and clearly readable
  - [ ] If breaker visible but tag not shown → N_A
  - [ ] FAIL only if breaker is shown without tag

### To Don'ts
- [ ] Do NOT count watermarks, metadata, on‑screen annotations, IEPMS stamps as labels
- [ ] Do NOT require label format to match exact NE/FE pattern; any clear identification is acceptable
- [ ] Do NOT fail for missing label on fully encapsulated connectors
- [ ] Do NOT fail for label placement on breaker surfaces other than front (as long as readable)
- [ ] Do NOT fail for minor cosmetic differences or extra text on labels

---

## Findings Submission (final_answer)

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
  - [ ] `not_verifiable_count`: number of checks marked N_A (not added to `findings`)
- [ ] For every FAIL finding that can be localized, call `annotate_regions` with bbox and severity color (Critical=red, Major=orange, Minor=yellow)
- [ ] Capture annotated image filenames and include them in corresponding `findings`
- [ ] If a defect is visible but too small/unclear to bbox reliably, mark in findings as "N_A (defect observed, localization uncertain)" and do NOT include annotated image

### To Don'ts
- [ ] Do NOT call `final_answer` multiple times; only once
- [ ] Do NOT include N_A items in `findings`; they belong only in `not_verifiable_count`
- [ ] Do NOT omit `requirement` field; always include full rule text for every finding
- [ ] Do NOT use silent annotation; let annotated images appear in conversation

---

## Markdown Summary Format

After `final_answer` returns, send ONE short markdown message:

```
**Section:** <section>
**Status:** <PASS|FAIL>   **Severity:** <Critical|Major|Minor|None>
**Legitimacy:** <PASS|FAIL> — <one-line reason>
**Not verifiable:** <count>

**Findings:**
- [<PASS|FAIL>] <check> — <requirement>
 <description>
 *Annotated:* `<filename>`   (omit this line if no annotated_image)
- [<PASS|FAIL>] <check> — <requirement>
 <description>
```

### To Do's
- [ ] Use exactly the above structure
- [ ] List findings in the same order as in `final_answer.findings`
- [ ] Stop after the list; do not add Summary or Recommendations

### To Don'ts
- [ ] Do NOT add extra narrative paragraphs
- [ ] Do NOT reorder findings
- [ ] Do NOT include N_A items in the markdown summary

---

## Cross‑Cutting Reminders

- [ ] Always prefer structured JSON output from `analyze_image`; retry if text is returned
- [ ] Use N_A generously when details are not visible; FAIL requires clear evidence
- [ ] Bbox coordinates must precisely cover the defect for FAIL items
- [ ] Legitimacy check is separate from quality checks; only the former can FAIL the whole section for staging
- [ ] Severity assignment: Critical for show‑stoppers, Major for important quality issues, Minor for cosmetic or low‑impact items

---

**End of checklists.** Sub‑agent should tick these items during visual QA before submitting findings.
