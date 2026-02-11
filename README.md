# Codebase Audit Plugin

A Claude Code plugin for comprehensive codebase audits — code review, debugging, architecture analysis, security audit, and performance profiling in one command.

## Prerequisites

This plugin depends on [cartographer](https://github.com/kingbootoshi/cartographer) for codebase mapping. Install it first:

```
/plugins marketplace add kingbootoshi/cartographer
/plugins install cartographer
```

## Installation

1. Add the marketplace:
   ```
   /plugins marketplace add isethius/codebase-audit-plugin
   ```

2. Install the plugin:
   ```
   /plugins install codebase-audit
   ```

## Usage

Run `/codebase-audit` in any project to get a full architecture review.

You can also trigger it naturally by saying things like:
- "review this codebase"
- "security audit"
- "find bugs"
- "architecture review"
- "what's wrong with this code"

## What It Does

The audit runs in 7 phases:

1. **Context Discovery** — Reads CLAUDE.md, AGENTS.md, docs, and auto-invokes cartographer if no codebase map exists (or if it's stale)
2. **Repo Mapping** — Identifies components: routes, models, services, workers, middleware, integrations
3. **Bug Finding** — Runs tests, traces root causes, provides minimal patches
4. **Security Audit** — OWASP-aligned checks: injection, XSS, secrets, auth, crypto, SSRF, and more
5. **Architecture Review** — Evaluates idempotency, scaling, concurrency, rate limiting, failure recovery
6. **Performance Analysis** — Identifies top 5 hotspots with Big-O analysis and benchmarks
7. **Maintainability** — Coverage gaps, complexity hotspots, code smells, documentation gaps

## Output

Generates two artifacts in the target project:

- `docs/ARCH_REVIEW.md` — Full report with executive summary, risk register, and prioritized backlog (25+ items)
- `docs/FLOWS.mmd` — Mermaid diagram of system architecture and data flows

## Cartographer Integration

This plugin automatically invokes cartographer when needed:
- If `docs/CODEBASE_MAP.md` doesn't exist, cartographer runs first
- If the map is stale (>20 commits old), cartographer refreshes it
- Skip with "quick audit" or "skip mapping" if you want to bypass this

## License

MIT
