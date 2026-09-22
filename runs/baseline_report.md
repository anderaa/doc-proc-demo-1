# Baselines

| baseline | aggregate | agreement_type | parties | agreement_date | governing_law | competition_restrictions | has_liability_cap | liability_cap_clause | has_audit_rights | renewal_notice_days | termination_for_convenience |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| zero_shot | 0.823 | 0.543 | 0.851 | 0.844 | 0.126 | 0.789 | 0.818 | 0.627 | 0.933 | 0.750 | 0.846 |
| bootstrap_few_shot | 0.823 | 0.543 | 0.851 | 0.844 | 0.126 | 0.789 | 0.818 | 0.627 | 0.933 | 0.750 | 0.846 |

Best baseline: **zero_shot** at 0.823. A compiled program has to beat this on the holdout, or shipping the baseline is the honest move.
