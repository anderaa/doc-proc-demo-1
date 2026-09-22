# doc-proc-demo-1

Generated 2026-09-22T20:20:10+00:00 by doc-harness 0.1.8.

# Baselines

| baseline | aggregate | agreement_type | parties | agreement_date | governing_law | competition_restrictions | has_liability_cap | liability_cap_clause | has_audit_rights | renewal_notice_days | termination_for_convenience |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| zero_shot | 0.823 | 0.543 | 0.851 | 0.844 | 0.126 | 0.789 | 0.818 | 0.627 | 0.933 | 0.750 | 0.846 |
| bootstrap_few_shot | 0.823 | 0.543 | 0.851 | 0.844 | 0.126 | 0.789 | 0.818 | 0.627 | 0.933 | 0.750 | 0.846 |

Best baseline: **zero_shot** at 0.823. A compiled program has to beat this on the holdout, or shipping the baseline is the honest move.


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


# Production QA

509 of 509 documents produced output; 0 failed every retry and are recorded as failures. 0 came through the Batch API and 509 live.

| check | result | detail |
| --- | --- | --- |
| coverage | pass | 509 outputs for 509 documents |
| schema | pass | 0 parse or call failure(s), 0.00% against a ceiling of 0.50% |
| class distribution | **FAIL** | shifted: has_liability_cap/false +15.7%, has_liability_cap/true -15.7% |
| null rates | pass | abstention rates are in line with validation |
| manual spot-check | pass | 50 random document(s) flagged for review |

**Gates did not all pass; do not hand these results over yet.**

## Human review routes

The random slice is not optional. The four targeted routes select documents that are
already suspect, so a quality estimate built on them alone reads worse than the corpus is.

| route | documents |
| --- | --- |
| transcribed_or_unread | 2: KALLOINC_11_03_2011-EX-10.1-STRATEGIC ALLIANCE AGREEMENT, PRIMEENERGYRESOURCESCORP_04_02_2007-EX-10.28-COMPLETION AND LIQUIDITY MAINTENANCE AGREEMENT |
| truncated | 0 |
| nulls_on_answered_tasks | 287: 2ThemartComInc_19990826_10-12G_EX-10.10_6700288_EX-10.10_Co-Branding Agreement_ Agency Agreement, ABILITYINC_06_15_2020-EX-4.25-SERVICES AGREEMENT, ACCELERATEDTECHNOLOGIESHOLDINGCORP_04_24_2003-EX-10.13-JOINT VENTURE AGREEMENT, ACCURAYINC_09_01_2010-EX-10.31-DISTRIBUTOR AGREEMENT, ADAPTIMMUNETHERAPEUTICSPLC_04_06_2017-EX-10.11-STRATEGIC ALLIANCE AGREEMENT, ADMA BioManufacturing, LLC -  Amendment #3 to Manufacturing Agreement, AFSALABANCORPINC_08_01_1996-EX-1.1-AGENCY AGREEMENT, ALAMOGORDOFINANCIALCORP_12_16_1999-EX-1-AGENCY AGREEMENT, and 279 more |
| low_confidence | 50: ALCOSTORESINC_12_14_2005-EX-10.26-AGENCY AGREEMENT, ATENTOSA_07_06_2020-EX-99.1-JOINT FILING AGREEMENT, AULAMERICANUNITTRUST_04_24_2020-EX-99.8.77-SERVICING AGREEMENT, ArcGroupInc_20171211_8-K_EX-10.1_10976103_EX-10.1_Sponsorship Agreement, BANGIINC_05_25_2005-EX-10-Premium Managed Hosting Agreement, BANUESTRAFINANCIALCORP_09_08_2006-EX-10.16-AGENCY AGREEMENT, BIOAMBERINC_04_10_2013-EX-10.34-DEVELOPMENT AGREEMENT - First Amendment, BLACKROCKMUNIHOLDINGSINVESTMENTQUALITYFUND_04_07_2020-EX-99.01-JOINT FILING AGREEMENT, and 42 more |
| random_slice | 50: ASPIRITYHOLDINGSLLC_05_07_2012-EX-10.6-OUTSOURCING AGREEMENT, ATHENSBANCSHARESCORP_11_02_2009-EX-1.2-AGENCY AGREEMENT , 2009, Antares Pharma, Inc. - Manufacturing Agreement, BERKELEYLIGHTS,INC_06_26_2020-EX-10.12-COLLABORATION AGREEMENT, BIOFRONTERAAG_04_29_2019-EX-4.17-SUPPLY AGREEMENT, BLUEROCKRESIDENTIALGROWTHREIT,INC_06_01_2016-EX-1.1-AGENCY AGREEMENT, CANOPETROLEUM,INC_12_13_2007-EX-10.1-Sponsorship Agreement, CCAINDUSTRIESINC_04_14_2014-EX-10.1-OUTSOURCING AGREEMENT, and 42 more |
| failed | 0 |


# Known limitations

Read alongside the numbers above. Full reasoning and the numbers each was decided on are in
`decisions.md`.

- **Liability cap clause text is not fit for automated use.** Holdout token-F1 0.234 against
  0.628 on validation: the tuned instruction fitted the validation split. The field is produced
  for all 509 documents but every value needs human review.
- **Liability caps are under-detected.** The program answers "cap" for 28.5% of the corpus
  against 44.2% in the labeled sample, and the production class-distribution gate fails on it.
  The same bias appears on validation (40% against 52% gold) and matches the holdout recall of
  0.75: roughly one contract in four that has a cap is reported as having none. This is model
  behaviour, not a corpus difference -- agreement type and governing law shares match the labeled
  sample within 1-5 points.
- **Quote holdout numbers, not validation ones.** has_liability_cap, termination_for_convenience
  and competition_restrictions each fell 11-27 points from validation to holdout.
- **governing_law's macro-F1 of 0.173 understates it.** The program was right on 29 of 29 holdout
  documents. Macro-F1 averages over 52 state classes, 45 of which have no examples, and the
  harness falls back to averaging all declared classes when no class reaches the support floor.
  The same dilution applies to agreement_type.
- **parties cannot exceed about 0.92.** 16 labels in 13 of the 120 documents join several party
  names into one entry, which the scorer counts as both a false positive and a miss. The labels
  were accepted as the client supplied them.
- **61 classes are reported but were never optimized for**, being below the support floor of 30:
  52 governing-law states, 6 agreement types, 3 competition restrictions. Several tasks' numbers
  rest on single-figure supports and the harness names them above.
- **Two documents have an unread scanned page**, both inspected by hand and judged to carry
  nothing the tasks need.
