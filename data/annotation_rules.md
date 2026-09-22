# Annotation rules

The rule actually applied to each task when the 120 documents were labeled, and the edge cases
that had to be decided. Drafted by Claude from `tasks.yaml`, `decisions.md` and the labels
themselves, then reviewed by the labeler. Lines marked **[confirm]** were inferred from the
labels rather than stated by the labeler, and should be corrected if they are wrong.

All 120 documents were labeled blind: from the document alone, with no model output shown for any
row. No document was labeled by correcting a prediction.

## General

- A blank cell means the document gives no answer. It is scored: blank against blank gold is a
  true negative, a value against blank gold is a false positive.
- The three yes/no tasks are the exception; they are never blank. A clause that is absent is
  answered `no`, not "unknown".
- An amendment is labeled on its own terms for every task except `agreement_type`, which takes
  the type of the agreement it amends.

## agreement_type

One of seven groups, never blank. The 25 CUAD-style types were collapsed into these groups before
labeling; the old names are kept as synonyms in `tasks.yaml`.

| group | covers |
| --- | --- |
| `distribution` | distributor, reseller, agency |
| `ip_licensing` | license, IP, trademark and technology licenses, franchise |
| `services` | service, consulting, maintenance, outsourcing, hosting, transportation |
| `supply_manufacturing` | supply, manufacturing |
| `marketing` | marketing, promotion, co-branding, sponsorship, endorsement, affiliate |
| `partnership` | joint venture, strategic alliance, collaboration, development |
| `non_compete` | agreements whose main purpose is a non-compete or non-solicit undertaking |

- Judged by the agreement's main commercial purpose, not its title. A document titled
  "Strategic Alliance Agreement" that is in substance a distribution arrangement is
  `distribution`. **[confirm]**
- Where two groups both fit, the one carrying the money obligation wins. **[confirm]**
- `non_compete` has no examples in the 120. It stays a group because merging it elsewhere would
  be wrong, not because it can be measured.

Observed: services 34, partnership 27, marketing 21, ip_licensing 15, distribution 14,
supply_manufacturing 9, non_compete 0.

## parties

Every company or other entity that is a party. Semicolon-separated, one entity per entry.

- Individuals who signed on a company's behalf are **not** parties. An individual is listed only
  when the individual is personally a party (e.g. a consultant contracting in their own name).
- Affiliates, guarantors and outside counsel are not listed unless named as parties.
- The legal name as the agreement first writes it, not a defined short name ("Company",
  "Consultant", "BLI").
- **Known defect, accepted:** 16 entries in 13 documents join several names into one entry
  ("Stryker and Conformis"; three names separated by commas). The harness splits only on
  semicolons, so each of these counts as one party with a long name. A perfect model scores about
  P 0.89 / R 0.95 / F1 0.92 on this task. See `decisions.md`, 2026-09-22.
- Boilerplate entries ("collectively the Parties") were removed mechanically before import.

Observed: median 2 parties per document, maximum 19.

## agreement_date

The date the agreement was signed or is stated to be dated, ISO 8601, possibly partial.

- Only the granularity the document gives: a document dated "March 2019" is labeled `2019-03`,
  never `2019-03-01`. Partial gold is matched at its own granularity; `allow_coarser` is false,
  so `2019` does not match `2019-06-15`.
- The date in the preamble ("dated as of ...") is taken over a signature date where they
  differ. **[confirm]**
- An effective date that differs from the signing date is not used. **[confirm]**
- Blank when the document gives no date.

Observed: 110 dated (98 to the day, 5 to the month, 7 to the year), 10 blank.

## governing_law

The jurisdiction from the governing-law clause: a two-letter US state code, `DC`, or `FOREIGN`.

- Read from the governing-law clause only. A party's address, the place of incorporation and the
  forum or venue clause are not used.
- Any non-US jurisdiction is `FOREIGN`, whichever country it is. The country itself is not
  recorded anywhere, by design.
- Where the clause names a state and excludes its conflict-of-laws rules, the state is still the
  answer.
- Blank when the agreement names no governing law.

Observed: 19 blank, 20 FOREIGN, then NY 25, CA 15, DE 10, FL 6, TX 5, and a long tail of states
with 1-4 examples each. 40 states have no examples.

## competition_restrictions

Any of `non_compete`, `exclusivity`, `no_solicit_customers`, `no_solicit_employees`; blank means
none. Restrictions on **either** party count.

- `exclusivity` covers both "deal only with us" and a grant of exclusive rights, including an
  exclusive territory or an exclusive product line. **[confirm]**
- A clause that only restricts use of confidential information is not a restriction here.
- A no-hire clause covering the other side's employees is `no_solicit_employees`, whether it says
  solicit or hire. **[confirm]**

Observed: 61 documents with none; exclusivity 37, non_compete 24, no_solicit_employees 13,
no_solicit_customers 8. The commonest combination is exclusivity with non_compete (9).

## has_liability_cap and liability_cap_clause

Yes or no, never blank, plus the clause text when yes.

- A cap limits the **amount** of liability: a fixed sum, the fees paid, or a multiple of them.
- A clause that only excludes categories of damages (consequential, indirect, punitive) with no
  ceiling on the amount is **not** a cap.
- A cap on one party only still counts as yes.
- The pasted passage is the operative sentence or subsection, not the whole liability article.
  Observed lengths: median 366 characters, range 79-974.
- Two documents are `yes` with no clause pasted: the cap is in the PDF but its wording could not
  be located in the extracted text. Both carry a note. They will score as misses on the span task
  and that is expected, not a model failure.

## has_audit_rights

Yes or no, never blank. Either party having the right to audit or inspect the other's books,
records or accounts counts, including an audit exercised through an independent accountant.

- A right to receive reports or statements, with no right to inspect or audit, is `no`.
  **[confirm]**

Observed: 45 yes, 75 no.

## renewal_notice_days

The notice needed to stop an automatic renewal, in days. Blank when the agreement does not renew
automatically, or renews but states no notice period.

- Months are converted at 30 days and years at 365, per the task question. The labels contain a
  360 and a 42, which look like 12 x 30 and 6 weeks. **[confirm]** which convention was used;
  the harness compares with zero tolerance, so 360 and 365 are different answers.
- The notice to terminate the agreement generally, as opposed to the notice to stop a renewal, is
  not this task and was left out. **[confirm]**

Observed: 24 documents answered, 96 blank. Values: 15, 30, 42, 60, 65, 90, 180, 360.

## termination_for_convenience

Yes or no, never blank. Either party being able to end the agreement by giving notice, with no
reason needed, counts.

- Termination for breach, insolvency, change of control or failure to meet a milestone is a
  reason, so those alone are `no`.
- A right available to only one party still counts as yes.
- A right that only applies after an initial term, or only during a window, still counts as
  yes. **[confirm]**

Observed: 45 yes, 75 no.
