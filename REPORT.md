# Contract data extraction: results

Project `doc-proc-demo-1`, finished 22 September 2026, using doc-harness 0.1.8 and the
Claude Haiku 4.5 model.

## What it does

It reads a commercial contract and answers ten questions about it. It has been run over all
**509 contracts**, and the answers are in
[runs/production/outputs.jsonl](runs/production/outputs.jsonl).

| Question | Answer it gives |
| --- | --- |
| What kind of agreement is it? | One of seven kinds: distribution, IP or licensing, services, supply or manufacturing, marketing, partnership, non-compete |
| Who are the parties? | The company names |
| When was it signed? | A date. Just the year or month if that is all the contract gives |
| Which state's law governs it? | A US state code, or FOREIGN |
| Does it restrict competition? | Any of: non-compete, exclusivity, no poaching customers, no poaching employees |
| Is there a cap on liability? | Yes or no |
| What is the cap clause? | The text of the clause |
| Can either side audit the other? | Yes or no |
| How much notice stops an auto-renewal? | A number of days |
| Can either side end it early without a reason? | Yes or no |

## How well it works

These numbers come from **32 contracts the model never saw during development**. They were
set aside at the start, labelled by hand without looking at any model output, and used once,
at the end. That is what makes them a fair estimate of how the model behaves on new
contracts. See [How it was tested](#how-it-was-tested).

**Trustworthy.** Use these answers as they are.

| Question | Result on the 32 test contracts |
| --- | --- |
| Which state's law | Right on all 29 contracts that name one, and correctly silent on the other 3 |
| When it was signed | Right on 30 of 32 |
| Who the parties are | Found 82 of the 91 companies, and named 7 that were not parties |
| Auto-renewal notice | Right on all 7 that auto-renew, and correctly silent on the other 25 |

**Usable, with a check.** These are right about three times in four. Good enough to sort or
filter contracts, not good enough to rely on for a single contract.

| Question | Result on the 32 test contracts |
| --- | --- |
| What kind of agreement | Right on 26 of 32 |
| Can either side audit | Right on 29 of 32. Found all 12 that have audit rights, and wrongly flagged 3 others |
| Can either side end it early | Right on 24 of 32. Found 8 of the 11 that allow it, and wrongly flagged 5 others |
| Does it restrict competition | Found 20 of the 25 restrictions, and flagged 8 that were not there |

**Needs a person.** Do not use these on their own.

| Question | Result on the 32 test contracts |
| --- | --- |
| Is there a liability cap | Found only **6 of the 14** contracts that have one. But it never claimed a cap that was not there: all 6 were real |
| What the cap clause says | Of the 14 contracts with a cap, it quoted the right passage on **1**, part of it on 1, and got 12 wrong or blank |

The liability cap answers are the weak point of the system. Read
[Known problems](#known-problems) before using them.

## What needs a person

The run flagged these contracts for human review. They are listed in
[runs/production/qa_report.md](runs/production/qa_report.md).

| Group | How many | Why |
| --- | --- | --- |
| Every contract where a liability cap matters | all 509 | The cap answers are unreliable, as above |
| Left a question blank that is usually answered | 287 | A blank can mean "the contract does not say", but it can also mean the model gave up |
| Least confident answers | 50 | The model was closest to the line on these |
| A random sample | 50 | See below |
| Pages that could not be read | 2 | Scanned images. Both were checked by hand and contain nothing the questions need |

**Why the random sample matters.** The other groups are contracts we already suspect are
wrong, so they will look worse than the corpus as a whole. If you only check those, you will
think the system is worse than it is. The 50 random contracts are the ones that tell you the
true error rate, because nothing about them was pre-selected. Check those too.

## How it was tested

1. **120 of the 509 contracts were labelled by hand**, by a person reading the contract, with
   no model answers shown. That labelling took about 5 hours.
2. Those 120 were split three ways, fixed in advance:
   **63 for training**, **25 for tuning**, and **32 held back**.
3. The model was improved using only the first two groups. Two attempts were made. The better
   one, which asks the model to reason step by step before answering, was chosen.
4. **The 32 held-back contracts were used once, at the end.** The file
   [runs/holdout/.lock](runs/holdout/.lock) records that. Testing twice and keeping the
   better score would make the number meaningless, so the system prevents it.
5. The chosen model scored **0.822** on the held-back contracts against **0.873** on the
   tuning contracts. The two being close means the model learned the task, rather than
   memorising the contracts it was tuned on.
6. As a comparison, the model with no tuning at all scored **0.823** on the tuning contracts.
   Tuning was worth about 5 points there.

**A caution about all these numbers.** They come from 32 contracts, and sometimes fewer:
one contract is worth 3 points. Treat them as "about right", not exact. Where the report
says "right on 24 of 32", the true rate could reasonably be anywhere from about 58% to 87%.

## Known problems

**1. Liability caps are missed more often than they are found.**
The model found 6 of 14 caps in testing. Across all 509 contracts it reports a cap in 28.5%,
while hand-labelling found caps in 44.2%. It is the same problem showing up at scale. When it
does report a cap, it has been right, so treat a "yes" as reliable and a "no" as unchecked.
This is why the automatic quality check on the full run is marked as failed.

**2. The cap clause text is usually wrong.**
It quoted the right passage for 1 of 14 caps. The instruction for this question was tuned on
the 25 tuning contracts and did not survive contact with new ones: it scored 0.628 there and
0.234 on the held-back set. The field is produced for all 509 contracts, but every value
needs a lawyer's eye.

**3. Party names have a known labelling fault.**
In 13 of the 120 hand-labelled contracts, several company names were written into one cell,
such as "Stryker and Conformis". The scorer reads that as one company with a long name, so
it counts both as a miss and as a false name. The labels were accepted as the client supplied
them. The effect: the party-name score cannot go above about 0.92 even for a perfect model.

**4. Two scores look terrible and are not.**
The "kind of agreement" and "governing law" questions are scored by averaging across every
possible answer, including the 45 US states that never appear in the test set and therefore
score zero. That drags the published averages down to 0.657 and 0.173. The real performance
is in the table above: governing law was right on all 29. Quote the counts, not those two
averages.

**5. Rare answers were never tuned for.**
61 answers were too rare in the labelled sample to train on: 52 states, 6 kinds of agreement,
and 3 kinds of competition restriction. They are still reported. Any number resting on a
handful of contracts is marked in the technical report.

**6. Nothing was measured on the 389 contracts that were never labelled.**
Every number here comes from the 32 test contracts. The full run was checked for coverage and
formatting, not for correctness.

## Words used in the technical files

| Word | What it means |
| --- | --- |
| Holdout | The 32 contracts set aside and used once, at the end |
| Validation | The 25 contracts used to compare tuning attempts |
| Precision | Of the answers it gave, how many were right |
| Recall | Of the answers it should have given, how many it found |
| F1 | Precision and recall combined into one number. For the yes/no questions it is measured only on the "yes" answers, so the liability cap score of 0.600 already reflects the missed caps |
| Macro-F1 | An average across every possible answer, including answers with no examples. See problem 4 |
| Support | How many examples a number rests on. Small support means a shaky number |
| Abstention | The model answering "the contract does not say" |

## Key documents

| Document | What it is |
| --- | --- |
| [runs/production/outputs.jsonl](runs/production/outputs.jsonl) | **The deliverable**: answers for all 509 contracts |
| [runs/production/qa_report.md](runs/production/qa_report.md) | The automatic checks on the full run, and the review lists |
| [programs/compiled/exp_002_prompt.md](programs/compiled/exp_002_prompt.md) | The exact instructions given to the model, in readable form |
| [tasks.yaml](tasks.yaml) | The ten questions, and how each answer is checked |
| [data/annotation_rules.md](data/annotation_rules.md) | The rules the human labeller followed, including the hard cases. Read this before disputing a score |
| [decisions.md](decisions.md) | Every judgement call made during the project, with the numbers behind it |
| [data/labels.jsonl](data/labels.jsonl) | The 120 hand-labelled contracts |
| [data/splits.json](data/splits.json) | Which contracts were used for training, tuning and testing |
| [config.yaml](config.yaml) | Model, budgets and settings |
| [docs/protocol.md](docs/protocol.md) | The method this project followed |

