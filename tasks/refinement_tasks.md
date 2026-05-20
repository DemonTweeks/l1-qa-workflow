# Refinement Tasks

**Source**: `Test_reports_stuffs/tipic testing summary.xlsx`  
**Date**: 2026-05-20  
**Status**: Parsed — 6 refinement items extracted

**Note**: Approved sample report section mapping confirmed. Site Environment sections are 2.29 and 3.30.
---

## Task 001: IDU Installation – Label Clarity & Legitimacy Checks

**Category**: IDU Installation (sections 2.1/2.2, 3.2/3.3)

**Issue Summary**:
- Missing or unclear NE/FE site identification labels
- Labels do not clearly identify the site
- Photos appear staged or show new equipment in "before" images (fake/critical reject)

**Affected Files**:
- `l1_qa_workflow_v2/SKILL.md` (Step 4: IDU Installation QA checks)
- `visual_qa_before_after/SKILL.md` (Step 2: Legitimacy check, Step 3: After photo analysis)

**Recommended Refinements**:
1. Strengthen "before photo legitimacy" check — explicitly instruct to detect and **reject immediately** if new equipment is visible.
2. Tighten label clarity requirement: labels must be **readable at photo resolution**, **unambiguous about site ID**, and **match the header site pair**.
3. Add visual detection guidance: look for packaging, pristine condition, new components, or angle/lighting suggesting setup scenario.

**Priority**: Critical — legitimacy is a rejection criterion

**Status**: pending implementation

---

## Task 002: Site Environment – Interior Photos & Watermark Validation

**Category**: Site Environment (sections 2.29/3.30)

**Issue Summary**:
- Exterior photos used instead of required interior cabin photos
- Missing IEPMS watermark
- Wrong image format

**Affected Files**:
- `l1_qa_workflow_v2/SKILL.md` (Step 5: NMS Screenshots, Site Environment section)
- `visual_qa_before_after/SKILL.md` (if used for environment QA)

**Recommended Refinements**:
1. Add explicit check: **reject if exterior photos detected** (e.g., outdoor sky, building exterior, landscape).
2. Require **4 interior corner-view photos** from cabin — clarify in instructions.
3. Mandate **IEPMS watermark presence** — provide visual examples of valid vs. invalid watermarks.
4. Validate **image format** (PNG/JPG requirements, DPI, resolution thresholds).

**Priority**: High — mandatory cabin site requirement

**Status**: pending implementation

---

## Task 003: IDU Power – Labeling & Cable Workmanship

**Category**: IDU Power (sections 2.3/2.4, 3.4/3.5)

**Issue Summary**:
- Breakers not labeled properly
- Missing yellow ID tags
- Power labels (MAIN/STBY) not visible

**Affected Files**:
- `l1_qa_workflow_v2/SKILL.md` (Step 4: IDU Power QA checks)
- `visual_qa_before_after/SKILL.md` (Step 3: After photo analysis)

**Recommended Refinements**:
1. Add dedicated check: **breaker yellow ID tag presence** — must be visible and readable.
2. Add check: **MAIN/STBY labels on power cables** — must be clearly visible at junction points.
3. Clarify rule: **tubular terminals at breaker, cable lug at busbar** are acceptable, but **label placement matters** (near breaker ID, on cable, at connection point).
4. Provide annotation guidance: highlight unlabeled breakers and cables as Major severity.

**Priority**: High — labeling is mandatory

**Status**: pending implementation

---

## Task 004: IDU Grounding – Heat Shrink, Cable Lug Quality & Completeness

**Category**: IDU Grounding (sections 2.5/2.6, 3.6/3.7)

**Issue Summary**:
- No heat shrink visible
- Poor cable lug workmanship
- Missing grounding labels
- No proper grounding cable shown in some cases

**Affected Files**:
- `l1_qa_workflow_v2/SKILL.md` (Step 4: IDU Grounding QA checks)
- `visual_qa_before_after/SKILL.md` (Step 3: After photo analysis)

