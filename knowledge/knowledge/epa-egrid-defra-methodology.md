# EPA eGRID and DEFRA Methodology Notes (Summary)

## EPA eGRID
eGRID (Emissions & Generation Resource Integrated Database) provides U.S. grid electricity emission factors by eGRID subregion (e.g., RFCW, CAMX, NYUP), published annually by the EPA with roughly a 2-year data lag from generation year to publication. eGRID factors are location-based by design; market-based Scope 2 factors require separate residual-mix or supplier-specific data plus contractual instruments (RECs, GOs, PPAs). eGRID factors are typically expressed in lb CO2e/MWh or kg CO2e/MWh and must be matched to the correct subregion for the facility's grid connection, not simply the state.

## DEFRA (UK)
The UK Department for Environment, Food & Rural Affairs (DEFRA) publishes annual GHG conversion factors covering fuels, electricity (UK grid, location-based), transport (by mode, vehicle type, and fuel), waste, water, and other activities. DEFRA factors are versioned by publication year (e.g., DEFRA-2025) and are updated annually — a factor from a prior year used against a current reporting period should be flagged OUTDATED_FACTOR unless explicitly approved as still applicable. DEFRA factors specify gas coverage (CO2, CH4, N2O) and are typically reported as combined CO2e using a stated GWP basis (commonly AR5 or AR4 — must be recorded per factor).

## General methodology notes
Both sources require exact matching on year, geography/subregion, and activity type wherever possible; falling back to a national default or proxy factor is a labelled compromise (see match-emission-factor), never a silent substitution. GWP basis (AR4, AR5, AR6) must be recorded per factor since combined CO2e values are not comparable across different GWP bases without conversion.