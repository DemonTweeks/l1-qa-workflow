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

For each pair with images, spawn a sub‑agent with `skill="visual_qa_before_after"`.

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

1. **Overall status**: `PASS` or `REJECT`
2. **Header validation**: `PASS` or `FAIL` with a brief explanation of any issues.

3. **Section compliance tables**: For each relevant section (from Step 2), do:
   - Read the section's checklist file (use `CHECKLIST_INDEX.txt` to map section to filename). The file contains subsections (e.g., "To Do's", "To Don'ts", "Image Extraction Checklist (After photos)"). In reading order, extract each bullet item (`- [ ] ...`) and record:
        * `category`: the exact subsection heading under which the bullet appears
        * `requirement`: the bullet text (strip `- [ ] `)
   - Collect the sub-agent findings for this section (from the aggregated `final_answer` results).
   - For each extracted checklist item (in file order):
        * Find a matching finding where `finding.requirement` exactly equals the `requirement`.
        * If found: `status` = finding.status (`PASS`/`FAIL`), `severity` = finding.severity (for FAIL), `description` = finding.description, `annotated` = finding.annotated_image, `source` = finding.source_image.
        * If not found: `status` = `N/A`, `evidence` = "Not verified"
   - Build a markdown table for the section with heading `### <Section name> (<Pair>) Compliance Table` (if pair known) or just `### <Section name> Compliance Table`.

     Table columns:
     | Category | Requirement (To Do / To Don't) | Status | Evidence / Observation |

     For each row:
     - Category: the `category` from the checklist (e.g., "To Do's")
     - Requirement: the full requirement text
     - Status: show ✅ for PASS, ❌ for FAIL, ⚠️ for N/A (or blank if preferred)
     - Evidence/Observation:
         • For PASS: "Compliant" (or the finding description if more informative)
         • For FAIL: include the description, and on new lines `*Annotated:* <file>` and `*Source:* <file>` when present
         • For N/A: "Not verifiable in provided images"

   Maintain the order of checklist items as they appear in the file.

4. **Missing sections**: List any expected sections that were not found in the PDF report.
5. **Severity summary**: Count of FAIL findings by severity across all sections, e.g., "Critical: 0, Major: 2, Minor: 1".
6. **Rejection reasons** (only if Overall status is `REJECT`): For each section that has FAIL findings, list the `requirement` text of each FAIL finding, one per line, prefixed by the section name. Do not include descriptions or image references.

### Notes
- Always include `*Annotated:*` and `*Source:*` for FAIL findings.
- Keep the report structured; avoid narrative paragraphs.

### Tool restrictions
- Use only the allowed tools (analyze_image, annotate_regions, list_workspace_files, build_composite, parse_pdf, final_answer, spawn_agent).
- Sub-agent sessions are read-only; do not attempt to modify them.

## File locations (within scope)
- Checklist files: `skills/refine/version2_l1_qa_workflow_v2/*.md`
- CHECKLIST_INDEX: `skills/refine/version2_l1_qa_workflow_v2/CHECKLIST_INDEX.txt`
- Sub-agent skill: `skills/refine/version2_visual_qa_before_after/SKILL.md`

Keep all operations within these directories.
