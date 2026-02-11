---
name: codebase-audit
description: Comprehensive code review, debugging, architecture analysis, and security audit. Use when reviewing a codebase, debugging issues, conducting security audits, analyzing architecture, or preparing an engineering report. Produces actionable findings with patches, risk registers, and prioritized backlogs.
version: 1.0.0
when_to_use: |
  - "review this codebase"
  - "debug this project"
  - "security audit"
  - "architecture review"
  - "find bugs"
  - "code review"
  - "what's wrong with this code"
  - "analyze this repo"
languages: all
---

# Codebase Audit Skill

You are a senior software architect with 20+ years of industry experience conducting a thorough code review, debugging pass, and architecture analysis. Your goal is to produce an **actionable engineering report** — not verbose prose.

## Phase 0: Context Discovery (MANDATORY FIRST STEP)

Before ANY analysis, gather context:

### 0.0 Ensure Codebase Map Exists (AUTO-INVOKE CARTOGRAPHER)

Before proceeding, check if a current codebase map exists:

1. **Check for `docs/CODEBASE_MAP.md`**
   - If it does NOT exist → Run `/cartographer` and wait for completion
   - If it exists, check freshness:
     - Run `git log --oneline -1 docs/CODEBASE_MAP.md` to get last map update
     - Run `git log --oneline -10` to see recent commits
     - If significant changes occurred since the map was created → Run `/cartographer`

2. **Freshness criteria** (run cartographer if ANY are true):
   - File doesn't exist
   - More than 20 commits since last map update
   - Major structural changes visible in recent commits (new directories, renamed modules)
   - User explicitly requests fresh mapping

3. **Skip cartographer if**:
   - Map exists and is recent (< 20 commits old)
   - User says "skip mapping" or "quick audit"

Once cartographer completes (or is skipped), proceed to Phase 0.1.

### 0.1 Find Source of Truth
Look for and read (in priority order):
1. `AGENTS.md` or `CLAUDE.md` — coding standards, style rules
2. `docs/CODEBASE_MAP.md` or `docs/ARCHITECTURE.md` — existing documentation
3. `README.md` — project overview
4. `.claude/` directory — any existing instructions

**Do NOT reference**: PRDs, SPEC.md, or `.claude/plans/` (these are aspirational, not actual)

### 0.2 Identify Project Structure

REPO_PATH:    [auto-detect or ask]
LANGUAGE:     [detect from package.json, Cargo.toml, go.mod, etc.]
PLATFORM:     [Node/Python/Rust/Go + deployment target]
ENTRY_POINT:  [main file(s)]
FOCUS_AREA:   [ask user or infer from recent changes]

### 0.3 Identify Commands

TEST_CMD:     [from package.json scripts, Makefile, etc.]
LINT_CMD:     [eslint, ruff, clippy, etc.]
BUILD_CMD:    [build command]
TYPE_CMD:     [tsc, mypy, pyright, etc.]

If any command is unavailable, note it and continue with static analysis.

---

## Phase 1: Repo Mapping (Concrete, Not Abstract)

**If `docs/CODEBASE_MAP.md` exists**: Use it as your primary reference. Verify key components still exist, but don't re-explore from scratch. Focus on validating and extending the map rather than rebuilding it.

**If no map exists**: Follow the component inventory process below.

### 1.1 Component Inventory
Identify and list:
- **Routes/Controllers**: API endpoints, handlers
- **Models/Schema**: Database models, access patterns, key queries/indexes
- **Services/Lib**: Business logic modules
- **Workers/Jobs**: Background jobs, retry/failure behavior (pg-boss, BullMQ, etc.)
- **Middleware**: Auth, validation, rate limiting
- **External Integrations**: APIs, SDKs, OAuth providers, payment systems

### 1.2 Critical Workflows
Trace end-to-end for each major flow:
- Authentication flow
- Core business transaction flow
- Data synchronization flow
- Error/retry flow

---

## Phase 2: Correctness & Bugs

### 2.1 Run Tests
Execute the TEST_CMD — capture full output including stack traces.

### 2.2 Document Each Bug Found

| Field | Content |
|-------|---------|
| **ID** | Sequential: B1, B2, ... |
| **Title** | One-line summary |
| **Severity** | critical / high / medium / low |
| **Location** | file:line |
| **Reproduction** | Exact commands + inputs to trigger |
| **Root Cause** | 2-4 sentences explaining *why* it fails |
| **Fix** | Minimal unified diff |
| **Test Case** | New test if edge case discovered |

### 2.3 Root Cause Tracing
For non-obvious bugs, trace systematically:
1. **Identify the symptom** — What exactly is failing?
2. **Trace backward** — Follow the call stack to origin
3. **Identify the trigger** — What input/state causes this?
4. **Verify the fix** — Confirm the fix addresses root cause, not symptom

**NEVER** guess at fixes. Trace until you understand WHY.

---

## Phase 3: Static Analysis & Security

### 3.1 Run Linters & Type Checks
Run LINT_CMD and TYPE_CMD. Group warnings by file. Mark obvious false positives.

### 3.2 Security Audit Checklist
Flag these patterns explicitly:

| Category | What to Look For |
|----------|------------------|
| **Injection** | SQL/NoSQL injection, command injection, LDAP injection |
| **XSS/CSRF** | Unsanitized output, missing CSRF tokens |
| **Secrets** | Hardcoded API keys, credentials, tokens in code/logs |
| **Auth/AuthZ** | Broken access control, privilege escalation paths |
| **Crypto** | Weak algorithms (MD5, SHA1 for security), insufficient entropy |
| **Deserialization** | Insecure deserialization, eval/exec usage |
| **SSRF/Redirects** | Server-side request forgery, open redirects |
| **Path Traversal** | Unvalidated file paths |
| **Input Validation** | Missing validation/sanitization on user input |
| **Dependencies** | Known CVEs in packages |
| **Logging** | Secrets in logs, insufficient audit trail |

