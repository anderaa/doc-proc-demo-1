# Decisions

Human decisions, recorded as they are made, with the numbers they were made on. Appended to
by `make-splits`, `compile`, `holdout` and `close`; add your own entries in the same shape.

Decisions that belong here: corrected versus blind labeling assignment, borderline
fuzzy-match adjudication, the support floor and what was done about each class below it,
class collapsing, whether partial dates match finer gold values, whether abstention is
scored or the program should guess, and accepting a compiled program over the baseline.

## 2026-09-22 -- corpus and model

- Removed `ADUROBIOTECH,INC_06_02_2020-EX-10.7-CONSULTING AGREEMENT(1).PDF`: byte-identical (same MD5)
  to the file without `(1)`. Kept, it could have landed in two splits and leaked. Corpus is now 509 PDFs.
  The four other files with `(1)` in the name differ in content and were kept.
- Task and reflection model: `anthropic/claude-haiku-4-5`, chosen as the cheapest current model
  ($1 / $5 per million input / output tokens). No thinking, no effort setting (Haiku 4.5 does not
  take `effort`). A stronger reflection model is the first thing to revisit if compiled programs
  do not beat the baseline.

## 2026-09-22 -- extraction

- 509 PDFs extracted with pymupdf, no truncation. Longest text is 347,709 characters (about 87K tokens),
  inside Haiku 4.5's 200K context. Spot checks of sampled texts showed a clean text layer, so
  `transcription.mode` stays `thin_pages`.
- Two pages were flagged as scanned and left unread; `extraction.transcription.model` stays unset.
  Both were inspected by eye and carry nothing the tasks need:
  - KALLOINC_11_03_2011 strategic alliance, page 1 of 46: a cover page of logos. Its words
    ("KALLO Inc.", "STRATEGIC ALLIANCE AGREEMENT", "MOBILECARE") are in the text layer anyway.
  - PRIMEENERGYRESOURCESCORP_04_02_2007, page 4 of 4: the Guaranty Bank, FSB signature block.
    "Guaranty Bank" appears twice in the extracted text of pages 1-3.

## 2026-09-22 -- cost ceiling

- `budget.max_usd: 75`, set by the user before any model call. Estimated from the corpus (mean
  14.6K tokens per document, two predictor groups, Haiku 4.5 at $1 / $5 per million tokens):
  prefill ~$1.50, baselines ~$5-14, production ~$9 (no demos) to ~$25 (two demos). Each compile
  experiment costs ~$10 with no demos and 300 rollouts, and ~$28 with 1+1 demos and 300 rollouts.
- The installed harness (0.1.8) reads `max_usd` but nothing checks it during a run. The spend
  limit on the Anthropic API key is the hard stop.

## 2026-09-22 -- optimization budget, fitted to $75

- `max_bootstrapped_demos` 4 -> 0 and `max_labeled_demos` 4 -> 0. A demo is a whole contract (mean
  14.6K tokens). With 4+4, an experiment was estimated at ~$165, and ~9% of nine-contract prompts
  overflow Haiku 4.5's 200K window, which would fail the run under `max_failure_rate: 0.0`. With no
  demos, optimization tunes instructions only. This is a cost constraint, not a finding that demos
  do not help.
- `max_experiments` 8 -> 3 and `max_rollouts` 2000 -> 300 (per experiment), estimated ~$10 each.
- Planned total ~$50 of the $75: prefill ~$1.50, baselines ~$5, 3 experiments ~$30, holdout ~$3,
  production on all 509 documents ~$9 (Batch API).
- The BootstrapFewShot baseline reads the same demo limits, so at 0/0 it adds no demos and matches
  zero-shot. It is recorded as it runs, not skipped.

## 2026-09-22T17:57:51+00:00 - labeling sample

- Documents to label: **120** of 509, drawn at random (seed 20260918)
- Holdout: **36**, drawn at random from the sample, labeled blind
- To correct from model answers: **84**

## 2026-09-22 -- agreement_type collapsed from 25 classes to 7, before labeling

- Estimated from file names across all 509 documents, each of the 25 types is 2-9% of the corpus
  (largest ~46 documents). In the 84 non-holdout documents most types would have 0-5 examples:
  none reaches `support_floor: 30` and most fall below `measurable_floor: 10`.
