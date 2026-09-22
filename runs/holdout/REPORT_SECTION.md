# Holdout

Measured once on 32 documents (2026-09-22T19:26:00+00:00).

Aggregate **0.822** (95% CI 0.766-0.874) against 0.873 on validation, a gap of +0.051.

Reading: **these tasks memorized split specifics**. Action: **revert them to baseline**.

| task | holdout | 95% CI | validation | gap | support | reads as |
| --- | --- | --- | --- | --- | --- | --- |
| agreement_type | 0.657 | 0.472-0.787 | 0.531 | -0.127 | 32 | coarse comparison |
| parties | 0.911 | 0.831-0.972 | 0.855 | -0.056 | 91 | measurable |
| agreement_date | 0.935 | 0.839-1.000 | 0.913 | -0.022 | 31 | measurable |
| governing_law | 0.173 | 0.096-0.173 | 0.126 | -0.047 | 29 | detecting total failure |
| competition_restrictions | 0.696 | 0.457-0.860 | 0.919 | +0.222 | 25 | detecting total failure |
| has_liability_cap | 0.600 | 0.286-0.800 | 0.870 | +0.270 | 32 | measurable |
| liability_cap_clause | 0.234 | 0.000-0.535 | 0.628 | +0.395 | 2577 | measurable |
| has_audit_rights | 0.889 | 0.727-1.000 | 1.000 | +0.111 | 32 | measurable |
| renewal_notice_days | 1.000 | 1.000-1.000 | 0.667 | -0.333 | 7 | presence check only |
| termination_for_convenience | 0.667 | 0.400-0.846 | 0.917 | +0.250 | 32 | measurable |

Per task, precision and recall with their intervals:

| task | P | P 95% CI | R | R 95% CI | F1 |
| --- | --- | --- | --- | --- | --- |
| agreement_type | 0.812 | 0.647-0.911 | 0.812 | 0.647-0.911 | 0.812 |
| parties | 0.921 | 0.846-0.961 | 0.901 | 0.823-0.947 | 0.911 |
| agreement_date | 0.935 | 0.793-0.982 | 0.935 | 0.793-0.982 | 0.935 |
| governing_law | 1.000 | 0.883-1.000 | 1.000 | 0.883-1.000 | 1.000 |
| competition_restrictions | 0.714 | 0.529-0.847 | 0.800 | 0.609-0.911 | 0.755 |
| has_liability_cap | 0.750 | 0.579-0.867 | 0.750 | 0.579-0.867 | 0.750 |
| liability_cap_clause | 0.277 | 0.258-0.298 | 0.202 | 0.187-0.218 | 0.234 |
| has_audit_rights | 0.906 | 0.758-0.968 | 0.906 | 0.758-0.968 | 0.906 |
| renewal_notice_days | 1.000 | 0.646-1.000 | 1.000 | 0.646-1.000 | 1.000 |
| termination_for_convenience | 0.750 | 0.579-0.867 | 0.750 | 0.579-0.867 | 0.750 |

## Tasks with a large gap

These learned something specific to the validation split: `competition_restrictions`, `has_liability_cap`, `liability_cap_clause`, `termination_for_convenience`.

## What this holdout cannot measure

The rarest class in each of these tasks has too few examples in the holdout for the
number above to mean much. Do not quote them on their own.

- **agreement_type**: no class reaches the support floor; macro-F1 computed over all classes
- **governing_law**: no class reaches the support floor; macro-F1 computed over all classes
- **competition_restrictions**: no class reaches the support floor; macro-F1 computed over all classes
- **renewal_notice_days**: rarest class has 7 example(s): presence check only; this task's number should not be quoted on its own
