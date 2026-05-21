# Refinement Process — Step-by-Step

## Overview
This document outlines the complete workflow to refine L1 QA skills based on practical testing failures extracted from `tipic testing summary.xlsx`.

---

## Refinement Steps (correct the skills to match the approved report)

1. **Treat the approved sample as the correct ground truth**
   - `Test_reports_stuffs/Report 60 [Quality Self-check]L1 Approved.pdf` is valid and should pass.
   - The current skills are wrongly rejecting correct content and must be adjusted.

2. **Extract approved report structure and evidence**
   - Parse the sample report and capture its section numbers, page text, and image filenames.
   - Record how the approved report presents IDU, power, grounding, cable, and NMS evidence.

3. **Compare the approved sample against current skill logic**
   - Review `l1_qa_workflow_v2/SKILL.md` and `visual_qa_before_after/SKILL.md` without editing them.
   - Identify rules that reject the approved report, are ambiguous, or disagree with the valid report content.
   - Also compare against failure items in `tipic testing summary.xlsx` to retain coverage of true defects.

4. **Draft corrected skill requirements first**
   - Write the intended behavior in plain language before changing any skill files.
   - Ensure the approved report remains acceptable under the new language.
   - Preserve stricter rules only when they address genuine failures and do not reject approved evidence.

5. **Validate the draft using the approved report and failure list**
   - Confirm the approved sample would pass the drafted requirements.
   - Confirm the existing failure items would still be caught.
   - Refine the wording until both are satisfied.

6. **Update the skill files only after the draft is stable**
   - Apply changes to `l1_qa_workflow_v2/SKILL.md` and `visual_qa_before_after/SKILL.md` after the draft is confirmed.
   - Use the approved sample as the validation anchor.

7. **Re-run validation and document the results**
   - Execute the revised workflow against the approved sample to confirm the report passes.
   - Document changes in `tasks/refinement_tasks.md` and `tasks/changelog.md`.
   - Commit the final updates with task references.

## Phase 1: Analysis & Planning ✓ COMPLETED

### Step 1.1: Extract Refinement Items
- **Input**: `Test_reports_stuffs/tipic testing summary.xlsx`
- **Output**: 6 failure categories with 22 distinct issues
- **Status**: ✓ DONE
- **Files**: [refinement_tasks.md](refinement_tasks.md), [changelog.md](changelog.md)

### Step 1.2: Map Issues to Skill Files
- **Step 1.2a**: IDU Installation → `l1_qa_workflow_v2/SKILL.md` (Step 4) + `visual_qa_before_after/SKILL.md` (Steps 2-3)
- **Step 1.2b**: Site Environment → `l1_qa_workflow_v2/SKILL.md` (Step 5)
- **Step 1.2c**: IDU Power → `l1_qa_workflow_v2/SKILL.md` (Step 4) + `visual_qa_before_after/SKILL.md` (Step 3)
- **Step 1.2d**: IDU Grounding → `l1_qa_workflow_v2/SKILL.md` (Step 4) + `visual_qa_before_after/SKILL.md` (Step 3)
- **Step 1.2e**: FE/IF Cable → `l1_qa_workflow_v2/SKILL.md` (Step 4) + `visual_qa_before_after/SKILL.md` (Step 3)
- **Step 1.2f**: Topology/RSL → `l1_qa_workflow_v2/SKILL.md` (Step 5)
- **Status**: ✓ DONE

### Step 1.3: Prioritize Tasks
- **Critical (implement first)**: TASK-001 (IDU Installation), TASK-004 (IDU Grounding)
  - Reason: Rejection criteria (legitimacy, grounding presence)
- **High (implement after Critical)**: TASK-002, TASK-003, TASK-005, TASK-006
  - Reason: Quality checks that catch common defects
- **Status**: ✓ DONE

---

## Phase 2: Implementation — CRITICAL TASKS

### Step 2.1: TASK-001 — IDU Installation Legitimacy & Label Clarity

**File 1**: `visual_qa_before_after/SKILL.md`