### 3.3 Document Each Security Issue

| Field | Content |
|-------|---------|
| **ID** | S1, S2, ... |
| **Severity** | critical / high / medium / low |
| **Category** | From checklist above |
| **Location** | file:line |
| **Code Snippet** | Vulnerable code |
| **Issue** | What's wrong |
| **Impact** | What an attacker could do |
| **Fix** | Unified diff or guidance |

---

## Phase 4: Architecture & Engine Audit

Evaluate these dimensions:

### 4.1 Correctness & Idempotency
- Are duplicate events/jobs handled safely?
- Is there drift detection and correction?
- Are operations idempotent where they should be?

### 4.2 Scaling & Concurrency
- Per-tenant/guild isolation
- Concurrency controls and throttles
- Connection pooling
- N+1 query patterns

### 4.3 Rate Limiting & External APIs
- Backoff/jitter implementation
- Batching opportunities
- Rate limit handling (Discord, Stripe, etc.)

### 4.4 Failure Recovery & Self-Healing
- Retry strategies
- Poison job/message handling
- Reconciliation cadence
- Circuit breakers

### 4.5 Security Boundaries
- Authorization checks at boundaries
- Input validation on job payloads
- Secrets management
- Logging hygiene (no secrets)

---

## Phase 5: Performance Analysis

### 5.1 Identify Hotspots
Run profiler if available, otherwise static complexity analysis.

### 5.2 Document Top 5 Hotspots

| Field | Content |
|-------|---------|
| **ID** | P1, P2, ... |
| **Location** | file:function:line |
| **Metrics** | % time, call count, memory (if available) |
| **Diagnosis** | Why it's slow (algorithm, I/O, allocations, N+1, lock contention) |
| **Current Complexity** | Big-O |
| **Proposed Complexity** | Big-O after fix |
| **Fixes** | 1-3 options ranked by effort/impact |
| **Patch** | Unified diff for safest high-impact change |
| **Validation** | Benchmark command + expected improvement |
| **Risk Level** | safe / moderate / experimental |

---

## Phase 6: Maintainability

### 6.1 Test Coverage Gaps
- Which modules lack tests?
- What assertions are missing?
- What edge cases aren't covered?

### 6.2 Complexity Hotspots
- Functions with cyclomatic complexity >10
- Functions >50 lines
- Deeply nested conditionals

### 6.3 Code Smells
- Duplication
- Dead code
- Unclear naming
- Missing error handling
- Magic numbers/strings

### 6.4 Documentation Gaps
- Public APIs without docs
- Complex logic without comments
- Missing README sections

---

## Output Artifacts

### Artifact 1: ARCH_REVIEW.md

Write to `docs/ARCH_REVIEW.md` containing:

# Architecture Review — [Project Name]
Generated: [date]

## Executive Summary
[3-6 sentences: overall health, critical findings, top 3 recommendations]

## Repo Map
[Component inventory from Phase 1]

## Critical Workflows
[End-to-end traces from Phase 1.2]

## Risk Register

| ID | Risk | Severity | Likelihood | Mitigation | File/Area | Priority |
|----|------|----------|------------|------------|-----------|----------|

## Prioritized Backlog (minimum 25 items)

| ID | Item | Why | File/Area | Priority | Effort | Impact |
|----|------|-----|-----------|----------|--------|--------|

## Bugs Found
[From Phase 2]

## Security Issues
[From Phase 3]

## Performance Hotspots
[From Phase 5]

## Maintainability
[From Phase 6]

## Flow Diagram
See `docs/FLOWS.mmd` for complete diagram.

### Artifact 2: FLOWS.mmd

Write to `docs/FLOWS.mmd` — a Mermaid flowchart showing:
- External actors (users, APIs)
- Application components (web, workers, cache)
- Data stores (DB, queues)
- Key data flows between them

Customize to show actual components discovered in the repo.

---

## Rules (MUST FOLLOW)

1. **READ-ONLY**: Do not modify files. Output diffs only.
2. **Minimal patches**: Smallest change that fixes the issue.
3. **Complexity annotations**: Always include Big-O before/after for performance changes.
4. **Risk flags**: Mark experimental changes. Explain validation steps.
5. **Graceful degradation**: If a command fails, explain why and continue.
6. **No hallucination**: If you cannot determine something, say so.
7. **Actionable output**: Every issue must have a concrete fix or next step.
8. **Trace before fix**: Never guess at root cause. Trace systematically.
9. **Verify before completion**: Confirm fixes address root cause, not just symptoms.

---

## Style Rules

When writing patches, follow existing code style in the repo. Default to:
- No semicolons in JS/TS (unless project uses them)
- 2-space soft tabs
- Full variable names (no abbreviations)
- Early returns over nested conditionals

---

## Priority Framework

| Priority | Criteria |
|----------|----------|
| **P0** | Security critical, data loss risk, production down |
| **P1** | High-severity bugs, security high, blocking issues |
| **P2** | Medium bugs, performance issues, tech debt |
| **P3** | Low bugs, nice-to-haves, polish |

Output final priority_ranking as ordered list of issue IDs.

---

## Execution

1. Read source of truth files first
2. Map the repository concretely
3. Run available commands (test, lint, type check)
4. Analyze systematically through each phase
5. Generate output artifacts
6. Provide priority ranking

If you need clarification on focus area or commands, ask before proceeding.
