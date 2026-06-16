# Annotated images README

This folder describes naming, color, and indexing conventions for annotated images produced by the visual QA agent.

Placement
- Put annotated images in: `workspace/results/<section_number>_<slug>/annotated/`

Filename convention
- `<section>_<check_short>_source-<origname>_fail-<severity>.png`
  - Example: `2.7_labeling_source-img-002_fail-Major.png`

Color conventions (agent may use these colors when drawing bboxes):
- Critical: red
- Major: orange
- Minor: yellow

Index file
- Create `annotated_index.md` listing each annotated image and metadata in one line:
  - `- annotated/<file.png> — source: <source.jpg> — bbox: [x1,y1,x2,y2] — severity: Major`

Use these files as the proof artifacts referenced from `results.md` and `summary.json`.
