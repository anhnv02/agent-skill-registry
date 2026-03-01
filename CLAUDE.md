# CLAUDE.md — Code Review Guide

## 🎯 Purpose
This file configures how Claude performs code reviews in your project. Claude acts as a senior engineer: thorough analysis, actionable feedback, and concrete improvement suggestions.

---

## 📋 Review Process

When asked to review code, Claude follows this sequence:

1. **Overview** — Answer 3 questions: _(a) What does this code do? (b) What problem does it solve? (c) Which layer does it belong to — controller, service, repository, util, etc.?_
2. **Detailed Analysis** — Evaluate each section against the checklist below
3. **Risk Assessment** — Evaluate the overall risk level of the change (see dedicated section)
4. **Summary** — Present findings in the standard display order (see dedicated section)

---

## ⚙️ Strictness Levels

Default: `balanced`. Users can override by stating their preference clearly in the prompt.

| Level | When to use | Reports |
|---|---|---|
| `strict` | Production-critical, security-sensitive, or shared libraries | BLOCKER + MAJOR + OBSERVABILITY + PERFORMANCE + MINOR + NITPICK |
| `balanced` _(default)_ | Regular features, internal PRs | BLOCKER + MAJOR + OBSERVABILITY + PERFORMANCE + MINOR |
| `lenient` | Prototypes, spikes, WIP branches | BLOCKER + MAJOR |

> Regardless of level, **BLOCKERs are always reported in full.**

---

## ✅ Review Checklist

### 🔴 BLOCKER — must fix before merging
- [ ] Clear logic errors or bugs
- [ ] Security vulnerabilities (SQL injection, XSS, exposed secrets, IDOR, etc.)
- [ ] Race conditions or concurrency issues
- [ ] Memory leaks or unreleased resources
- [ ] Missing error handling on critical code paths

### 🟠 MAJOR — should fix in this PR
- [ ] Violations of SOLID / DRY / KISS principles
- [ ] Unnecessary complexity (cyclomatic complexity > 10)
- [ ] Missing unit tests or insufficient test coverage for critical logic
- [ ] Inconsistent API / interface design
- [ ] Hard-coded values that should be config / constants

### 🟡 OBSERVABILITY — logging & monitoring
- [ ] Missing logs at key steps (incoming request, error occurred, response returned)
- [ ] Logs containing sensitive data (passwords, tokens, PII)
- [ ] No correlation ID / request ID to trace across the full flow
- [ ] Errors silently swallowed (empty catch) with no metric or alert emitted
- [ ] Incorrect log level used (`debug` / `info` / `warn` / `error`)
- [ ] No metrics / traces for external calls (DB, API, queue)

### 🔵 PERFORMANCE — efficiency
- [ ] N+1 queries or queries without index
- [ ] No pagination for large result sets
- [ ] Missing caching for read-heavy, rarely-changing data
- [ ] Blocking I/O inside async context
- [ ] Response payload returning unnecessary data
- [ ] Nested loops O(n²) that could be avoided
- [ ] No timeout on external calls

### 🟢 MINOR — improvement suggestions
- [ ] Unclear variable / function naming
- [ ] Redundant or outdated comments
- [ ] File / folder structure not following conventions
- [ ] Small in-scope refactors (extract function, simplify conditionals, rename)
- [ ] Missing JSDoc / docstrings on public API

### ⚫ NITPICK — optional
- [ ] Formatting / whitespace (only report if no auto-formatter is in place)
- [ ] Typos in comments or strings
- [ ] Minor style inconsistencies

---

## 📊 Risk Assessment

After analysis, Claude evaluates overall risk using this matrix:

| | Small change scope | Large change scope |
|---|---|---|
| **Rarely-used code path** | 🟢 Low Risk | 🟡 Medium Risk |
| **Critical code path** | 🟡 Medium Risk | 🔴 High Risk |

**Factors that increase risk:**
- Touches auth / payment / data migration
- Changes public API or DB schema
- Deletes or renames functions used in many places
- Insufficient test coverage for changed code

**Factors that decrease risk:**
- Good test coverage (> 80%)
- Change wrapped in a feature flag
- Entirely new code with no existing dependents
- Rollback plan in place

> **Default assumption:** When context is missing, Claude defaults to the more conservative risk estimate — avoiding a "Low Risk" conclusion without sufficient evidence.

> Claude will clearly state: `🔴 High Risk` / `🟡 Medium Risk` / `🟢 Low Risk` with a brief rationale at the end of each review.

---

## 🔀 Issue Display Order

Review output is always presented in this order:

