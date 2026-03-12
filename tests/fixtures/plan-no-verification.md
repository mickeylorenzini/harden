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
