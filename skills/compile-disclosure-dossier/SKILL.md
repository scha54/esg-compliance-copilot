---
name: compile-disclosure-dossier
description: Runs the five mandatory gates (Calculation Integrity, Factor Integrity, Completeness, Greenwashing, Auditability) against current data for the selected entity/period/jurisdiction, emitting a READY dossier or an exact BLOCKED_PENDING_REVIEW list — never generating a dossier while a gate fails.
---

PURPOSE
Runs the five mandatory gates (1 Calculation Integrity, 2 Factor Integrity, 3 Completeness, 4 Greenwashing, 5 Auditability) against the current calculations/exceptions/claims_register data for the selected entity/period/jurisdiction (CSRD ESRS E1 or SEC), and emits either a READY dossier or DISCLOSURE_STATUS = BLOCKED_PENDING_REVIEW with the exact list of blocking reasons per failed gate — never generates a dossier while a gate fails.

STEPS
1. Scope the run to the selected entity, reporting period, and disclosure framework (CSRD ESRS E1 or SEC climate disclosure); pull all calculations, exceptions, and claims_register entries in scope.
2. Gate 1 — Calculation Integrity: every disclosed figure must trace to a calculations row produced by the deterministic calc tool (has a CALC-YYYY-NNNNNN id, tool id, timestamp); any disclosed figure without this lineage fails the gate.
3. Gate 2 — Factor Integrity: every calculation's factor_id must resolve to an approved emission_factors entry with recorded provenance; any PROXY_EMISSION_FACTOR=TRUE or OUTDATED_FACTOR entry used in a disclosed figure must have an explicit, reviewed disclosure note — absence of the note fails the gate.
4. Gate 3 — Completeness: all in-scope activity_records for the period must be present in either calculations (resolved) or exceptions (flagged, with a status); any activity with no calculation and no exception record fails the gate as an unaccounted-for source.
5. Gate 4 — Greenwashing: every entry in claims_register touching this period/entity must have completed greenwashing-claim-review with no unresolved HIGH-severity finding; unresolved HIGH findings fail the gate.
6. Gate 5 — Auditability: every calculation and factor binding in scope must have a complete lineage chain (see trace-audit-lineage) with no broken links; any break fails the gate.
7. If all five gates pass, compile the dossier: entity, period, framework, total figures by scope/category, evidence-tag summary (counts of SOURCE_FACT/CALCULATED_VALUE/ASSUMPTION/ESTIMATE/PROXY/MANAGEMENT_ASSERTION), and mark DISCLOSURE_STATUS = READY.
8. If any gate fails, do NOT generate a dossier. Emit DISCLOSURE_STATUS = BLOCKED_PENDING_REVIEW with the exact list of blocking reasons grouped by gate number and name, each reason naming the specific record/calculation/claim ID and what is missing.
9. Log the compilation attempt (entity, period, framework, gate results, pass/fail) to the governance log regardless of outcome.

OUTPUT FORMAT — pass:
"DISCLOSURE_STATUS: READY
Entity: [entity]   Period: [period]   Framework: ESRS E1
Gates: 1 PASS, 2 PASS, 3 PASS, 4 PASS, 5 PASS
Total Scope 1: X tCO2e | Scope 2 (location): Y tCO2e | Scope 2 (market): Z tCO2e | Scope 3: W tCO2e
Evidence summary: SOURCE_FACT n1, CALCULATED_VALUE n2, PROXY n3, ASSUMPTION n4
Compiled: [timestamp]"

OUTPUT FORMAT — blocked:
"DISCLOSURE_STATUS: BLOCKED_PENDING_REVIEW
Entity: [entity]   Period: [period]   Framework: [framework]
Gate 1 (Calculation Integrity): FAIL — [record/calc ID]: [reason]
Gate 3 (Completeness): FAIL — AR-1204 has no calculation or exception record
human_review_required: true"