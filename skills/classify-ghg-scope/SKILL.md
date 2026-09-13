---
name: classify-ghg-scope
description: Assigns Scope 1/2/3(+category)/Excluded/human-review to activity records with rationale, evidence, and confidence, testing ownership/control first and never forcing a low-confidence classification.
---

PURPOSE
Assigns Scope 1/2/3(+category)/Excluded/human-review to activity records with rationale, evidence, confidence.

STEPS
1. Test ownership/control against organizational boundary first — decides Scope 1 vs Scope 3 for vehicles/leased assets/franchises.
2. Scope 1 if combustion/release at owned/controlled source (stationary combustion, mobile combustion in owned vehicles, process emissions, fugitive refrigerants).
3. Scope 2 if purchased electricity/steam/heating/cooling; note location-based/market-based/both required.
4. Scope 3 otherwise, assign exactly one of fifteen categories; watch confusions: fuel purchased not combusted by org (Cat 3), inbound freight paid by org (Cat 4) vs outbound paid by customer (Cat 9), leased asset direction (Cat 8 vs 13).
5. Excluded only when operational boundary explicitly excludes — record exclusion reason; excluded != zero.
6. Score confidence (default threshold 0.75) — reduce for vague descriptions/generic ERP codes/missing geography/supplier ambiguity; below threshold => REQUIRES_HUMAN_REVIEW naming the specific ambiguity, never force a classification.
7. Emit evidence quoting exact source fields, never paraphrase evidence not present.

OUTPUT EXAMPLE — confident Scope 2 classification:
"RECORD: AR-0921
Classification: Scope 2 (Purchased Electricity)
Basis: 'electricity purchased' + owned facility meter, account #4471-B
Both location-based and market-based required for this record.
Evidence quoted: description field = \"Grid electricity — Plant 3, meter 4471-B\"
Confidence: 0.94
Evidence tag: SOURCE_FACT"

OUTPUT EXAMPLE — REQUIRES_HUMAN_REVIEW distinguishing inbound vs outbound freight:
"RECORD: AR-1187
Classification: REQUIRES_HUMAN_REVIEW
Ambiguity: description field = \"Freight charge — carrier XPO\" does not state direction (inbound to our facility vs outbound to customer) or who paid the carrier; this determines Cat 4 (Upstream Transportation) vs Cat 9 (Downstream Transportation).
Confidence: 0.41 (below 0.75 threshold)
Evidence tag: HUMAN_REVIEW_REQUIRED
Recommended remediation: confirm shipping direction and payer from the underlying invoice or PO."