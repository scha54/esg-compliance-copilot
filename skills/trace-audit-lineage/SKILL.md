---
name: trace-audit-lineage
description: Given any disclosure figure, calculation ID, or activity record, walks the full chain backward and forward, producing an addressable, exportable lineage chain with nothing produced by unlogged model arithmetic.
---

PURPOSE
Given any disclosure figure, calculation ID, or activity record, walks the full chain backward (disclosure figure -> calculated value -> formula -> normalized activity -> source record/row -> source document -> emission factor -> factor dataset/version/GWP basis -> matching tier -> review status) and forward, producing an addressable, exportable lineage chain with nothing produced by unlogged model arithmetic.

STEPS
1. Accept any starting reference: a disclosure figure, a CALC-YYYY-NNNNNN id, an activity_record id, or a factor id.
2. Backward trace from a disclosure figure: disclosure figure -> calculation(s) contributing to it -> formula string recorded on the calculation -> normalized activity_record(s) -> source_document (filename, row/page/line) -> emission_factor used -> factor dataset/version/GWP basis -> matching tier reached (from match-emission-factor) -> current review status of that binding.
3. Forward trace from an activity_record or factor: activity_record -> calculation(s) that consumed it -> disclosure figure(s) that aggregate it -> dossier(s)/period(s) it appears in.
4. At each hop, cite the exact record ID and the exact field(s) used — never summarize or paraphrase a hop away; if a hop cannot be resolved (e.g., a calculation references a factor_id no longer present), stop and report BROKEN_LINEAGE at that hop rather than guessing the missing link.
5. Assemble the full chain into an ordered, addressable list (each node with its ID, type, and one-line description) suitable for export as an audit trail.
6. Tag every node in the chain with its evidence classification (SOURCE_FACT, CALCULATED_VALUE, ASSUMPTION, ESTIMATE, PROXY, MANAGEMENT_ASSERTION, MISSING_EVIDENCE) as already recorded on that record — never re-derive or reclassify a tag during tracing.
7. Never perform or restate arithmetic while tracing; lineage tracing only reads and orders existing recorded values, it does not recompute them.
8. Log the trace request (starting reference, requester, chain length, any broken links found) to the governance log.

OUTPUT FORMAT
"LINEAGE: trace from DISC-2026-ESRS-E1-SC2-001
[1] Disclosure figure: Scope 2 location-based, FY2025, 412.6 tCO2e (CALCULATED_VALUE)
[2] Calculation: CALC-2026-000184 — formula 12,400 kWh x 0.2073 kgCO2e/kWh / 1000 (CALCULATED_VALUE)
[3] Activity record: AR-0921, source utility_q1.csv row 14 (SOURCE_FACT)
[4] Source document: utility_q1.csv, uploaded 2026-02-14 (SOURCE_FACT)
[5] Emission factor: DEFRA-2025-ELEC-UK-LOC v2025.1, matching tier 1 (SOURCE_FACT)
Chain: COMPLETE, no broken links
Exported: lineage-DISC-2026-ESRS-E1-SC2-001.json"

On a broken link:
"LINEAGE: trace from CALC-2026-000201 — BROKEN_LINEAGE at hop 3: factor_id FCT-00812 referenced but not found in emission_factors. human_review_required: true"