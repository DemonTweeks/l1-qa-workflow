# L1 QA Workflow - Quick Reference & Usage Guide

## Overview
The refined L1 QA Workflow skills now produce consistent, well-structured output with:
- **Unified Results Summary Table** (all sections at a glance)
- **Numbered Critical Findings** (easy reference and traceability)
- **Severity Summary** (quick impact assessment)
- **Rejection Reasons** (clear failure causes)

---

## For Section-by-Section Checking

### How to Request a Check
When asking the agent to check a specific section:

```
Please check section 2.7/2.8 (IF Cable) from the attached PDF report.
```

### Expected Output per Section

The agent will return:

1. **Structured Findings** (JSON payload):
   - Check name
   - Full requirement text
   - Status (PASS/FAIL/N_A)
   - Severity (for FAIL only)
   - Description of observation
   - Annotated image filename (for FAIL findings)
   - Source image filename

2. **Markdown Summary** (for human review):
   ```
   ### Section: 2.7/2.8 IF Cable
   **Status:** FAIL  |  **Severity:** Critical
   
   **Legitimacy Check:** PASS
   **Not Verifiable Count:** 1
   
   #### Findings Summary:
   - [FAIL] Waterproofing on connectors — ...
     - Observation: ...
     - *Annotated:* `filename.png`
     - *Source:* `filename.png`
   ```

---

## For Complete Report Generation

### How to Request a Full Report
```
Please review the complete L1 QA report in the attached PDF.
```

### Report Generation Process
1. **Parse PDF** → Extract images and sections
2. **Validate Header** → Check metadata and site ID
3. **Spawn Sub-agents** → Check each section in parallel
4. **Aggregate Results** → Combine all findings
5. **Generate Report** → Output in standard format

### Report Output Sections
1. Overall Status (PASS/REJECT)
2. Header Validation result
3. **Results Summary Table** — all sections with status/severity
4. **Critical Findings** — detailed failure analysis (if any)
5. Missing sections
6. Severity summary
7. Rejection reasons (if REJECT)

---

## Output Format Summary

### Status Indicators
- ✅ **PASS** — Requirement satisfied
- ❌ **FAIL** — Requirement violated
- ⚠️ **N/A** — Not verifiable in provided photos

### Severity Levels
- 🔴 **Critical** — Immediate safety or compliance risk
- 🟠 **Major** — Significant issue requiring correction
- 🟡 **Minor** — Non-critical observation
- ⚪ **None** — No failures

### Annotation References
Every FAIL finding includes:
- **Annotated:** Image with bounding boxes highlighting the issue
- **Source:** Original image where the issue was found

---

## Using the Refined Skills

### Key Advantages

1. **Consistency**
   - Same format regardless of which skill runs
   - Predictable structure across all reports

2. **Traceability**
   - Sequential numbering of findings
   - Full requirement text included
   - Source and annotated images referenced

3. **Clarity**
   - Results table shows all sections at once
   - Critical findings highlighted separately
   - Severity levels clearly marked

4. **Efficiency**
   - Section-by-section checking available
   - Parallel processing of multiple sections
   - Quick reference for key metrics

### For Section-by-Section Workflow

Ask the agent to check specific sections:
```
Check 2.1/2.2, 2.7/2.8, and 3.8/3.9
```

The agent will:
1. Load the appropriate checklist for each section
2. Extract and analyze images
3. Return structured findings for each
4. Provide a summary per section

---

## Files & References

**Main Orchestrators:**
- `version2_l1_qa_workflow_v2/SKILL.md` — Primary v2 workflow (recommended)
- `l1_qa_workflow_v2/SKILL.md` — Original workflow (legacy)

**Visual QA Sub-agents:**
- `version2_visual_qa_before_after/SKILL.md` — Primary v2 sub-agent (recommended)
- `visual_qa_before_after/SKILL.md` — Original sub-agent (legacy)

**Reference Documents:**
- `OUTPUT_FORMAT_TEMPLATE.md` — Detailed format specification
- `EXAMPLE_OUTPUT.md` — Complete example reports (PASS and REJECT)

**Checklists:**
- `version2_l1_qa_workflow_v2/CHECKLIST_INDEX.txt` — All section checklists
- `version2_l1_qa_workflow_v2/*.md` — Individual section requirements

---

## Tips for Best Results

1. **Provide clear section names** — Use "2.7/2.8 IF Cable" not just "Cable"
2. **Include full PDF** — Orchestrator needs complete report for header validation
3. **Check one or multiple** — Can request individual or all sections
4. **Review annotated images** — They pinpoint exact issues
5. **Use rejection reasons** — Explains why report fails validation

---

## Troubleshooting

**Issue:** Findings marked as N/A
- **Reason:** Component not visible in provided photos (angle, distance, framing)
- **Solution:** Ensure all required photos are included for the section

**Issue:** Missing Annotated images for FAIL findings
- **Reason:** Issue too small or unclear to localize with bbox
- **Solution:** Review Source image; annotation indicates uncertainty in localization

**Issue:** Requirement text seems incomplete
- **Reason:** Text may be truncated in table display
- **Solution:** Check Critical Findings section for full requirement text

---

## Quality Standards

The refined workflow ensures:
- ✅ Every FAIL finding has supporting evidence (annotated image)
- ✅ Every requirement is stated in full (no abbreviations)
- ✅ Severity levels are consistent across all reports
- ✅ Section ordering is predictable and maintained
- ✅ Results can be cross-referenced between orchestrator and sub-agents
