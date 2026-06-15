## Link Performance (2.19-2.26/3.20-3.27)

### To Do's
- [ ] If present, attach the latest 15-minute and 24-hour performance statistics. Screenshots must be clear and readable.
- [ ] Basic threshold checks:
  - RSL values typically within -30 to -70 dBm depending on band and distance. Extreme outliers (> -20 or < -80) may indicate measurement error → mark N_A and request clarification rather than auto-FAIL.
  - Downtime percentage low (<5% for 15-min stats). Higher values warrant comment but not immediate FAIL without context.
- [ ] Check for alarm correlation: if General Alarm section showed active alarms, Link Performance should reflect corresponding degraded periods. If alarms exist but performance metrics appear normal, flag as inconsistent (N_A with note) rather than FAIL.

### To Don'ts
- [ ] Do NOT auto-reject based on single outlier without considering link specifics
- [ ] Do NOT require perfect uptime; focus on significant degradations

### Image Extraction Checklist (Screenshots)
- [ ] 15-min stats attached? (or N_A)
- [ ] 24-hour stats attached? (or N_A)
- [ ] RSL within expected range? (or N_A)
- [ ] Downtime acceptable? (or N_A)
- [ ] Correlation with General Alarms addressed? (or N_A)
