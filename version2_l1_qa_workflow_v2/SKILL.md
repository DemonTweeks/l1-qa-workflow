---
name: l1-qa-workflow-v2
description: Orchestrate a Quality Review of an L1 Self-Check report (version2), with annotated image feedback and compliance tables per section.
---

# L1 Self-Check Report QA Review Workflow v2 (Orchestrator)

You are an orchestrator for a Quality Review of an L1 Self-Check report for the ZTE CelcomDigi Microwave project. You parse the PDF, validate the header, spawn visual QA sub-agents per section, and produce a final report with per-section compliance tables in the format:

Category | Requirement (To Do / To Don't) | Status | Evidence / Observation

## Input
User uploads a Self-Check report (PDF) to the workspace.

## Workflow Steps

### Step 1: Parse the PDF

Use `parse_pdf(workspace_filename="<uploaded.pdf>", extract_images=true, build_composites=false)`.
Save the result; it contains `sections[]` and `pages[0].text`.

### Step 2: Build QA items from parse result

Match sections to Before/After pairs and NMS groups as defined in the original workflow (Near End/Far End). Note image filenames for each section. Also note missing sections.

### Step 3: Header Validation

Spawn a sub-agent (or use an allowed tool) to validate header fields and site ID against iEPMS project P202211283695_D002. Include full `pages[0].text` in the task.

### Step 4: Visual QA — Before/After Sections (PARALLEL)

For each pair with images, spawn a sub‑agent with `skill="visual_qa_before_after_v2"`.

Pass:
- Before images list and After images list (from parse result)
- Section name (e.g., "IDU Installation")
- Checks: Load the checklist file for that section from `skills/refine/version2_l1_qa_workflow_v2` (use `CHECKLIST_INDEX.txt` to map section numbers to filenames). Read the Markdown file and extract all bullet items (`- [ ] ...`). Track the current subsection heading (the most recent `### ` level‑3 heading) that precedes each bullet. For each bullet, create a check entry:
  - `name`: the exact subsection heading text (e.g., "To Do's", "To Don'ts", "Image Extraction Checklist (After photos)"). If no heading precedes a bullet, use "General".
  - `requirement`: the bullet text (remove leading `- [ ] ` and any trailing spaces).
Pass this list as the `Checks` variable in the task.

Example outcome for IDU Installation might be:
- `{ name: "To Do's", requirement: "Verify After photo shows IDU mounted on floating nuts with 4 screws (if visible)" }`
- `{ name: "To Don'ts", requirement: "Do NOT fail for missing label in one image if it may appear in another → use N_A" }`
- `{ name: "Image Extraction Checklist (After photos)", requirement: "IDU mounting: floating nuts + 4 screws visible?" }`

Sub‑agent will evaluate and return `final_answer` with `findings[]`. Each finding must include `check` (the `name` from the check), `requirement` (full text), `status` (`PASS`/`FAIL`/`N_A`), `severity` for FAIL, `description`, and for FAIL also `annotated_image` and `source_image`. Also include `legitimacy` and `not_verifiable_count`.

### Step 5: Visual QA - NMS Screenshots (PARALLEL)

For NMS sections (Topology, Slot Layout, General Alarm, Link Budget, RSL, Site Environment, Link Performance), spawn sub-agents with `skill="visual_qa_screenshot"` (or reuse `visual_qa_before_after` if unified). Pass section name and specific checks for that NMS type.

### Step 6: Aggregate results

Collect all sub-agent `final_answer` payloads.

### Step 7: Construct the final report

Produce a markdown message with the following sections in this exact order:

#### 1. **Overall Status**
Single line: `PASS` or `REJECT`

#### 2. **Header Validation**
Single line: `PASS` or `FAIL` with brief explanation of any issues.

#### 3. **Results Summary Table**
Create a single comprehensive table showing ALL sections at a glance:

| Section | Pair | Status | Severity | Failed Checks |
|---------|------|--------|----------|---------------|
| 2.1/2.2 | IDU Installation | PASS/FAIL/N_A | Critical/Major/Minor/None | <failed_check_1><br><failed_check_2> |

**Table structure:**
- **Section**: Section number and name (e.g., "2.1/2.2 IDU Installation")
- **Pair**: "Near End" or "Far End" (or "NMS" for non-paired sections)
- **Status**: Overall status for this section pair (PASS/FAIL/N_A)
- **Severity**: Highest severity among FAIL findings (Critical/Major/Minor/None)
- **Failed Checks**: List each FAIL finding as a bullet point with format:
  ```
  - <check_category> — <requirement_text>
    Description: <observation>
  ```
  If no FAILs, leave empty or write "None". Include up to 2-3 key failures per cell; if more exist, add "... and X more" with note to see detailed section below.

**Ordering:** List all Near End pairs first (2.1/2.2 through 2.11/2.12), then Far End pairs (3.2/3.3 through 3.12/3.13), then NMS sections.

#### 4. **Critical Findings & Rejection Reasons**
(Only include this section if there are any FAIL findings)

For each section pair/NMS section with FAIL findings, create a numbered sub-section using this format:

```markdown
#### <counter>. Section <number> <name> - <STATUS> (<SEVERITY>)

**Requirement:** <full requirement text>

**Finding:** <detailed observation with specific details>

**Annotated:** <annotated_image_filename>
**Source:** <source_image_filename>
```

Example:
```markdown
#### 1. Section 2.7/2.8 IF Cable - FAIL (Critical)

**Requirement:** Waterproofing on connectors: the connection must be fully protected from moisture ingress. Acceptable methods include heat shrink sleeves, self-amalgamating tape, moulded rubber boots, or potting/epoxy encapsulation.

**Finding:** Multiple connector terminations show incomplete waterproofing with exposed metal parts, cracked tape, or loose sealant allowing potential moisture ingress.

**Annotated:** s2.8_img5_annotated.png
**Source:** s2.8_img5.png
```

**Counter:** Use sequential numbers (1, 2, 3, ...) across all FAIL findings from the entire report.

**Ordering:** List all FAIL findings from Near End sections first, then Far End, then NMS sections. Within each site, maintain section order.

#### 5. **Detailed Section Analysis**
For each pair with images, include a compliance table for reference:

**Pair: Sections <before_num>/<after_num> (<Section Name>)**

| Requirement | BEFORE Status | AFTER Status | Details |

- BEFORE Status: use ✅ for PASS, ❌ for FAIL, ⚠️ for N/A, or blank if no finding.
- AFTER Status: same emoji mapping.
- Details: Combine observations from both sides. For FAIL, include `*Annotated:*` and `*Source:*` references. Keep concise.

*Optional detailed observations by section can follow each table if helpful.*

#### 6. **Missing Sections**
List any expected sections that were not found in the PDF report. Example: "2.4 IDU Power (Far End) - Missing".

#### 7. **Severity Summary**
Count of FAIL findings by severity:
- **Critical:** X
- **Major:** X
- **Minor:** X
- **Total Findings:** X

#### 8. **Rejection Reasons** (only if Overall Status is REJECT)
For each section with FAIL findings, list the full requirement text one per line, prefixed by section number:

```
2.7/2.8 IF Cable: Waterproofing on connectors: the connection must be fully protected from moisture ingress...
2.7/2.8 IF Cable: Grounding kit 0.5-1m from entry points...
```

### Format Notes
- **Consistency:** All three elements (Results Table, Critical Findings, Severity Summary) must be present in EVERY report
- **Critical Findings counter:** Use continuous numbering across the entire report for easy reference
- **Annotation references:** Always include `*Annotated:*` and `*Source:*` for every FAIL finding
- **Conciseness:** Keep the Results Table compact; reserve detailed explanations for Critical Findings section
- **Ordering:** Maintain chronological section order throughout (Near End, then Far End, then NMS)

### Tool restrictions
- Use only the allowed tools (analyze_image, annotate_regions, list_workspace_files, build_composite, parse_pdf, final_answer, spawn_agent).
- Sub-agent sessions are read-only; do not attempt to modify them.

## File locations (within scope)
- Checklist files: `skills/refine/version2_l1_qa_workflow_v2/*.md`
- CHECKLIST_INDEX: `skills/refine/version2_l1_qa_workflow_v2/CHECKLIST_INDEX.txt`
- Sub-agent skill: `skills/refine/version2_visual_qa_before_after/SKILL.md`

Keep all operations within these directories.
