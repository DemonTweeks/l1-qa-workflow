# Extraction Prompt for `extract_image_data`

You are a data extractor for L1 QA v2 and Visual QA. Analyze the image and return **only** a JSON object with the following schema. Do not include any other text.

```json
{
  "image_id": "<filename>",
  "type": "before" | "after",
  "site_context": {
    "environment": "indoor" | "outdoor" | "roof" | "indoor_outdoor" | null,
    "equipment_visible": ["idu", "odu", "fe_cable", "if_cable", "breaker", "grounding_kit", "antenna", "nms_screen", "other"],
    "angle_count": 1 | 2 | 3 | 4+ | null
  },
  "idu_installation": {
    "has_idu": boolean | null,
    "ventilation_gap_visible": boolean | null,
    "mounting_method": "floating_nuts" | "direct_screws" | "other" | null,
    "cable_labels": [
      {
        "end": "near_idu" | "far_towards_odu" | "far_towards_fe" | "unknown",
        "label_text": string | null,
        "readable": boolean,
        "identifies_endpoint": boolean
      }
    ]
  },
  "idu_power": {
    "breaker_visible": boolean | null,
    "breaker_tag_present": boolean | null,
    "breaker_tag_readable": boolean | null,
    "terminal_type": "tubular" | "lug" | "unknown" | null,
    "power_cable_label_present": boolean | null
  },
  "idu_grounding": {
    "grounding_cable_visible": boolean | null,
    "grounding_cable_color": "yellow_green" | "yellow" | "green" | "other" | null,
    "lugs_present": boolean | null,
    "termination_secure": boolean | null
  },
  "if_cable": {
    "connector_visible": boolean | null,
    "bending_radius_ok": boolean | null,
    "waterproofing_method": "heat_shrink" | "tape" | "moulded_boot" | "sealant" | "none" | null,
    "coverage_complete": boolean | null,
    "grounding_kit_distance": "immediate" | "within_30cm" | "beyond_30cm" | null,
    "label_required": boolean,
    "label_present": boolean | null,
    "label_text": string | null
  },
  "fe_cable": {
    "cable_ends": [
      {
        "end": "near_idu" | "near_odu" | "far_fe",
        "connector_visible": boolean,
        "tag_present": boolean,
        "tag_legible": boolean | null,
        "port_visible": boolean | null,
        "cable_seated": boolean | null,
        "port_condition": "clean" | "corroded" | "damaged" | null
      }
    ],
    "bundling_neat": boolean | null
  },
  "mw_odu": {
    "odu_visible": boolean | null,
    "mounted_securely": boolean | null,
    "captive_screws_visible": boolean | null,
    "screws_diagonal": boolean | null,
    "connector_waterproofing": "heat_shrink" | "tape" | "boot" | "none" | null,
    "grounding_kit_visible": boolean | null,
    "antenna_visible": boolean | null,
    "antenna_label_present": boolean | null
  },
  "nms_screenshots": [
    {
      "type": "15min" | "24hr" | "alarm" | "other",
      "legible": boolean,
      "contains_expected_data": boolean | null
    }
  ],
  "site_environment": {
    "watermark_iedms": boolean | null,
    "gps_enabled": boolean | null,
    "angle_diversity": boolean | null,
    "environmental_compliance": "clean" | "cluttered" | "construction_damage" | null
  },
  "link_performance": {
    "has_15min": boolean | null,
    "has_24hr": boolean | null,
    "rsl_values_visible": boolean | null,
    "downtime_percentage": number | null,
    "alarm_correlation": boolean | null
  }
}
```

Guidelines:

- If an element is not visible or cannot be determined, set its value to `null` (for scalar) or omit the array entry.
- For `label_text`: if a visible label exists, extract the exact text; otherwise `null`.
- For colors: grounding cable color; if visible, choose from options.
- Waterproofing methods: observe the technique used on connector transitions.
- For `fe_cable.cable_ends`: represent each visible end; include port/cable details when visible.
- For `nms_screenshots`: include one entry per NMS screenshot in the image; if multiple, list them.
- For `link_performance`: only extract numeric values if clearly shown.
- Do not write any commentary; return only the JSON object.
