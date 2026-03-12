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
