# Harden Eval Harness

## How to run

Open a Claude session in the harden repo and say:
"Read tests/run-eval.md and follow the instructions"

## Instructions

For each `.md` file in `tests/fixtures/`:

1. Read the fixture plan
2. Read the corresponding `tests/expected/<fixture-stem>.expected` file
3. Apply the "Implementation Plans / Execution Runbooks" domain criteria
   from `SKILL.md` to the fixture — specifically the verification-method
   criteria. No web research. No full harden process. Criteria check only.
4. List which tasks you would flag and at what severity
5. Compare your flags against the expected file

**Grading:** A fixture passes if you flag the same tasks at the same
severity as the expected file. Exact wording differences are acceptable —
match on task identity and severity, not phrasing.

**Report format:**

| Fixture | Expected | Actual | Result |
|---------|----------|--------|--------|
| plan-no-verification.md | 3x 🔴 | ... | PASS/FAIL |
| plan-with-verification.md | clean | ... | PASS/FAIL |
| plan-partial-verification.md | 1x 🔴 | ... | PASS/FAIL |
| plan-vague-verification.md | 3x 🟡 | ... | PASS/FAIL |

**Summary:** X/4 passed

**On failure:** Investigate SKILL.md criteria first — are they clear enough
to produce the expected result? Update fixture or expected file only if the
fixture itself is flawed, not to paper over a criteria gap.