**Action 2.1a**: Strengthen Step 2 (Before/After Legitimacy Check)
- **Current**: Generic before/after comparison
- **Required change**: Add explicit fake detection logic
- **New text to add**:
  ```
  CRITICAL: Detect staged/fake Before photos:
  - New equipment in Before = immediate REJECT
  - Signs: pristine condition, new packaging visible, new components,
    protective covers, angle/lighting suggesting setup
  - If detected, mark legitimacy as FAIL with reason "Fake Before photo"
    and severity Critical
  ```

**Action 2.1b**: Add label clarity requirement to Step 3 (After photo analysis)
- **Current**: Generic label check
- **Required change**: Add readability and site-ID match validation
- **New checks to add**:
  ```
  Label Clarity Check:
  - requirement: "All visible cable ends must be labeled with yellow tags
    identifying NE and FE site IDs clearly and unambiguously"
  - Add validation: label text must be readable at photo zoom (OCR/human readable)
  - Add validation: site IDs in label must match header site pair
  - If label is partially obscured or pixelated: Mark as N_A (not verifiable)
  - If label is visible but unreadable: Mark as FAIL, Major severity
  - If label identifies wrong site: Mark as FAIL, Critical severity
  ```

**File 2**: `l1_qa_workflow_v2/SKILL.md`

**Action 2.1c**: Update Step 4 IDU Installation QA checks table
- **Current section**: "IDU Installation (2.1/2.2, 3.2/3.3):"
- **Required change**: Add legitimacy check and clarify label requirement
- **New text**:
  ```
  - Before shows old/existing equipment ONLY (new in Before = fake = Critical REJECT)
  - After: IDU on floating nuts, 4 screws, 1U ventilation gap
  - All visible cable ends labeled with **readable, unambiguous** yellow tags
  - Labels must identify **NE and FE sites clearly**, matching header site pair
  - **Legitimacy rule**: If Before photo shows new equipment, pristine condition,
    or signs of staging → immediate REJECT as fake
  ```

**Commit message**: `[TASK-001] Strengthen IDU Installation legitimacy check and label clarity validation`

**Status**: pending implementation

---

### Step 2.2: TASK-004 — IDU Grounding Mandatory Requirements

**File 1**: `visual_qa_before_after/SKILL.md`

**Action 2.2a**: Update Step 3 annotation schema for grounding checks
- **Current**: Generic quality checks
- **Required change**: Add mandatory grounding components with specific visibility rules
- **New checks**:
  ```
  Grounding Cable Presence:
  - requirement: "Yellow/green grounding cable must be physically visible in photo"
  - If cable not visible: Mark as FAIL, Critical severity
  - If visible but damaged/cut: Mark as FAIL, Critical severity
  
  Heat Shrink on Terminations:
  - requirement: "Heat shrink must be visible on all cable terminations"
  - If no heat shrink visible: Mark as FAIL, Major severity
  - If heat shrink only partial: Mark as FAIL, Major severity
  
  Cable Lug Quality:
  - requirement: "Cable lugs must be properly crimped with clean solder flow"
  - If loose strands visible: Mark as FAIL, Major severity
  - If poor workmanship (misaligned, cold solder): Mark as FAIL, Major severity
  
  Grounding Labels:
  - requirement: "Grounding cable termination points must be labeled"
  - If label missing: Mark as FAIL, Major severity
  ```

**File 2**: `l1_qa_workflow_v2/SKILL.md`

**Action 2.2b**: Update Step 4 IDU Grounding QA checks
- **Current section**: "IDU Grounding (2.5/2.6, 3.6/3.7):"
- **Required change**: Make all three components mandatory with clear visibility rules
- **New text**:
  ```
  - Yellow-green grounding cable **MUST be present and visible** in photo
    (if not visible: Critical REJECT)
  - Cable lugs at visible terminations with **clean workmanship** (solder flow,
    no loose strands)
  - Heat shrink **covering entire termination point** (mandatory, not optional)
  - Labels visible identifying termination points (IDU end, busbar end, or midpoint)
  
  MANDATORY: If grounding cable is not physically visible in After photos, REJECT
  the entire section as Critical failure.
  ```

