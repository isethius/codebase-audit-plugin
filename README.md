# Codebase Audit Plugin

A Claude Code plugin for comprehensive codebase audits.

## Prerequisites

Install the cartographer dependency first:
```
/plugins add cartographer-marketplace/cartographer
```

## Installation

1. Add this private marketplace:
   ```
   /plugins marketplace add isethius/codebase-audit-plugin
   ```

2. Install the plugin:
   ```
   /plugins install codebase-audit
   ```

## Usage

Run `/codebase-audit` in any project to get a full architecture review.

## Features

- Comprehensive code review and debugging
- Architecture analysis
- Security audit (OWASP-aligned)
- Performance analysis
- Maintainability assessment
- Generates `docs/ARCH_REVIEW.md` and `docs/FLOWS.mmd`

## What It Does

1. **Context Discovery** - Reads CLAUDE.md, AGENTS.md, docs, and auto-invokes cartographer if needed
2. **Repo Mapping** - Identifies components, routes, models, services, workers, middleware
3. **Bug Finding** - Runs tests, traces root causes, provides patches
4. **Security Audit** - Checks for injection, XSS, secrets, auth issues, and more
5. **Architecture Review** - Evaluates idempotency, scaling, rate limiting, failure recovery
6. **Performance Analysis** - Identifies hotspots with Big-O analysis
7. **Maintainability** - Coverage gaps, complexity hotspots, code smells

## Output

Generates two artifacts:
- `docs/ARCH_REVIEW.md` - Full report with risk register and prioritized backlog (25+ items)
- `docs/FLOWS.mmd` - Mermaid diagram of system architecture
