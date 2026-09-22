# doc_proc_demo_1

A doc-harness project, pinned to doc-harness 0.1.8.

## Setup

```
pyenv virtualenv 3.12.11 doc-proc-demo-1
pyenv local doc-proc-demo-1
pip install pip-tools
make lock
make sync
```

## Where to start

```
doc-harness status
```

Then read `docs/protocol.md` once, and `CLAUDE.md` for the invariants.

## Layout

```
tasks.yaml          the tasks, and the source of truth for everything generated
config.yaml         models, budgets, split seed, thresholds, truncation policy
custom/             project-specific normalizers, matchers, extractors
decisions.md        human decisions, recorded as they are made
data/               labels, splits, the labeling plan and sheet: committed
  pdfs/, text/      the documents and their cached text: gitignored
programs/           baseline inputs and compiled programs
runs/               one directory per scoring run, plus the leaderboard
REPORT.md           written at close
```
