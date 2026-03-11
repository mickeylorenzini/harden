# Design: Harden Verification Criteria + Eval Harness

## Problem

Harden's Implementation Plans domain criteria check structural consistency
(summary-detail correspondence, precondition ordering, command existence) but
do not check whether tasks include concrete verification methods. Plans pass
harden audits despite having no way for an executor to confirm whether each
step succeeded. This results in verification-free plans that shift the burden
to execution time — where it consistently gets dropped.

## Solution

Three changes to the harden repo:

1. Add verification criteria to the Implementation Plans domain in SKILL.md
2. Add a "verification-free planning" anti-pattern to SKILL.md
3. Create an eval harness with fixture plans and expected results for
   regression testing after SKILL.md updates

## Design Decisions

- **Verification level: concrete + falsifiable (not test-first).** Criteria
  require that every task has a verification method specific enough for a
  literal executor to confirm success. Does not prescribe test-first ordering
  or TDD specifically — this keeps harden domain-neutral.
- **All tasks, not just output-producing tasks.** Every task produces a result;
  every result can be checked. Exempting setup/scaffolding tasks creates a
  loophole.
- **No domain-specific references in anti-pattern.** The anti-pattern describes
  the universal failure mode without referencing TDD, code, or any specific
  workflow. It applies equally to plans for code, protocols, deployments, and
  documents.
- **Eval harness is a markdown doc, not a skill.** Harden updates are
  infrequent. A dedicated eval skill would add maintenance overhead and token
  budget cost disproportionate to its use frequency. A `run-eval.md` file that
  Claude follows on demand achieves the same result without skill
  infrastructure.

## Changes

### 1. SKILL.md — Implementation Plans criteria (2 bullets added)

After the existing 4 bullets in "Implementation Plans / Execution Runbooks":

```markdown
- Every task must include a concrete verification method: a command with
  expected output, a file/state existence check, or an observable result.
  "Verify it works" and "run tests" without specifics are insufficient —
  a literal executor must be able to confirm success without follow-up.
- Verification methods must be falsifiable — they must be able to fail.
  "Check that the file exists" is falsifiable. "Review the output" is not.
```

### 2. SKILL.md — Anti-pattern (1 entry added)

After "Static-only plan auditing":

```markdown
**Verification-free planning:** Approving an implementation plan where tasks
lack concrete verification methods. Every task produces a result; every result
can be checked. Plans without per-task verification shift the burden to the
executor to invent checks — which means they often don't.
```

### 3. Eval harness

**Location:** `tests/` in repo root

**Structure:**
```
tests/
  run-eval.md
  fixtures/
    plan-no-verification.md
    plan-with-verification.md
    plan-partial-verification.md
    plan-vague-verification.md
  expected/
    plan-no-verification.expected
    plan-with-verification.expected
    plan-partial-verification.expected
    plan-vague-verification.expected
```

**Fixtures:**
- `plan-no-verification.md` — 3 tasks, none have verification methods
- `plan-with-verification.md` — 3 tasks, all have concrete falsifiable
  verification (command + expected output)
- `plan-partial-verification.md` — 3 tasks, 1 missing verification
- `plan-vague-verification.md` — 3 tasks, all say "verify it works" or
  "run tests" (insufficient specificity)

Each fixture is a realistic minimal implementation plan (~30 lines, 3 tasks)
in a domain-neutral context (e.g., "set up a data pipeline" — not tied to
any specific language or framework).

**Severity mapping:** Missing verification entirely = 🔴 Critical (executor
has no way to verify). Vague or unfalsifiable verification present = 🟡
Significant (intent exists but executor can't act on it).

**Expected files:**
- `plan-no-verification.expected` — Flag all 3 tasks, severity 🔴
- `plan-with-verification.expected` — Pass clean (no verification flags)
- `plan-partial-verification.expected` — Flag 1 task, severity 🔴
- `plan-vague-verification.expected` — Flag all 3 tasks, severity 🟡

**`run-eval.md`:**
- Instructions for Claude to follow when invoked with "Read
  tests/run-eval.md and follow the instructions"
- For each fixture: apply the Implementation Plans domain criteria from
  SKILL.md (no web research, no full harden process — criteria check only)
- Compare findings against companion `.expected` file
- **Grading rubric:** A fixture passes if harden flags the same tasks at
  the same severity as the `.expected` file. Exact wording differences are
  acceptable — match on task identity and severity, not phrasing.
- Report per-fixture: name, expected flags, actual flags, match/mismatch
- Summary line: X/4 passed
- **On failure:** Investigate SKILL.md criteria first (are they clear enough
  to produce the expected result?). Update fixture or `.expected` only if
  the fixture itself is flawed, not to paper over a criteria gap.

**When to run:** After any SKILL.md update, before pushing.

## Scope Boundary

This changes only the harden repo:
- SKILL.md (~10 lines added)
- tests/ directory (new, 9 files)

No changes to: CLAUDE.md, writing-plans skill, plan-write-guard hook,
or any other skill or repo.

## Acceptance Criteria

1. SKILL.md Implementation Plans section includes verification criteria
2. SKILL.md Anti-Patterns section includes "Verification-free planning"
3. SKILL.md total line count remains under 250
4. All 4 fixture plans exist and are realistic minimal plans
5. All 4 .expected files specify which tasks to flag and at what severity
6. run-eval.md contains complete instructions for running the eval
7. Running the eval: for each fixture, harden flags the same tasks at
   the same severity as the .expected file
