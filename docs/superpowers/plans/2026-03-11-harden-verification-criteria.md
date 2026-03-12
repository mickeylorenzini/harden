# Harden Verification Criteria + Eval Harness Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add verification-method criteria to harden's Implementation Plans domain and create an eval harness to regression-test harden after updates.

**Architecture:** Two bullets added to SKILL.md's Implementation Plans criteria section, one anti-pattern added to Anti-Patterns section, plus a `tests/` directory with 4 fixture plans, 4 expected-result files, and a `run-eval.md` instruction file.

**Tech Stack:** Markdown only — no code, no dependencies.

**Spec:** `docs/superpowers/specs/2026-03-10-harden-verification-criteria-design.md`

---

## Chunk 1: All Tasks

**Dependencies:** Task 1 must complete before Tasks 2-3. Tasks 2 and 3 are independent of each other.

### Task 1: Update SKILL.md — Add verification criteria and anti-pattern

**Files:**
- Modify: `SKILL.md:194` (after last Implementation Plans bullet)
- Modify: `SKILL.md` Anti-Patterns section (after last entry)

- [ ] **Step 1: Add verification criteria bullets**

Insert after line 194 (after the "Hook/automation rules" bullet) in the "Implementation Plans / Execution Runbooks" section:

```markdown
- Every task must include a concrete verification method: a command with
  expected output, a file/state existence check, or an observable result.
  "Verify it works" and "run tests" without specifics are insufficient —
  a literal executor must be able to confirm success without follow-up.
- Verification methods must be falsifiable — they must be able to fail.
  "Check that the file exists" is falsifiable. "Review the output" is not.
```

- [ ] **Step 2: Add anti-pattern**

Insert after the "Static-only plan auditing" entry (search for the heading text — line number will have shifted from Step 1 insertion), with a blank line separator:

```markdown
**Verification-free planning:** Approving an implementation plan where tasks
lack concrete verification methods. Every task produces a result; every result
can be checked. Plans without per-task verification shift the burden to the
executor to invent checks — which means they often don't.
```

- [ ] **Step 3: Verify SKILL.md line count**

Run: `wc -l SKILL.md`
Expected: Under 250 lines (current: 231, adding ~10 lines → ~241)

- [ ] **Step 4: Verify insertion points are correct**

Run: `grep -n "Hook/automation" SKILL.md` — should show the bullet immediately before the new verification criteria.
Run: `grep -n "Verification-free" SKILL.md` — should show the new anti-pattern.
Run: `grep -n "Static-only plan" SKILL.md` — should show the anti-pattern immediately before the new one.

- [ ] **Step 5: Commit**

```bash
git add SKILL.md
git commit -m "Add verification criteria and anti-pattern to Implementation Plans domain"
```

---

### Task 2: Create eval fixture plans

**Files:**
- Create: `tests/fixtures/plan-no-verification.md`
- Create: `tests/fixtures/plan-with-verification.md`
- Create: `tests/fixtures/plan-partial-verification.md`
- Create: `tests/fixtures/plan-vague-verification.md`

All fixtures use the same domain-neutral scenario: a 3-task plan for setting up a data ingestion pipeline (create config, write parser, set up scheduler). Only the verification methods differ.

- [ ] **Step 1: Create plan-no-verification.md**

```markdown
# Data Ingestion Pipeline — Implementation Plan

**Goal:** Set up a pipeline that reads CSV files from a source directory,
parses them, and loads records into a database on a schedule.

---

### Task 1: Create pipeline configuration

Create `config/pipeline.yaml` with source directory, database connection
string, polling interval, and file pattern settings.

### Task 2: Write CSV parser module

Create `src/parser.py` that reads CSV files matching the configured pattern,
validates column headers against an expected schema, and returns a list of
record dictionaries.

### Task 3: Set up scheduled ingestion

Create `src/scheduler.py` that polls the source directory at the configured
interval, passes new files to the parser, and inserts parsed records into
the database.
```

- [ ] **Step 2: Create plan-with-verification.md**

```markdown
# Data Ingestion Pipeline — Implementation Plan

**Goal:** Set up a pipeline that reads CSV files from a source directory,
parses them, and loads records into a database on a schedule.

---

### Task 1: Create pipeline configuration

Create `config/pipeline.yaml` with source directory, database connection
string, polling interval, and file pattern settings.

**Verify:** `cat config/pipeline.yaml | grep source_dir` returns a non-empty
value. `python -c "import yaml; yaml.safe_load(open('config/pipeline.yaml'))"` exits 0.

### Task 2: Write CSV parser module

Create `src/parser.py` that reads CSV files matching the configured pattern,
validates column headers against an expected schema, and returns a list of
record dictionaries.

**Verify:** `python -m pytest tests/test_parser.py -v` passes 3 tests:
`test_valid_csv_returns_records`, `test_missing_column_raises_error`,
`test_empty_file_returns_empty_list`.

### Task 3: Set up scheduled ingestion

Create `src/scheduler.py` that polls the source directory at the configured
interval, passes new files to the parser, and inserts parsed records into
the database.

**Verify:** `python -m pytest tests/test_scheduler.py -v` passes 2 tests:
`test_new_file_triggers_parse`, `test_already_processed_file_skipped`.
`ls src/scheduler.py` exits 0.
```

