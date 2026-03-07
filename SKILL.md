---
name: harden
description: >
  Audits any substantial deliverable — documents, grant sections,
  strategies, protocols, skills, configs, roadmaps, plans — against
  domain-specific quality criteria before delivery. Searches the web
  for best practices, runs dual-pass thematic and structural audit with
  severity classification, fixes all issues, and iterates until
  convergence. Activates when producing deliverables exceeding 4
  paragraphs or 20 lines, or when the user says "harden", "audit",
  "stress test", or "review before delivering", or when re-auditing
  a modified deliverable for regressions. Does not activate for
  conversational responses, brainstorming, web search summaries, or
  rough drafts.
compatibility: >
  claude.ai, Claude Code, Codex CLI, Gemini CLI (requires web search
  for research step; degrades gracefully without it)
---

# Harden

> **MANDATORY:** Re-read this entire file before every audit pass. Do not rely on earlier readings within the same conversation. Context window drift causes skipped steps.

## Purpose

Ensures substantial deliverables are structurally sound, internally consistent,
and aligned with domain best practices before delivery. Replaces ad hoc quality
checks with a repeatable methodology.

## How It Works

1. **Research** — Searches the web for domain-specific best practices
   before auditing (not just internal assumptions)
2. **Dual-pass audit** — Checks both ideas (are they sound?) and
   structure (do paths/references/formatting work?)
3. **Severity classification** — 🔴 Critical / 🟡 Significant / 🟢 Minor
   with specific locations
4. **Fix** — Implements all critical and significant fixes (not just reports)
5. **Converge** — Re-audits until only minor issues remain (max 3 passes)

## The Process

### Step 1: Research

Search for current best practices specific to the deliverable's domain.

**Scope guidance:**
- Minimum 2 searches for focused domains (e.g., a skill file -> Anthropic
  skill docs)
- 3-5 searches for broad or unfamiliar domains (e.g., a grant aims page ->
  NIH guidelines, successful examples, common reviewer complaints)
- Always check for official/canonical sources first, then practitioner insights
- **Fetch the canonical source.** If search results include the single most
  authoritative document for this domain (official guide, spec, standard),
  fetch and read it — do not rely on search snippets. Snippets capture ~5%
  of a document's content and miss testing, edge cases, and patterns.
  Limit to the first ~5000 tokens if the source is very long; prioritize
  sections on requirements, validation, and common mistakes.
- Synthesize findings into actionable bullets that inform the audit — not a
  reading list

**If web search is unavailable:** State this limitation explicitly. Proceed
using training knowledge but flag that the audit lacks external validation.

### Step 2: Comparative Audit

Run TWO distinct passes within this step. Both are mandatory.

**Pass A — Thematic audit (ideas level):**
- Does the deliverable achieve its stated purpose?
- Are the ideas sound, complete, and non-contradictory?
- Does it align with research findings from Step 1?
- Are there gaps, unstated assumptions, or logical errors?
- Would the target audience (reviewer, agent, collaborator) find it clear?

**Pass B — Structural audit (artifact level):**
- Do all paths, filenames, and references resolve?
- Are names, terms, and conventions consistent throughout?
- Do cross-references point to real sections/files?
- Is formatting correct for the target format (markdown, YAML, code)?
- Are there orphaned sections, redundant content, or dangling references?
- If executable (a plan, a config): would a literal reader succeed?

**Pass C — Regression check (only when re-hardening a modified deliverable):**

Trigger detection: Apply Pass C when the deliverable was previously completed
or delivered and is now being revised — indicated by the user referencing a
prior version, requesting modifications to existing work, or re-submitting
after changes. Examples: "update the roadmap I wrote earlier", "fix the issues
you found", or any edit to an artifact that was previously declared done.

- What previously worked that this modification could break?
- Are existing cross-references still valid after the change?
- Do acceptance criteria from the original still hold?
- Has scope crept beyond the intended modification?

**Severity classification for every issue found:**
- 🔴 **Critical** — Will cause failure, incorrect behavior, or fundamental
  misunderstanding. Must fix.
- 🟡 **Significant** — Degrades quality, violates best practices, causes
  confusion, or wastes resources (tokens, time). Must fix.
- 🟢 **Minor** — Cosmetic, low-impact, stylistic. Fix if easy, skip if not.

**Required output structure:**
1. **What passes** — Sections/aspects reviewed and confirmed correct. This
   proves the audit actually examined these areas rather than skipping them.
2. **What's missing** — Gaps, absent sections, unstated assumptions.
3. **What's wrong** — Errors, inconsistencies, violations. Each with severity
   and specific location (line number, section name, or quote).
4. **Prioritized fix list** — All issues ordered 🔴 → 🟡 → 🟢 with specific
   remediation for each.

### Step 3: Update

Execute all 🔴 and 🟡 fixes. Do not cherry-pick. Do not defer significant
issues. Fix 🟢 issues if they're adjacent to other changes.