- Groups: distribution (distributor, reseller, agency); ip_licensing (license, ip, franchise);
  services (service, consulting, maintenance, outsourcing, hosting, transportation);
  supply_manufacturing (supply, manufacturing); marketing (marketing, promotion, co_branding,
  sponsorship, endorsement, affiliate); partnership (joint_venture, strategic_alliance,
  collaboration, development); non_compete (non_compete).
- The old type names are kept as synonyms, so a label or answer written with an old name maps to
  its group. Decided before any labels existed, so nothing was relabeled.
- non_compete as a type is rare and will probably stay below the measurable floor. It is kept
  apart because merging it into another group would be wrong, not because it can be measured.

## 2026-09-22 -- labeling mode: blind for all 120

- The user chose `label-sheet --no-prefill`. All 120 documents, the 84 non-holdout included, are
  labeled from the document alone, with no model output shown. It costs nothing and avoids
  anchoring, at roughly twice the labeling time (~40 hours estimated, against ~23 prefilled).

## 2026-09-22 -- parties labels: mechanical cleanup, judgement left to the labeler

- Rule applied: one entity per cell, the legal name as the contract first writes it; no combined
  entries, no "referred to as the Parties" boilerplate, no defined short names.
- Claude removed 15 entries from 12 documents in labels.xlsx, in two mechanical cases only:
  boilerplate phrases, and combined entries whose every name was already present as its own entry
  (checked per row before deleting). No entry was removed that took a name away from a row.
- 15 further entries in 12 documents need the contract read to be resolved (e.g. "Gingko and BLI",
  "Company and Consultant"). Claude did not write these: 4 are in the holdout, where a label
  written from model reading would measure the model against itself, and the rest would make those
  rows Claude's labels rather than the labeler's. They are left for the user.

## 2026-09-22 -- parties labels accepted as delivered