- [ ] **Step 3: Create plan-partial-verification.md**

```markdown
# Data Ingestion Pipeline — Implementation Plan

**Goal:** Set up a pipeline that reads CSV files from a source directory,
parses them, and loads records into a database on a schedule.

---

### Task 1: Create pipeline configuration

Create `config/pipeline.yaml` with source directory, database connection
string, polling interval, and file pattern settings.

**Verify:** `cat config/pipeline.yaml | grep source_dir` returns a non-empty
value. `python -c "import yaml; yaml.safe_load(open('config/pipeline.yaml'))"` exits 0.

### Task 2: Write CSV parser module

Create `src/parser.py` that reads CSV files matching the configured pattern,
validates column headers against an expected schema, and returns a list of
record dictionaries.

### Task 3: Set up scheduled ingestion

Create `src/scheduler.py` that polls the source directory at the configured
interval, passes new files to the parser, and inserts parsed records into
the database.

**Verify:** `python -m pytest tests/test_scheduler.py -v` passes 2 tests:
`test_new_file_triggers_parse`, `test_already_processed_file_skipped`.
```

- [ ] **Step 4: Create plan-vague-verification.md**

```markdown
# Data Ingestion Pipeline — Implementation Plan

**Goal:** Set up a pipeline that reads CSV files from a source directory,
parses them, and loads records into a database on a schedule.

---

### Task 1: Create pipeline configuration

Create `config/pipeline.yaml` with source directory, database connection
string, polling interval, and file pattern settings.

**Verify:** Review the config file and make sure it looks correct.

### Task 2: Write CSV parser module

Create `src/parser.py` that reads CSV files matching the configured pattern,
validates column headers against an expected schema, and returns a list of
record dictionaries.

**Verify:** Run tests and verify it works.

### Task 3: Set up scheduled ingestion

Create `src/scheduler.py` that polls the source directory at the configured
interval, passes new files to the parser, and inserts parsed records into
the database.

**Verify:** Check that the scheduler runs properly.
```

- [ ] **Step 5: Verify all 4 fixtures exist**

Run: `ls tests/fixtures/`
Expected: 4 files — `plan-no-verification.md`, `plan-with-verification.md`, `plan-partial-verification.md`, `plan-vague-verification.md`

- [ ] **Step 6: Commit**

```bash
git add tests/fixtures/
git commit -m "Add eval fixture plans for verification criteria testing"
```

---

### Task 3: Create expected files and run-eval.md

**Files:**
- Create: `tests/expected/plan-no-verification.expected`
- Create: `tests/expected/plan-with-verification.expected`
- Create: `tests/expected/plan-partial-verification.expected`
- Create: `tests/expected/plan-vague-verification.expected`
- Create: `tests/run-eval.md`

- [ ] **Step 1: Create plan-no-verification.expected**

```
Fixture: plan-no-verification.md
Expected flags:
- Task 1 (Create pipeline configuration): 🔴 Critical — no verification method
- Task 2 (Write CSV parser module): 🔴 Critical — no verification method
- Task 3 (Set up scheduled ingestion): 🔴 Critical — no verification method
```

- [ ] **Step 2: Create plan-with-verification.expected**

```
Fixture: plan-with-verification.md
Expected flags: none (all tasks have concrete, falsifiable verification)
```

- [ ] **Step 3: Create plan-partial-verification.expected**

```
Fixture: plan-partial-verification.md
Expected flags:
- Task 2 (Write CSV parser module): 🔴 Critical — no verification method
```

- [ ] **Step 4: Create plan-vague-verification.expected**

```
Fixture: plan-vague-verification.md
Expected flags:
- Task 1 (Create pipeline configuration): 🟡 Significant — verification is unfalsifiable ("looks correct" cannot fail)
- Task 2 (Write CSV parser module): 🟡 Significant — verification is vague ("run tests and verify it works" lacks specifics)
- Task 3 (Set up scheduled ingestion): 🟡 Significant — verification is unfalsifiable ("runs properly" cannot fail)
```

- [ ] **Step 5: Create run-eval.md**

```markdown
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
```

- [ ] **Step 6: Verify all files exist**

Run: `ls tests/expected/`
Expected: 4 files — matching the fixture names with `.expected` extension

Run: `ls tests/run-eval.md`
Expected: file exists

Run: `find tests/ -type f | wc -l`
Expected: 9

- [ ] **Step 7: Commit**

```bash
git add tests/
git commit -m "Add expected files and run-eval.md for harden eval harness"
```

---

## Final Verification

- [ ] **Run the eval** — In the same worktree where SKILL.md was updated,
  follow `tests/run-eval.md` instructions. All 4 fixtures should match
  their expected files.

- [ ] **Verify SKILL.md line count** — `wc -l SKILL.md` shows under 250.

- [ ] **Push to main**

```bash
git pull --rebase origin main && git push origin HEAD:main
```
