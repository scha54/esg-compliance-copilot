---
name: match-emission-factor
description: Binds classified activity records to an approved emission factor from the seeded library via strict priority hierarchy, labelling every compromise and never inventing a factor.
---

PURPOSE
Binds classified activity records to approved emission factor from seeded library via strict priority hierarchy, labelling every compromise. Never invents a factor; no fit => exception.

STEPS
1. Normalize activity descriptor to library taxonomy.
2. Apply hierarchy in order, stop at first hit:
   (a) exact activity+geography+reporting year,
   (b) exact activity+geography different year with documented applicability — record year gap,
   (c) exact activity with approved regional/national default,
   (d) approved proxy factor => PROXY_EMISSION_FACTOR=TRUE + reason,
   (e) no match => HUMAN_REVIEW_REQUIRED.
3. For Scope 2: select location-based grid factor, and if RECs/GOs/PPAs evidenced, market-based factor separately — never blend; missing contractual evidence = MISSING_EVIDENCE not zero.
4. Verify unit compatibility between factor denominator and record unit; incompatible+unconvertible => exception, never swap factors to force fit.
5. Record full provenance: source, dataset, factor ID, description, value, unit, geography, year/version, gas coverage, GWP basis, scope applicability, effective period, source ref, one-sentence rationale.
6. Flag OUTDATED_FACTOR if factor year precedes reporting year and newer approved version exists; surface in disclosure gates.
7. Log decision + hierarchy tier reached to governance log.

OUTPUT EXAMPLE — successful binding:
"BINDING: activity AR-0921 -> factor EPA-eGRID-2024-RFCW-ELEC
Tier reached: 1 (exact activity+geography+reporting year)
Factor: 0.398 kgCO2e/kWh, location-based, RFC West subregion, vintage 2024, GWP basis AR5
Effective period: 2024-01-01 to 2024-12-31 (covers reporting period)
Evidence tag: SOURCE_FACT
Rationale: direct match on utility account's stated NERC subregion."

OUTPUT EXAMPLE — HUMAN_REVIEW_REQUIRED with closest proxy option:
"BINDING: activity AR-1340 -> NO MATCH FOUND
Tiers checked: 1 (no exact year match), 2 (no documented applicability for prior year), 3 (no regional default for this activity type), 4 (closest proxy candidate: DEFRA-2025-GENERIC-INDUSTRIAL-FUEL, but geography and process type diverge materially)
Status: HUMAN_REVIEW_REQUIRED
Evidence tag: HUMAN_REVIEW_REQUIRED
Recommended remediation: confirm exact fuel type and combustion process, or approve the flagged proxy with a documented rationale."