After fixes, run a mechanical verification:
- If the deliverable contains paths: grep/check all paths resolve
- If it contains names/terms: verify consistency
- If it references sections: confirm they exist
- If it has acceptance criteria: verify the artifact satisfies them

### Step 4: Diminishing Returns Check

Evaluate what this pass found:
- Any 🔴 critical issues? → Run another pass (return to Step 2)
- Any structural 🟡 issues? → Run another pass (return to Step 2)
- Only 🟢 minor issues? → Stop. Deliver.

**Limits:** 2 passes is typical. 3 is maximum. If still finding critical
issues after 3 passes, flag to the user that the deliverable may need
fundamental restructuring rather than incremental fixes.

**Re-hardening trigger:** When external standards change (new Anthropic docs,
updated NIH guidelines, framework version bumps), previously hardened
deliverables should be re-audited against the new standards. The user triggers
this manually; the skill treats it as a fresh hardening with Pass C enabled.

## Domain Quality Criteria

When auditing, identify the relevant quality criteria for the deliverable type.
For domains not listed below, generate criteria by asking: "What would an
expert reviewer check before approving this?"

### Skills (Claude Code / Claude Chat)
- Frontmatter: name (lowercase, hyphens, <=64 chars; gerund form is
  conventional but not required), description (third person, non-empty,
  all triggers included; Anthropic Help Center states <=200 chars,
  ecosystem convention <=1024 chars — test auto-invocation if exceeding
  200)
- If the skill has environment dependencies (web search, filesystem, MCP,
  specific platform), declare them in the `compatibility` frontmatter field
  AND mention the primary dependency in the description
- Body under 500 lines, context-efficient (no content Claude already knows)
- Progressive disclosure where applicable
- Trigger validation: ask Claude "when would you use [skill name]?" and
  compare its answer against intended triggers. Check for under-triggering
  (missing expected phrases) and over-triggering (matching unrelated queries)
- Instructions are specific and actionable, not buried or ambiguous; critical
  steps appear early in the document

### Setup/Configuration Documents
- All paths consistent and valid, instructions imperative
- Acceptance criteria explicit and mechanically verifiable
- Self-documenting with graceful fallback for missing prerequisites

### Grant Sections
- Significance (clear gap), innovation (distinct from prior art),
  approach (feasible, risks mitigated), alignment (budget matches scope)
- Reviewer-friendly: scannable, no undefined jargon

### Strategic Plans / Roadmaps
- Outcomes measurable, dependencies explicit, timeline realistic
- Risks identified with mitigations, prioritization rationale stated

### Protocols / SOPs
- Steps unambiguous and executable by stated audience
- Materials specified with versions, decision points identified
- Expected results stated for verification

### Implementation Plans / Execution Runbooks
- Every summary list (e.g., "Bootstrapping Steps") must have 1:1
  correspondence with its detailed execution section. No step in one
  without a match in the other.
- For each step, verify preconditions are satisfied by prior steps.
  Flag: config created before its target exists, files referenced before
  creation, tools invoked before dependencies are available.
- Every CLI command, slash command, skill name, and tool referenced must
  actually exist. Verify against the known environment — not assumptions
  or recall. Flag speculative or hallucinated commands.
- Hook/automation rules must be achievable with their mechanism type
  (command hooks = grep/regex, prompt hooks = LLM yes/no, agent hooks =
  multi-turn). Flag rules requiring capabilities beyond their type.

## Anti-Patterns

**Performative skepticism:** Adding self-critical commentary ("you might want
to strengthen this") without running the actual audit process. Sounds rigorous,
catches nothing structural.

**Thematic-only auditing:** Checking that ideas are sound while ignoring paths,
names, formatting, and consistency. How 14 issues survived a "validated"
deliverable in prior experience.

**Structural-only auditing:** The inverse — checking paths and formatting while
missing that the ideas are wrong or incomplete.

**Severity inflation:** Marking everything 🔴 to appear thorough. Reserve
critical for actual failure modes.

**Audit without research:** Skipping Step 1 and auditing against internal
assumptions rather than external standards. The research step imports criteria
that wouldn't be generated otherwise.

**Snippet-depth research:** Finding the canonical source but reading only
search result excerpts instead of fetching the full document. Captures
technical requirements but misses testing methodology, edge cases, and
behavioral patterns. How 4 relevant quality criteria were missed despite
the source appearing twice in search results.

**Skipping "what passes":** Only reporting failures without confirming what
was reviewed and found correct. Hides gaps in audit coverage.

**Static-only plan auditing:** Checking that references resolve and formatting
is correct while treating a sequential plan as a static document. Plans are
executable — each step has preconditions. Auditing a plan without simulating
execution order misses ordering violations, summary↔detail drift, and
nonexistent tool references. How 7 issues (4 moderate) survived 4 audit passes
on an implementation plan.
