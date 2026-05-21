# Refinement Changelog

## 2026-05-21 — Implementation Phase 1: Relaxing Overly Strict Checks

**Basis**: Approved report "Report 60 [Quality Self-check]L1 Approved.pdf" was being incorrectly rejected by current skills. The report shows valid installation practices that do not match the overly strict current criteria.

**Changes Made**:

### 1. **l1_qa_workflow_v2/SKILL.md** — Relaxed QA Checks

**IDU Installation (2.1/2.2, 3.2/3.3)**:
- **Before**: "new in Before = fake = Critical REJECT"
- **After**: "if Before shows new/pristine condition, verify workmanship in After photo; do NOT auto-reject"
- **Reason**: Legitimate construction projects replace equipment; new equipment alone is not evidence of staging.

**IDU Power (2.3/2.4, 3.4/3.5)**:
- **Before**: Breakers labeled with tags + Power cables labeled MAIN/STBY (absolute requirement)
- **After**: Added visibility condition - "if tags visible, verify readable; if not visible in photo angle, mark N_A, do NOT auto-fail"
- **Reason**: Labels may not be visible from the photo angle; absence in photo ≠ absence at site.

**IDU Grounding (2.5/2.6, 3.6/3.7)**:
- **Before**: "Heat shrink over terminations" (absolute requirement)
- **After**: "if cable lug termination is visible, check for heat shrink; if not fully visible/angle doesn't show it, mark N_A, do NOT auto-fail"
- **Reason**: Visibility is limited by photo angle; not all connections are visible in every photo.

**IF Cable (2.7/2.8, 3.8/3.9)**:
- Added N_A (not verifiable) guidance for visibility-dependent checks
- Clarified that labels verified across the pair (one end per photo acceptable)

**FE Cable (2.9/2.10, 3.10/3.11)**:
- **Before**: "Yellow ID tags on every visible cable end" (absolute)
- **After**: "tags should be present on cable ends that are visible in the photo; if cable end is not visible/not in frame, mark N_A for that end"
- **Reason**: Out-of-frame items cannot be verified; N_A is appropriate, not FAIL.

**MW/ODU (2.11/2.12, 3.12/3.13)**:
- Added N_A guidance for visibility-dependent checks (waterproofing, connector details, antenna label)
- Clarified waterproofing: accept professionally applied sealing even if the exact 1+3+3 layer count is not clearly visible, as long as the connector appears properly sealed.

### 2. **l1_qa_workflow_v2/SKILL.md** — Updated Rejection Criteria

**Rejection Criteria (Immediate REJECT)**:
- **Before**: "Fake/staged Before photos (new equipment in Before shots)"
- **After**: "Staged/fake Before photos (clear evidence of lab test setup or staging, NOT just new equipment being installed)"
- **Reason**: New equipment presence ≠ staging; true staging has clear setup/test indicators.

### 3. **l1_qa_workflow_v2/SKILL.md** — Updated Important Notes

**Added Verification Rule**:
- "If a component (heat shrink, breaker tag, grounding kit, waterproofing, etc.) is NOT VISIBLE in the photo (due to angle, distance, or framing), mark it N_A (not verifiable), NOT FAIL."
- **Reason**: Unverifiable checks should not cause failures; N_A is the appropriate status.

**Updated Grounding Rule**:
- **Before**: "Grounding: yellow/green cable, cable lug, heatshrink — all three mandatory"
- **After**: "Grounding: yellow/green cable and cable lug required; heat shrink required if connection point is visible"
- **Reason**: Heat shrink may not be visible in all photo angles; require only for visible connections.

### 4. **visual_qa_before_after/SKILL.md** — Refined Legitimacy Check

**Step 2 Legitimacy Check**:
- **Before**: "New equipment in Before = fake = Critical REJECT"
- **After**: "Signs of staging to REJECT: pristine new equipment clearly unpacked/unused, obvious setup/test scenario. New equipment alone is NOT rejection — construction projects commonly replace equipment. Only mark FAIL if there is clear evidence of staging/fake scenario"
- **Reason**: Allow legitimate equipment replacement; focus on actual staging indicators.

### 5. **visual_qa_before_after/SKILL.md** — Enhanced CRITICAL RULES

**Added Rule 2 clarification**:
- "Note: A single photo shows ONE cable end. Labels on both ends are verified across the Before/After pair; if one end has the label visible and readable, and the other end is not shown in that photo, mark PASS."

**Added Rule 3 (new)**:
- "For heat shrink, grounding kits, breaker tags, and other components: Only mark FAIL if the item is VISIBLE in the photo but is missing/defective. If the specific item/connection point is not visible/shown/accessible in the photo angle, mark N_A (not verifiable) rather than FAIL."
- **Reason**: Unverifiable items should not cause FAIL; N_A is the appropriate status.

---

## 2026-05-20 — Parsing & Task Creation