**Action 2.2c**: Update Step 5 Rejection Criteria
- **Current**: "No grounding cable installed..."
- **Required change**: Make more explicit and actionable
- **New text**:
  ```
  - Missing grounding cable (not visible in After photos) — IMMEDIATE REJECT
  - Missing heat shrink on grounding terminations — Major severity
  - Poor cable lug workmanship (loose strands, cold solder) — Major severity
  ```

**Commit message**: `[TASK-004] Enforce mandatory grounding cable, heat shrink, and lug quality checks`

**Status**: pending implementation

---

## Phase 3: Implementation — HIGH PRIORITY TASKS

### Step 3.1: TASK-002 — Site Environment Interior Photos & Watermark

**File**: `l1_qa_workflow_v2/SKILL.md`

**Action 3.1a**: Update Step 5 Site Environment (2.21/3.22) section
- **Current**: Generic requirement for 4 cabin photos
- **Required change**: Add interior-only validation, watermark check, format validation
- **New text**:
  ```
  - Site Environment (2.21/3.22): for cabin sites, provide **4 inside photos from corner views**
    - Photos must be **INTERIOR** (reject exterior/outdoor scenes)
    - Photos must be **GPS-enabled** and use **ONLY IEPMS watermark**
    - No watermark or non-IEPMS watermark = REJECT
    - Image format: PNG or JPG, minimum 1200x800 px resolution
    - Each photo must clearly show cabin interior (walls, equipment, layout)
    - If exterior photos detected (outdoor sky, building exterior, landscape): FAIL, Critical severity
  ```

**Commit message**: `[TASK-002] Add interior photo validation, watermark check, and format requirements for Site Environment`

**Status**: pending implementation

---

### Step 3.2: TASK-003 — IDU Power Breaker & Cable Labeling

**File 1**: `l1_qa_workflow_v2/SKILL.md`

**Action 3.2a**: Update Step 4 IDU Power (2.3/2.4, 3.4/3.5) checks
- **Current**: Generic power installation checks
- **Required change**: Tighten breaker labeling and MAIN/STBY visibility
- **New text**:
  ```
  - Tubular terminals at breaker, cable lugs at busbar
  - **Breakers MUST have yellow ID tags, readable and visible**
    (missing or unreadable: Major FAIL)
  - **Power cables MUST be labeled with MAIN/STBY or equivalent**
    (missing: Major FAIL; unreadable: Major FAIL)
  - Power labels must be positioned near breaker connection or on cable itself
  - All visible connections must show proper terminal seating
  ```

**File 2**: `visual_qa_before_after/SKILL.md`

**Action 3.2b**: Add breaker & power labeling checks to annotation schema
- **New checks**:
  ```
  Breaker Yellow ID Tags:
  - requirement: "Breaker must have yellow ID tag, readable and visible"
  - If tag missing: Mark as FAIL, Major severity, bbox the breaker
  - If tag present but unreadable: Mark as FAIL, Major severity
  
  Power Cable Labels (MAIN/STBY):
  - requirement: "Power cables must be labeled with MAIN/STBY or equivalent"
  - If label missing: Mark as FAIL, Major severity
  - If label unreadable: Mark as FAIL, Major severity
  - Annotation: highlight cable with label area boxed
  ```

**Commit message**: `[TASK-003] Require breaker yellow ID tags and MAIN/STBY power cable labels with readability validation`

**Status**: pending implementation

---

### Step 3.3: TASK-005 — FE/IF Cable Clamps, Waterproofing & Labels

**File 1**: `l1_qa_workflow_v2/SKILL.md`

