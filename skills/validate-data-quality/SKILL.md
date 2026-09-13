---
name: validate-data-quality
description: Scans normalized/classified/calculated records for duplicates, negative quantities, statistical spikes, and currency/period mismatches, raising ANOMALY_REQUIRES_REVIEW exceptions rather than silently correcting or discarding.
---

PURPOSE
Scans normalized/classified/calculated records for duplicates, negative quantities, statistical spikes vs facility baseline, currency/period mismatches; raises ANOMALY_REQUIRES_REVIEW exceptions rather than silently correcting or discarding.

STEPS
1. Run duplicate detection across activity_records within the same reporting period using composite key (facility/cost-center + activity descriptor + period); flag exact and near-duplicates (same descriptor/period, quantity within 1%) as POTENTIAL_DUPLICATE, do not auto-delete either record.
2. Check for negative or zero quantities where the activity type does not logically support them (e.g., negative fuel volume); flag NEGATIVE_QUANTITY_ANOMALY, do not flip the sign or assume a data-entry error — require human confirmation of the correction.
3. Compute a facility/cost-center baseline (rolling average of the same activity type over the trailing 4 reporting periods, when available) and flag any record deviating beyond a defined threshold (default: +/-40%) as STATISTICAL_SPIKE, stating the baseline, the observed value, and the percentage deviation — never silently smooth or cap the value.
4. Cross-check currency codes and units against the expected values for the facility's jurisdiction; mismatches (e.g., USD invoice recorded against a EUR-denominated facility) are flagged CURRENCY_MISMATCH.
5. Cross-check the record's stated period against the reporting period boundary; records falling outside the selected reporting window are flagged PERIOD_MISMATCH and excluded from calculation until confirmed.
6. Every flagged condition becomes an entry in the exceptions table with type = ANOMALY_REQUIRES_REVIEW, the specific sub-type (from steps 1-5), the record ID(s), the numeric evidence for the flag, and human_review_required = true.
7. Never auto-correct, discard, average away, or silently exclude a flagged record — validation only labels and routes to review, it does not remediate.
8. Log the validation pass (records scanned, exceptions raised, by sub-type) to the governance log.

OUTPUT FORMAT
"VALIDATION: pass VALIDATE-2026-000019
Records scanned: 214
Exceptions raised: 3
  - STATISTICAL_SPIKE: AR-1042 (baseline 8,200 kWh, observed 15,900 kWh, +94%)
  - NEGATIVE_QUANTITY_ANOMALY: AR-1103 (quantity -320 gallons)
  - PERIOD_MISMATCH: AR-1188 (record period 2025-Q4, reporting window 2026-Q1)
Evidence tag: ANOMALY_REQUIRES_REVIEW (all 3)
human_review_required: true"