- The user chose to accept the labels as the client provided them, defects included, rather than
  correct the remaining 16 entries that join several party names into one entry (e.g. "Stryker and
  Conformis", "AizuFujitsu Semiconductor Limited, Fujitsu Semiconductor Limited, Transphorm, Inc").
  The earlier mechanical cleanup stands; nothing further was edited.
- Measured consequence, from the project's own list matcher with theta 0.90: a joined entry costs
  twice, because the real names score as false positives and the joined phrase as a false negative.
  13 of 120 documents (4 of them holdout) carry at least one. A model that answered every party
  correctly on every document would score about P 0.89 / R 0.95 / F1 0.92 on `parties`.
- Read every `parties` number against that ceiling of ~0.92, not against 1.0, and do not spend
  optimization budget chasing the last 8 points on this task. The other nine tasks are unaffected.

## 2026-09-22T18:46:46+00:00 - support floor: agreement_type/non_compete

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/AK

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/AL

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/AR

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/CT

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/DC

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/GA

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/HI

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/ID

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/KY

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/LA

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/MD

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/ME

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/MO

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/MS

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/MT

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/NC

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/ND

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/NE

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/NH

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/NM

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/OK

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/OR

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/RI

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/SC

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/SD

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/TN

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/UT

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/VA

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/VT

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/WI

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/WV

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/WY

- Labeled examples: **0** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/AZ

- Labeled examples: **1** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/CO

- Labeled examples: **1** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/IA

- Labeled examples: **1** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/IL

- Labeled examples: **1** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/IN

- Labeled examples: **1** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/KS

- Labeled examples: **1** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/MI

- Labeled examples: **1** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/MN

- Labeled examples: **1** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/NJ

- Labeled examples: **1** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/WA

- Labeled examples: **1** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/MA

- Labeled examples: **2** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/NV

- Labeled examples: **2** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/OH

- Labeled examples: **2** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/PA

- Labeled examples: **4** (not measurable)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/TX

- Labeled examples: **5** (presence check only)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/FL

- Labeled examples: **6** (presence check only)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: competition_restrictions/no_solicit_customers

- Labeled examples: **8** (presence check only)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: agreement_type/supply_manufacturing

- Labeled examples: **9** (presence check only)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/DE

- Labeled examples: **10** (detecting total failure)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: competition_restrictions/no_solicit_employees

- Labeled examples: **13** (detecting total failure)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: agreement_type/distribution

- Labeled examples: **14** (detecting total failure)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: agreement_type/ip_licensing

- Labeled examples: **15** (detecting total failure)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/CA

- Labeled examples: **15** (detecting total failure)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/FOREIGN

- Labeled examples: **20** (detecting total failure)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: agreement_type/marketing

- Labeled examples: **21** (detecting total failure)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: competition_restrictions/non_compete

- Labeled examples: **24** (detecting total failure)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: governing_law/NY

- Labeled examples: **25** (detecting total failure)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22T18:46:46+00:00 - support floor: agreement_type/partnership

- Labeled examples: **27** (detecting total failure)
- Choice: **report_unmeasured**
- Rationale: report_unmeasured

## 2026-09-22 -- support floor: one rationale for all 61 classes

The 61 per-class entries above were answered `report_unmeasured` under a single policy, chosen by
the user rather than class by class:

- Enrichment was rejected because it means more labeling hours and more spend; 120 documents was
  itself chosen to bound both.
- Collapsing `governing_law` into NY / CA / DE / OTHER_US / FOREIGN would have made the task
  measurable but would have dropped the state name, which is what the task exists to produce.
  `agreement_type` was already collapsed once, from 25 classes to 7; collapsing further would join
  groups that are not the same kind of agreement.
- So every class below the floor of 30 stays scored and reported, and leaves the optimization
  target via `metric.excluded_classes` in config.yaml (6 agreement_type, 3
  competition_restrictions, 52 governing_law).

What remains in the optimization target: the three binary tasks, parties, agreement_date,
renewal_notice_days, liability_cap_clause, `agreement_type=services` (34) and
`competition_restrictions=exclusivity` (37).

Splits: 45/25/30 -- 63 train, 25 validation, 32 holdout, seed 20260918. Four documents moved out of
the holdout into train because they were the only example of their governing_law class (CO, KS, OH,
WA); a class the program never saw in training cannot be measured on the holdout.

## 2026-09-22 -- hand-written few-shot baseline skipped

- `programs/baseline.py` is left returning no demonstrations, on the user's instruction. Writing
  demonstrations means reading training contracts closely, and the labels are being taken as the
  client delivered them.
- Consequence: two baselines are recorded rather than three, and with `max_bootstrapped_demos` and
  `max_labeled_demos` both 0, BootstrapFewShot adds no demonstrations either, so it should score
  the same as zero-shot. The bar a compiled program has to clear is therefore zero-shot Haiku 4.5.
  That is a lower bar than the protocol intends; a compiled program beating it is weaker evidence
  than a compiled program beating a good hand-written few-shot program.

## 2026-09-22 -- how governing_law and agreement_type are to be read

- Baseline macro-F1: governing_law 0.126, agreement_type 0.543. Neither describes the model.
  `evaluate.py` averages macro-F1 over classes at or above `splits.support_floor` (30); when no
  class reaches the floor it falls back to averaging over every declared class. A 25-document
  validation split can never put 30 examples in any class, so the fallback fires on every run.
  governing_law then averages 52 states, 45 of which have no validation example and contribute 0
  each: 6.56 / 52 = 0.126. On the same predictions the model was right on 23 of 25 documents
  (exact match 0.92, micro P 0.91 / R 1.00 / F1 0.95).
- Decision: leave the metric alone and quote the right number. For governing_law and
  agreement_type the report carries exact match and micro precision/recall/F1, with macro-F1 shown
  beside them and the dilution stated. `splits.support_floor` stays 30, fixed as it was before
  labeling; lowering it would silently change which classes leave the optimization target and would
  dress up 5-example classes as measured.
- Those classes are already out of the optimization target through `metric.excluded_classes`, so
  the optimizer is not chasing the diluted number.
- Worth reporting upstream: a class with no gold examples and no predictions is averaged in as 0.
  Nothing was scored, so it should be skipped; a class the model wrongly predicts should still count.

## 2026-09-22 -- span failures examined; no matcher change made

- exp_001 liability_cap_clause 0.693 (baseline 0.627). All 6 validation misses were read by hand:
  DIVERSINET, where the model found the real cap and the gold label points at an
  exclusion-of-damages clause that annotation_rules.md says is not a cap; XACCT, where gold and
  prediction are adjacent sentences 1 character apart, both opening "XACCT'S SOLE LIABILITY AND
  LICENSEE'S EXCLUSIVE REMEDY", gold taking the maintenance sentence and the model the support
  sentence; Magenta, the known document whose cap wording is not in the extracted text, so gold is
  blank and any answer is a false positive; and BLACKBOX, INNOVIVA, TUNIU, which are genuine model
  errors.
- An earlier guess that duplicate passages were costing overlap was checked and is wrong: no
  validation document had the gold clause text appearing twice. The failing spans have zero
  overlap with gold, not partial overlap, so lowering `overlap_threshold` would change nothing.
- No matcher, normalizer or label was changed, and no rescore was run. Two of the six misses are
  label problems in labels the client supplied and the project has accepted; they stay, and the
  liability_cap_clause number is to be read as carrying about two documents of label noise in 25.

## 2026-09-22T19:26:00+00:00 - holdout reading

- Champion: **exp_002**
- Holdout aggregate: **0.822** against 0.873 on validation
- Reading: **these tasks memorized split specifics**
- Action: **revert them to baseline**

## 2026-09-22 -- shipping exp_002 as measured, with liability_cap_clause flagged

- Holdout: aggregate 0.822 (95% CI 0.766-0.874) against 0.873 on validation, gap 0.051, inside the
  interval. The champion generalized; the decision is to ship it.
- The user chose to ship exp_002 whole rather than drop the clause-text field. liability_cap_clause
  measured 0.234 on the holdout against 0.628 on validation (gap 0.395): the tuned spans
  instruction was fitted to the validation split. The field is shipped but is not fit for
  automated use; every cap clause goes to human review, and the report must carry the 0.234 beside
  it. Two of the six validation misses examined earlier were label problems, so part of that number
  is label noise, but not enough of it to rescue the task.
- The protocol's prescribed action for a large per-task gap is reverting those tasks to baseline.
  Not done, and here is why: the baseline was never measured on the holdout and cannot be now
  without an override stamped into REPORT.md, so a revert would trade a measured program for an
  unmeasured one.
- has_liability_cap 0.600, termination_for_convenience 0.667 and competition_restrictions 0.696 all
  fell 11-27 points from validation. They are usable, but only the holdout numbers may be quoted.
- governing_law is the strongest task: 29 of 29 correct on the holdout (P 1.00 / R 1.00). Its macro
  F1 of 0.173 is the dilution recorded on 2026-09-22 under "how governing_law is to be read".

## 2026-09-22 -- production gates and cost

- 509 of 509 documents produced output, 0 failures, 0 parse errors. Class distribution gate FAILED:
  has_liability_cap true 28.5% in production against 44.2% in the labeled sample.
- Diagnosed as model bias, not a corpus difference. agreement_type and governing_law shares match
  the labeled sample within 1-5 points, and the same under-calling shows on validation (model 40%
  true against 52% gold), consistent with holdout recall 0.75. About one contract in four that has
  a cap is reported as having none. The gate stays failed and understood: cap answers go to human
  review rather than being quoted on their own.
- Harness bug, and it cost money: the Batch API path cannot read a `chain_of_thought` program
  ("'ChainOfThought' object has no attribute 'signature'"). All 1,018 requests were submitted as a
  batch and billed, then every document was re-run live at full price. Production cost roughly
  $25-30 instead of ~$12. Worth reporting upstream, together with: the rollout budget guard
  erroring inside DSPy rather than stopping a run cleanly (exp_001 ran 11 rollouts past the cap);
  `compile` crashing when branching a pinned champion across a module change (fixed by
  `--no-branch`); the status PDF counter missing uppercase .PDF; macro-F1 averaging in classes with
  no gold and no predictions; and `budget.max_usd` being read but never enforced.
- Ledger figures recorded at close: 5 labeling hours (user-reported) and $52 total spend (estimated
  from token counts, not from billing).