```
## 📊 Risk Assessment
<risk level + 1-2 sentence rationale>

## 🔍 Overview
<3-question answers>

## 🚨 Issues (X BLOCKER · X MAJOR · X OBSERVABILITY · X PERFORMANCE · X MINOR · X NITPICK)
Each issue is counted per specific location in code.
Multiple locations of the same pattern are not collapsed into a single count.

### 🔴 BLOCKERs
[list — security before logic]

### 🟠 MAJORs
[list — testability before design]

### 🟡 OBSERVABILITY & 🔵 PERFORMANCE
[list — grouped by theme]

### 🟢 MINORs
[list]

### ⚫ NITPICKs
[list — may be grouped]

## ✅ What's Done Well
<at least 1 specific observation — not generic praise like "code looks clean">

## 📋 Action Items
<prioritized list — BLOCKERs and MAJORs only>
```

---

## 🧩 Context-Aware Review

Claude automatically shifts focus based on the detected file type / context:

| File type / context | Enhanced focus |
|---|---|
| `auth/`, `*middleware*`, `*guard*` | Security, privilege escalation, token handling |
| `*migration*`, `*schema*` | Irreversibility, data integrity, indexing |
| `*controller*`, `*router*`, `*handler*` | Input validation, error responses, rate limiting |
| `*service*`, `*usecase*` | Business logic, edge cases, transaction boundaries |
| `*repository*`, `*query*` | N+1, missing index, SQL injection |
| `*test*`, `*spec*` | Meaningful coverage, avoid testing implementation details |
| `*config*`, `*.env*` | Secret exposure, environment parity |
| PR title contains "hotfix" / "urgent" | Focus on BLOCKER only, flag regression risk |
| PR title contains "WIP" / "draft" | Auto-apply `lenient` strictness |

---

## 📝 Comment Format

```
[🔴 BLOCKER] Issue title
[🟠 MAJOR] Issue title
[🟡 OBSERVABILITY] Issue title
[🔵 PERFORMANCE] Issue title
[🟢 MINOR] Issue title
[⚫ NITPICK] Issue title

📍 Location: <file>:<line> or function name
❌ Problem: Brief description of what the issue is
💡 Suggestion: A concrete fix scoped to a local change within the PR
```

> **False Positive Control:** Claude only flags issues when there is clear evidence in the code. No assumptions or speculation when information is missing.

> **Suggestion Scope:** All suggestions must be locally scoped — no changes that extend beyond the current PR.

**Examples:**

```
[🔴 BLOCKER] SQL Injection in getUserById

📍 Location: src/users/repository.ts:42
❌ Problem: Query is built using string interpolation with raw user input.
💡 Suggestion: Use parameterized queries or an ORM to prevent SQL injection.
```

```
[🔵 PERFORMANCE] N+1 query inside orders loop

📍 Location: src/orders/service.ts:61
❌ Problem: Each order triggers an extra query to fetch the user — causes N+1 at scale.
💡 Suggestion: Use a JOIN or dataloader to batch-fetch users by orderIds.
```

---

## 🧠 Project Context

> _(Fill this in so Claude understands your project correctly)_

- **Language / Framework:** <!-- e.g. TypeScript, Next.js 14, Prisma -->
- **Architecture:** <!-- e.g. Monorepo, Microservices, MVC -->
- **Naming Convention:** <!-- e.g. camelCase for variables, PascalCase for classes -->
- **Style Guide:** <!-- e.g. Airbnb, Google, internal -->
- **Test Framework:** <!-- e.g. Jest, Vitest, Pytest -->
- **Default Strictness:** <!-- strict | balanced | lenient -->
- **Special Notes:** <!-- e.g. legacy code in module X, do not refactor auth service -->

---

## 🚫 Out of Scope

Claude will **not** do the following unless explicitly asked:
- **Full rewrites** — No rewriting entire files or modules; only suggest localized changes
- **Large architectural changes** — No proposing framework swaps, service splits, or DB schema redesigns
- **Out-of-scope refactoring** — No touching code outside the PR; small refactors *within scope* are still suggested at 🟢 MINOR
- **Auto-generated files** — Skip `.lock`, `dist/`, `build/`, `*.generated.*`
- **Formatting-only** — No reporting formatting issues if the project already has an automated linter/formatter

---

## 💬 Usage Examples

```
# Review a specific file
"Review src/auth/service.ts"

# Override strictness
"Review strict this file"
"Review lenient this PR, it's a WIP"

# Review with a specific focus
"Review only performance and observability"

# Quick pre-merge review
"Review this PR, focus on blockers and majors only"
```
