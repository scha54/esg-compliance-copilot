---
name: ingest-normalize-activity-data
description: Normalizes ERP/invoice/freight/travel uploads (CSV/XLSX/PDF) into activity_records rows, dedupes, stamps lineage, masks sensitive values, flags missing required fields, and never fabricates a row.
---

PURPOSE
Normalizes ERP/invoice/freight/travel uploads (CSV/XLSX/PDF) into activity_records rows, dedupes, stamps lineage (source_documents header fields), masks sensitive values (amount_masked), flags missing required fields, never fabricates a row.

STEPS
1. Identify source document type and required schema fields for that type (utility invoice: account_id, period_start, period_end, quantity, unit, cost; freight manifest: origin, destination, mode, weight, distance; travel: traveler, mode, distance, class; ERP GL export: cost_center, account_code, vendor, amount, description).
2. Parse the uploaded file with the appropriate reader; for PDF, extract only fields present in visible text/tables — never infer a field from surrounding context if not explicitly present.
3. For every candidate record, check required fields are present and non-null. Missing required field => do not fabricate or default; tag record MISSING_EVIDENCE, list which field(s), and stop normalizing that specific row (other rows continue).
4. Deduplicate against existing activity_records using a composite key (source_document_id + row_number + normalized_description + quantity + period) — never silently merge two distinct-looking rows; report duplicates found and skip re-insertion, referencing the original record ID.
5. Stamp lineage: record source_document_id, original filename, upload timestamp, row/page/line reference, extraction method (parsed-table / OCR-text / structured-CSV), and ingesting user.
6. Mask sensitive values before display/storage in any output: monetary amounts default to amount_masked (e.g., partial redaction or bucketed range) unless the requesting context explicitly needs the raw figure for calculation; the raw value is retained in the database but never echoed unmasked in prose responses.
7. Normalize units and descriptors to the internal taxonomy where unambiguous (e.g., "KWH", "kwh", "kW·h" => "kWh"); if the source unit is ambiguous or unrecognized, do not guess — tag UNIT_AMBIGUOUS and pass to human review, do not proceed to classification.
8. Write normalized rows to activity_records with status = "normalized" and evidence tag SOURCE_FACT for values taken directly from the document; never upgrade a MISSING_EVIDENCE field to an assumed value at this stage.
9. Log the ingestion batch (document ID, row count, rows normalized, rows flagged, rows deduplicated) to the governance log.

OUTPUT FORMAT
"INGESTION: batch INGEST-2026-000042
Source: utility_q1.csv (14 rows)
Normalized: 12 rows -> activity_records
Flagged (MISSING_EVIDENCE): 1 row (row 9, missing period_end)
Duplicate (skipped): 1 row (row 3, matches AR-0087)
Evidence tag: SOURCE_FACT (normalized fields), MISSING_EVIDENCE (row 9)
Ingested by: [user] at 2026-03-04T10:11:02Z"

On a file that cannot be parsed or has no recognizable schema:
"STATUS: BLOCKED — unable to map file structure to a known activity-data schema. Recommend: confirm document type or provide a column mapping. human_review_required: true"

NEVER fabricate a row, a value, or a period that is not explicitly present in the source document.