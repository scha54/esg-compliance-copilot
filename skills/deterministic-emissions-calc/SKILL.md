---
name: deterministic-emissions-calc
description: Converts an activity record and bound factor into a traceable tCO2e figure by calling the deterministic calc TOOL only — never computes the arithmetic itself.
---

PURPOSE
Converts activity record + factor into traceable tCO2e via the deterministic calc TOOL only, never computes itself.

STEPS
1. Validate inputs — missing quantity/unit/factor => MISSING_EVIDENCE, stop.
2. Resolve unit via documented conversion table (kWh<->MWh, therms->kWh, gallons<->liters, kg<->tonnes, miles<->km, passenger-km, tonne-km); ambiguous/unrecognized => UNIT_AMBIGUOUS=TRUE, create exception, stop, never guess.
3. Check period applicability vs factor's effective period; outside => OUTDATED_FACTOR warning, require review, don't proceed silently.
4. Call the tool with {activity_record_id, quantity, source_unit, conversion_factor_id, factor_id, rounding_rule}; never restate/re-derive/estimate/mental-math/confirm arithmetic in prose.
5. Persist: tool writes immutable calculations row, returns CALC-YYYY-NNNNNN with original value/unit, conversion factor+source, converted value/unit, factor value/unit/version, formula string, result, result unit, tool id, timestamp.
6. Tag CALCULATED_VALUE; if factor was proxy also carry PROXY + PROXY_EMISSION_FACTOR=TRUE forward.
7. Log tool call + inputs/outputs ref to governance log.

OUTPUT FORMAT
"CALCULATION: CALC-2026-000184
Record: INV-10492 (row 14, utility_q1.csv)
Formula: 12,400 kWh x 0.2073 kgCO2e/kWh / 1000
Original: 12,400 kWh   Converted: 12,400 kWh (conversion factor 1.0, identity)
Factor: DEFRA-2025-ELEC-UK-LOC v2025.1 (location-based)
Result: 2.571 tCO2e
Evidence tag: CALCULATED_VALUE
Confidence: HIGH
Tool: calc-engine@1.0.0   Calculated: 2026-03-04T11:02:19Z"

On refusal:
"STATUS: BLOCKED" with record ID, stage, issue, recommended remediation, human_review_required: true.

NEVER emit a numeric tCO2e value that did not come from the tool.