**Action 3.3a**: Update Step 4 FE Cable (2.9/2.10, 3.10/3.11) checks
- **Current**: Generic cable bundling
- **Required change**: Reject white zip ties, require black clamps, label all ends
- **New text**:
  ```
  - Yellow ID tags on **EVERY visible cable end** (no exceptions)
    - Missing tag on any visible end: Major FAIL
  - **Black cable clamps ONLY** (white zip ties = not acceptable = Major FAIL)
  - Cables securely seated in ports (no loose/pulled connectors)
  - All visible cable ends properly labeled with NE/FE site IDs
  ```

**Action 3.3b**: Update Step 4 IF Cable (2.7/2.8, 3.8/3.9) checks
- **Current**: Generic 1+3+3 tape requirement
- **Required change**: Make waterproofing verification more realistic; allow professional sealing alternatives such as yellow heat shrink sleeves
- **New text**:
  ```
  - **200mm bending radius maintained** (visible in photo or documented)
  - **Connector waterproofing must be fully sealed and professionally applied**; yellow heat shrink sleeves or equivalent encapsulation are acceptable when the connector appears properly sealed.
    (do not fail solely because distinct tape layers are not visible; if the connector is hidden, mark N_A)
  - **Grounding kit 0.5-1m from entry points** (must be localized and visible)
  - Labels visible where IDU or ODU end is shown
  - If waterproofing appears incomplete or connector seal is absent: Major FAIL
  ```

**File 2**: `visual_qa_before_after/SKILL.md`

**Action 3.3c**: Add cable clamp, waterproofing, and labeling checks
- **New checks**:
  ```
  Cable Clamp Type (FE Cable):
  - requirement: "Black cable clamps ONLY; white zip ties not acceptable"
  - If white zip ties detected: Mark as FAIL, Major severity, bbox the area
  
  FE Cable End Labels:
  - requirement: "Yellow ID tags on every visible cable end"
  - If visible cable end has no label: Mark as FAIL, Major severity
  
  IF Cable Waterproofing:
  - requirement: "Connector waterproofing must be fully sealed and professionally applied; equivalent protection such as yellow heat shrink sleeves is acceptable when the seal is complete"
  - If the connector appears unsealed or incomplete: Mark as FAIL, Major severity
  
  Grounding Kit Proximity (IF Cable):
  - requirement: "Grounding kit 0.5-1m from entry points"
  - If kit not visible or distance unclear: Mark as N_A
  - If kit present but too far: Mark as FAIL, Minor severity
  ```

**Commit message**: `[TASK-005] Enforce black cable clamps, complete waterproofing tape, and mandatory FE cable labeling`

**Status**: pending implementation

---

### Step 3.4: TASK-006 — NMS Screenshot Quality & Data Visibility

**File**: `l1_qa_workflow_v2/SKILL.md`

**Action 3.4a**: Update Step 5 Topology (2.14/3.15) checks
- **Current**: Generic attach-topology requirement
- **Required change**: Add clarity, completeness, and readability checks
- **New text**:
  ```
  - Topology (2.14/3.15): attach topology screenshot showing:
    - **Full link path with clear site endpoints**
    - **Frequency/band labels must be visible**
    - **Diagram must be readable at normal zoom** (reject if blurry/pixelated)
    - Laptop screenshots acceptable; phone photos of screen may be rejected if pixelated
    - For 3.15: topology must be obtained from technical team
    - If screenshot is incomplete or unreadable: Major FAIL
  ```

**Action 3.4b**: Update Step 5 RSL (2.18/3.19) checks
- **Current**: Generic live link data requirement
- **Required change**: Add value visibility and clarity checks
- **New text**:
  ```
  - RSL / Microwave Link Configuration (2.18/3.19):
    - Screenshot must show **live microwave link data with RSL clearly visible**
    - Must display **Tx/Rx frequency, Tx power, link state** (visible in single screenshot or clear multi-shot)
    - **Bandwidth/modulation visible where available**
    - Screenshot must be **clear and readable** (reject if blurry, pixelated, or values obscured)
    - If RSL values not visible: Major FAIL
    - If link data incomplete: Major FAIL
  ```

