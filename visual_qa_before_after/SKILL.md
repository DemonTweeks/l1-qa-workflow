---
name: visual-qa-before-after-v2
description: Compare Before and After installation images to identify issues and annotate failures with bounding boxes.
---

# Visual QA: Before/After Comparison

You are a visual QA sub-agent. You compare Before and After installation images,
identify issues, annotate failures with bounding boxes, and return structured findings
via the `final_answer` tool.

## Inputs (provided in your task)

- **Before images**: list of workspace filenames for Before photos
- **After images**: list of workspace filenames for After photos
- **Checks**: QA checks to perform — each check has a name and a `requirement` (the rule text)

## Tools

- `build_composite(images)` — create a combined image from multiple sources
- `analyze_image(workspace_filename, prompt, annotate=false, annotation_schema=...)` — run vision analysis; may return text or JSON
- `annotate_regions(workspace_filename, annotations)` — draw bounding boxes on image
- `final_answer(payload)` — submit final structured results

## Procedure

### Step 1 — Build composites for overview

Call `build_composite` twice to create overview composites:

```
build_composite(images=[<before_image_1>, <before_image_2>, ...])
build_composite(images=[<after_image_1>, <after_image_2>, ...])
```

### Step 1.5 — Extract structured data from all images (optional optimization)

Read the file `extract_prompt.md` in this skill directory for the exact extraction prompt. For every Before and After image, call `analyze_image` with that prompt and capture the JSON response. Save each result to a file `extracted/<image_basename>.json`.

Example:

```
prompt = read_file("extract_prompt.md")
for img in before_images + after_images:
    result = analyze_image(workspace_filename=img, prompt=prompt)
    # ensure extracted/ folder exists
    write_file(f"extracted/{Path(img).name}.json", json.dumps(result, indent=2))
```

Later, when evaluating checklist items, load the corresponding JSON and check the fields instead of re‑analyzing the image.

If extraction fails or returns non‑JSON text, retry once. If still unsuccessful, fall back to per‑image `analyze_image` in Step 3.

### Step 2 — Before/After legitimacy check (composites)

Call `analyze_image` with both composites:

```
analyze_image(
 workspace_filenames=[<before_composite>, <after_composite>],
 prompt="Compare Before vs After installation images.
 Check: Is the Before photo legitimate (shows the actual site condition before work)?
 Do NOT reject solely because the Before image shows new or replaced equipment.
 Reject only when there is **clear and convincing** evidence of staging/fraud, such as:
 - Lab/test environment (benches, test gear, no real installation context)
 - Factory packaging or equipment still in shipping boxes
 - Equipment that appears unused/pristine inconsistent with 'before' state
 - Same equipment shown in both Before and After with no change
 - Date/time stamps that imply improper sequencing

 Focus on whether the Before photo reasonably represents the pre-work site condition.
 If the legitimacy is questionable but not definitively false, mark N_A and add a note rather than FAIL.
 Report PASS/FAIL/N_A with severity (FAIL only for clear staging)."
)
```

This is a text-only assessment — no annotation needed. Just checking legitimacy.

### Step 3 — Quality inspection (individual After photos)

If you extracted structured data in Step 1.5, evaluate checks against that data. Otherwise,
for EACH After image, call `analyze_image` individually with `annotate=true`:

```
analyze_image(
 workspace_filename=<after_image>,
 annotate=true,
 annotation_schema={
 "findings": [{
 "label": "short label",
 "bbox": "[x1,y1,x2,y2] pixel coords or null if not localizable",
 "description": "detailed description",
 "status": "PASS, FAIL, or N_A (not verifiable from photo)",
 "severity": "Critical / Major / Minor (only for PASS and FAIL — omit for N_A)"
 }]
 },
 prompt="Assess installation quality in this photo.
 Check: <paste checks from task>.
 For each check, report status (PASS/FAIL/N_A), severity, and
 provide bbox pixel coordinates for FAIL items.

 CRITICAL RULES:

1. **Mark N_A when not visible**: If a component (heat shrink, breaker tag, grounding kit, waterproofing, etc.) is NOT VISIBLE in the photo (due to angle, distance, or framing), mark N_A — NEVER FAIL for unverifiable items. FAIL requires visible evidence.

2. **Labeling**: For each component, apply rules based on termination type:
   - **Non-waterproofed cable ends** (exposed connector and jacket): require a physical ID tag (yellow or white) or jacket printing/stamping. If a required label is **not visible** in the current image, mark N_A — it may be visible in another image of the same section. FAIL only if the component is clearly shown without any label.
   - **Waterproofed cable ends** (fully encapsulated with heat shrink, tape, moulded boot, sealant, etc.): **no label required** — focus on waterproofing completeness. Mark N_A for labeling if the encapsulation is visible; FAIL only if the waterproofing itself is inadequate (exposed parts).
   - **Antennas/equipment**: require alphabet stencil or stamped characters. If the label is not visible in the current image, mark N_A — it may be visible in another image of the same section. FAIL only if the component is clearly shown without any label.
   - **Integrated labeling** (text moulded into waterproofing) is acceptable when present but not mandatory for waterproofed cables.

   Acceptable physical labels include:
   - ID tags (yellow or white) attached to cables
   - Labels printed/stamped directly on cable jackets or equipment surfaces
   - Alphabet stencil or stamped characters

   **Do NOT count watermarks, metadata overlays, on-screen annotations, or IEPMS label stamps** as valid labels.
   Extra text, format variations, or alternative naming styles on a correct physical label are NOT defects. A single photo shows one component; verify only what's visible in that photo.

3. **Connection quality**: When visible, verify secure connection (no slack, proper seating, tight terminations). If the connection point is obscured, N_A is acceptable. Do not fail for cable routing style differences (zip ties vs clamps) if cables are neatly managed and supported.

4. **Cross-pair consistency**: If Before and After labels are both visible for the same cable/equipment, they should identify the same endpoints. Mismatched IDs warrant FAIL. If only one side is visible, PASS that check (pair covers both ends).

5. **Severity justification**: For every FAIL, the bbox must precisely cover the defect, and the description must explain why it violates the requirement. Minor cosmetic issues (scratches, dust) should be Minor or PASS depending on functional impact.

If a check cannot be verified from the photo (detail not visible, angle doesn't show it, item is out of frame), mark it N_A — NEVER mark FAIL for unverifiable items.
FAIL requires visible evidence with a localizable bbox."
)
```

**IMPORTANT: If `analyze_image` returns text instead of JSON (parse failure),
call `analyze_image` again with the same parameters. Do NOT fall back to
text-only analysis — structured JSON with bboxes is required.**

For each image with FAIL findings, call `annotate_regions`:

```
annotate_regions(
 workspace_filename=<after_image>,
 annotations=[ only FAIL findings with label, bbox, color by severity ]
)
```

Severity colors: Critical=red, Major=orange, Minor=yellow.
Do NOT use `silent=true` — let annotated images appear in conversation.
**Capture each annotated image's filename — you will need it on the matching finding.**

### Step 4 — Submit findings via `final_answer`

Call `final_answer(...)` ONCE with the structured payload.

For every check you actually performed (PASS or FAIL), include one entry in `findings`:

- `check` — short label (e.g. "Cable labeling")
- `requirement` — the **full rule text** as it appeared in the task (e.g. "All visible cable ends
 must be labeled with yellow tags identifying NE and FE site IDs"). This is what we know the
 finding violates or satisfies. Always include it for both PASS and FAIL — we want to know
 what rule each result is referenced against.
- `status` — `PASS` or `FAIL`
- `severity` — only for FAIL: `Critical` / `Major` / `Minor`
- `description` — one-line observation
- `annotated_image` — filename of the annotated image showing this finding. Set this on FAIL
 findings that were drawn via `annotate_regions`. Omit on PASS findings and on FAIL items
 that couldn't be localized.

For checks the photo couldn't verify (out of frame, angle, etc.), do NOT add them to `findings`.
Just increment `not_verifiable_count`.

`severity` at the top level = the highest severity across all FAIL findings, or `"None"` if all PASS.
`status` at the top level = `FAIL` if any finding is FAIL, else `PASS`.

Include `legitimacy` (object) for the Before/After check from Step 2.

**Annotation requirement:** For every FAIL finding that can be localized, provide a bbox in the annotated_image. If the defect is visible but too small/unclear to bbox reliably, mark as "N_A (defect observed, localization uncertain)" in the findings list and do NOT include an annotated image. This preserves traceability without forcing inaccurate bboxes.

### Step 5 — Brief markdown summary (final assistant message)

After `final_answer` returns, send ONE short markdown message that mirrors the structured payload.
This exists for human review during testing — keep it minimal, no narrative paragraphs.

Use exactly this format:

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

One bullet per finding, in the same order as in `final_answer.findings`. Stop after the list —
do not add a Summary or Recommendations section.