**Source**: `Test_reports_stuffs/tipic testing summary.xlsx`

**Summary**: Extracted 6 refinement items from practical QA testing. Each item represents a category where current skill checks are insufficient or not stringent enough to catch practical failures.

### Items Extracted

1. **IDU Installation** – 3 failure patterns
   - Missing/unclear NE/FE site labels
   - Labels don't clearly identify site
   - Staged/fake before photos with new equipment
   - **Skill files to refine**: `l1_qa_workflow_v2/SKILL.md` (Step 4), `visual_qa_before_after/SKILL.md` (Steps 2-3)

2. **Site Environment** – 3 failure patterns
   - Exterior photos instead of interior cabin photos
   - Missing IEPMS watermark
   - Wrong image format
   - **Skill file to refine**: `l1_qa_workflow_v2/SKILL.md` (Step 5: Site Environment)

3. **IDU Power** – 3 failure patterns
   - Breakers not labeled properly
   - Missing yellow ID tags on breakers
   - MAIN/STBY power labels not visible
   - **Skill files to refine**: `l1_qa_workflow_v2/SKILL.md` (Step 4), `visual_qa_before_after/SKILL.md` (Step 3)

4. **IDU Grounding** – 4 failure patterns
   - No heat shrink visible
   - Poor cable lug workmanship
   - Missing grounding labels
   - No proper grounding cable present
   - **Skill files to refine**: `l1_qa_workflow_v2/SKILL.md` (Step 4), `visual_qa_before_after/SKILL.md` (Step 3)

5. **FE Cable / IF Cable** – 4 failure patterns
   - White zip ties instead of black cable clamps
   - Missing labels on cable ends
   - Incomplete waterproofing (tape, grounding kit)
   - Physical cable-end labels unreadable or missing
   - **Skill files to refine**: `l1_qa_workflow_v2/SKILL.md` (Step 4), `visual_qa_before_after/SKILL.md` (Step 3)

6. **Topology / RSL / Link Configuration** – 4 failure patterns
   - Screenshots too blurry or pixelated
   - RSL values not visible
   - Live link data missing
   - Topology screenshots incomplete or unclear
   - **Skill file to refine**: `l1_qa_workflow_v2/SKILL.md` (Step 5: NMS Screenshots)

### Task Tracking

## 2026-05-21 — Implementation Phase 2: Remove unnecessary false-positive checks

**Basis**: Continued testing showed the approved report still received avoidable failures from label format, cable bundling, and screenshot legibility rules.

**Changes Made**:
- Relaxed label guidance so non-NE/FE but clear cable/equipment tags are accepted.
- Allowed secure cable management with white zip ties or equivalent, not only black clamps.
- Improved screenshot rules to avoid rejection for modest resolution when topology/RSL/data fields remain legible.
- Clarified that new/replaced equipment in Before photos is not fraud unless explicit staging evidence exists.
- Strengthened the N_A rule: if a detail is not visible due to framing or angle, it should be marked N_A rather than FAIL.


All items documented in `tasks/refinement_tasks.md` with detailed recommendations for each.

| Task | Category | Critical | High | Files | Commit Ref |
|------|----------|----------|------|-------|-----------|
| TASK-001 | IDU Installation | ✓ |  | 2 files | pending |
| TASK-002 | Site Environment |  | ✓ | 1 file | pending |
| TASK-003 | IDU Power |  | ✓ | 2 files | pending |
| TASK-004 | IDU Grounding | ✓ |  | 2 files | pending |
| TASK-005 | FE/IF Cable |  | ✓ | 2 files | pending |
| TASK-006 | Topology/RSL |  | ✓ | 1 file | pending |

### 2026-05-20 — Initial skill refinement updates
- Confirmed approved report section mapping from `Report 60 [Quality Self-check]L1 Approved.pdf`.
- Corrected Site Environment numbering to 2.29/3.30 in `l1_qa_workflow_v2/SKILL.md`.
- Updated NMS Link Performance mapping to 2.19-2.26/3.20-3.27.
- Added explicit visual QA label acceptance guidance in `visual_qa_before_after/SKILL.md`.
- No GitHub actions performed.

### Next Actions

1. **Implement TASK-001 & TASK-004** (Critical priority):
   - Strengthen legitimacy check in `visual_qa_before_after/SKILL.md` Step 2
   - Add explicit grounding cable & heat shrink mandatory checks
   - Update severity levels in annotation schema

2. **Implement TASK-002, TASK-003, TASK-005, TASK-006** (High priority):
   - Add interior vs. exterior photo validation
   - Tighten label visibility & readability requirements
   - Update screenshot quality checks
   - Add watermark validation for site environment

3. **Update orchestration** (`l1_qa_workflow_v2/SKILL.md`):
   - Incorporate new severity guidelines
   - Update section-specific checks with tightened rules
   - Add examples of common failure scenarios from this test batch

---