**Action 3.4c**: Add general screenshot quality requirement to Step 5 preamble
- **Add before section-specific checks**:
  ```
  **Screenshot Quality Rules (applies to all NMS sections):**
  - All screenshots must be **clear, readable, and not pixelated** at normal zoom
  - Minimum resolution: 1024x768; larger screens preferred for visibility
  - All text and values in screenshots must be readable (OCR-compatible clarity)
  - If screenshot is blurry or pixelated → REJECT section as Major FAIL
  - Laptop screenshots (laptop screen capture) are acceptable if clear
  - Phone photos of screen are acceptable if clear and well-lit, not pixelated
  ```

**Commit message**: `[TASK-006] Add screenshot quality checks, data visibility requirements, and readability validation for NMS sections`

**Status**: pending implementation

---

## Phase 4: Validation & Recording

### Step 4.1: Validate Each Implementation
- **For each skill file change**:
  - ✓ Verify syntax (no typos, proper Markdown formatting)
  - ✓ Check that changes align with original skill purpose
  - ✓ Ensure no conflicting requirements between sections
  - ✓ Confirm severity levels (Critical vs. Major vs. Minor) are appropriate

### Step 4.2: Update Task Tracking
- **For each completed task**:
  - Update `tasks/refinement_tasks.md`: mark status as "completed"
  - Update `tasks/changelog.md`: add implementation date and commit hash
  - Example entry:
    ```
    | TASK-001 | IDU Installation | ✓ | ✓ |  | 2 files | commit abc123 | 2026-05-20 |
    ```

### Step 4.3: Commit with Clear References
- **Commit format**: 
  ```
  [TASK-XXX] <brief description>
  
  - Updated <file1>: <specific change>
  - Updated <file2>: <specific change>
  
  Related: refinement_tasks.md, changelog.md
  ```

### Step 4.4: Push to GitHub
```bash
git add l1_qa_workflow_v2/SKILL.md visual_qa_before_after/SKILL.md tasks/
git commit -m "[TASK-001] Strengthen IDU Installation legitimacy check and label clarity validation"
git push origin main
```

---

## Phase 5: Summary & Final Verification

### Step 5.1: Confirm All Changes
- **Checklist**:
  - [ ] TASK-001 implemented in both files
  - [ ] TASK-004 implemented in both files
  - [ ] TASK-002 through TASK-006 implemented in respective files
  - [ ] All changes committed with task references
  - [ ] All commits pushed to GitHub
  - [ ] `tasks/refinement_tasks.md` updated with completion status
  - [ ] `tasks/changelog.md` updated with commit hashes and dates

### Step 5.2: Post-Implementation Review
- **Skills should now catch**:
  - Fake/staged Before photos (TASK-001)
  - Unclear or missing site ID labels (TASK-001)
  - Missing grounding cables (TASK-004)
  - Poor heat shrink and lug quality (TASK-004)
  - Exterior photos in Site Environment section (TASK-002)
  - Missing watermarks (TASK-002)
  - Unlabeled breakers and power cables (TASK-003)
  - White zip ties on FE cables (TASK-005)
  - Incomplete waterproofing on IF cables (TASK-005)
  - Blurry/pixelated NMS screenshots (TASK-006)
  - Missing data visibility in RSL/Topology (TASK-006)

---

## Implementation Timeline

| Phase | Steps | Status | Est. Duration |
|-------|-------|--------|---|
| 1 | Analysis & Planning (1.1–1.3) | ✓ DONE | 1h |
| 2 | Critical Tasks (2.1–2.2) | pending | 1–2h |
| 3 | High Priority Tasks (3.1–3.4) | pending | 2–3h |
| 4 | Validation & Recording (4.1–4.4) | pending | 30min |
| 5 | Final Review (5.1–5.2) | pending | 30min |
| **TOTAL** | | | **5–7h** |

---

## Next Action
👉 Ready to implement Phase 2 (TASK-001 & TASK-004)?
- Answer: Yes → Start with Step 2.1a, 2.1b, 2.1c, then move to Step 2.2a, 2.2b, 2.2c
- Answer: Review first → Share feedback on specific steps before implementation