**Recommended Refinements**:
1. Strengthen requirement: **heat shrink is mandatory** — must be visible and cover termination point. Lack of heat shrink = **Major severity FAIL**.
2. Add check: **cable lug quality** — solder flow, no loose strands, proper crimp visible. Poor workmanship = **Major severity**.
3. Clarify check: **yellow/green grounding cable must be physically present** — not just inferred. If not visible in After photos = **FAIL**.
4. Add check: **grounding labels must identify termination points** (IDU end, busbar end, or midpoint).

**Priority**: Critical — grounding is a rejection criterion if missing entirely

**Status**: pending implementation

---

## Task 005: FE Cable / IF Cable – Waterproofing, Clamps & Labeling

**Category**: FE Cable (2.9/2.10, 3.10/3.11) and IF Cable (2.7/2.8, 3.8/3.9)

**Issue Summary**:
- White zip ties used instead of proper black cable clamps
- Missing labels on cable ends
- Incomplete waterproofing (tape, grounding kit)
- Physical cable-end labels missing or unreadable

**Affected Files**:
- `l1_qa_workflow_v2/SKILL.md` (Step 4: FE Cable & IF Cable QA checks)
- `visual_qa_before_after/SKILL.md` (Step 3: After photo analysis)

**Recommended Refinements**:
1. Clarify FE Cable requirement: **black cable clamps mandatory** — explicitly reject white zip ties (not professional, not weatherproof).
2. Strengthen IF Cable waterproofing: **1+3+3 tape layers on connectors** must be visible in photo. Incomplete tape = **Major FAIL**.
3. Add check: **grounding kit 0.5-1m from entry point** — must be localized and visible in photo.
4. Tighten label requirement: **yellow ID tags on every visible cable end** — no exceptions. If a cable end is visible but unlabeled = **Major FAIL**.

**Priority**: High — cable quality directly affects link reliability

**Status**: pending implementation

---

## Task 006: Topology / RSL / Link Configuration – Screenshot Quality & Visibility

**Category**: NMS Screenshots (sections 2.14/3.15, 2.18/3.19)

**Issue Summary**:
- Screenshots too blurry or pixelated
- RSL values not visible
- Live link data missing
- Topology screenshots incomplete or unclear

**Affected Files**:
- `l1_qa_workflow_v2/SKILL.md` (Step 5: NMS Screenshots – Topology, RSL sections)
- `visual_qa_before_after/SKILL.md` (potentially for screenshot validation)

**Recommended Refinements**:
1. Add explicit check: **screenshot resolution and clarity** — reject if blurry, pixelated, or unreadable at normal zoom.
2. Strengthen Topology requirement: **full topology diagram must be visible**, with **clear link path**, **site endpoints**, **frequency/band labels visible**.
3. Strengthen RSL check: **RSL values must be clearly visible** on screen — no partial data, no obscured fields.
4. Link Configuration check: **live link status, Tx/Rx frequency, modulation, bandwidth** must all be visible in a single screenshot or multiple clear shots.
5. Clarify: **laptop screenshots are acceptable** but must be clear; phone photos of screen may be rejected if pixelated.

**Priority**: High — NMS data is mandatory evidence

**Status**: pending implementation

---

## Summary Table

| Task ID | Category | Issue Count | Priority | Files Affected | Status |
|---------|----------|-------------|----------|-----------------|--------|
| 001 | IDU Installation | 3 | Critical | l1_qa_workflow, visual_qa | pending |
| 002 | Site Environment | 3 | High | l1_qa_workflow | pending |
| 003 | IDU Power | 3 | High | l1_qa_workflow, visual_qa | pending |
| 004 | IDU Grounding | 4 | Critical | l1_qa_workflow, visual_qa | pending |
| 005 | FE/IF Cable | 4 | High | l1_qa_workflow, visual_qa | pending |
| 006 | Topology/RSL | 4 | High | l1_qa_workflow | pending |

**Next Steps**:
1. Review each task and proposed refinements.
2. Update `l1_qa_workflow_v2/SKILL.md` with tightened checks and examples.
3. Update `visual_qa_before_after/SKILL.md` with annotation and severity guidance.
4. Commit with reference to task IDs (e.g., `[TASK-001] Strengthen IDU Installation legitimacy check`).
5. Move task status to completed.
