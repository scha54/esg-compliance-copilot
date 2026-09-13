---
name: greenwashing-claim-review
description: Tests sustainability claims against calculated emissions data and evidence, registering unsupported or contradicted claims as guarded risk findings — never asserting a legal conclusion.
---

PURPOSE
Tests sustainability claims against calculated emissions data and evidence, registers unsupported/contradicted/incomplete claims as risk, never asserts a legal conclusion.

STEPS
1. Parse claim into testable assertions (absolute vs intensity, baseline year, boundary, scopes included, offsets vs reductions, timeframe).
2. Locate supporting evidence per assertion; absent = MISSING_EVIDENCE never implicit pass.
3. Run standard contradiction checks: carbon-neutral/net-zero without documented methodology+retirement evidence; reduction claims excluding material Scope 3 categories; % reductions with no valid/stated/shifted baseline; cherry-picked reporting periods; offsets presented as absolute reductions; renewable-energy claims without contractual instruments; claims numerically inconsistent with calculated totals; "complete" inventories missing material categories; outdated factors used without disclosure; unsupported supplier assertions passed through as fact. 4. Score severity: HIGH (contradicts calculated data or omits material category), MEDIUM (unsupported not contradicted), LOW (imprecise wording).
5. State regulatory implication in neutral terms (ESRS E1 requirement affected, SEC materiality consideration) — never opine on legal liability.
6. Use guarded terminology ONLY: POTENTIAL_GREENWASHING_RISK or UNSUPPORTED_SUSTAINABILITY_CLAIM — never assert the org IS greenwashing.
7. Recommend specific achievable remediation; mark human review mandatory when HIGH.

OUTPUT EXAMPLE — "carbon neutral in FY2025" HIGH-severity claim:
"CLAIM: \"We were carbon neutral in FY2025.\"
Assertions parsed: (1) net-zero-equivalent claim requiring full-scope accounting + verified offsets/retirements for the residual; (2) implicit boundary = whole organization; (3) implicit timeframe = FY2025.
Evidence check: calculated Scope 1+2+3 total for FY2025 = 8,412 tCO2e (CALCULATED_VALUE). No offset retirement records found in claims_register or source_documents for FY2025. MISSING_EVIDENCE: offset/retirement documentation.
Contradiction: claim of full neutrality is not supported — calculated gross emissions exist with no evidenced offsetting instrument.
Severity: HIGH
Regulatory implication: under ESRS E1-4/E1-7, a neutrality claim without disclosed methodology and retirement evidence is a disclosure completeness and reliability concern; under SEC climate disclosure rules this is a materiality consideration for any public claim referenced in filings or investor communications.
Classification: POTENTIAL_GREENWASHING_RISK
Evidence tag: MISSING_EVIDENCE, ASSUMPTION (implicit boundary)
Recommended remediation: (a) produce verified offset retirement certificates covering the full 8,412 tCO2e, or (b) revise the claim to state the gross inventory and any partial offsetting explicitly.
human_review_required: true"