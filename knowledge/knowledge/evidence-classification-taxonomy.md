# Evidence Classification Taxonomy — Precise Definitions

Every output produced by this agent must carry at least one of the following eight tags. These definitions are exact and must not be blended or used interchangeably — the UI renders these tags directly, so precision here is load-bearing.

1. SOURCE_FACT — A value taken directly and verbatim from an uploaded source document or system-of-record field, with no interpretation, conversion, or inference applied. Example: a quantity field copied exactly from an invoice row.

2. CALCULATED_VALUE — A numeric result produced exclusively by the deterministic calculation tool, carrying a CALC-YYYY-NNNNNN identifier, formula string, and tool/timestamp metadata. Never a value computed in prose or reasoning.

3. ASSUMPTION — A stated premise the agent or a user has adopted in the absence of direct evidence, used to allow processing to continue, and explicitly flagged as an assumption rather than a fact (e.g., assuming a boundary or a default reporting year when not explicitly stated in source data).

4. ESTIMATE — A value derived through an approximation method that is documented and reproducible (e.g., a spend-based estimate using an economic input-output factor) but is understood to carry more uncertainty than a direct measurement — distinct from ASSUMPTION (a premise) and from CALCULATED_VALUE (an exact deterministic result from measured inputs).

5. PROXY — An emission factor or data point substituted for the ideal match because no exact match exists in the approved library, always accompanied by PROXY_EMISSION_FACTOR=TRUE and a stated reason for the substitution.

6. MANAGEMENT_ASSERTION — A claim, statement, or figure provided by the organization's management or a third party (e.g., a supplier-provided emissions figure) that has not been independently verified or recalculated by this agent's methodology.

7. MISSING_EVIDENCE — An explicit statement that required supporting evidence, data, or documentation does not exist in the available records — never treated as zero, never silently defaulted, and always routed toward human review or remediation.

8. HUMAN_REVIEW_REQUIRED — A flag indicating the agent has stopped short of a conclusion (classification, factor match, calculation, claim assessment, or dossier gate) because available evidence, confidence, or approval authority was insufficient to proceed automatically; always paired with a stated reason and, where possible, a recommended next step.