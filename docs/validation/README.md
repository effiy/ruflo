---
title: "Validation System — Three-Layer Regression Protection"
tags: ["validation", "regression", "smoke-tests", "witness", "ci", "ed25519", "markers"]
category: "docs/validation"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# Validation System

Three-layer regression-protection stack used by ruflo's CI to catch the regression classes that traditional unit tests miss. Adopt the same stack in any project — the toolkit is project-agnostic and ships in `plugins/ruflo-core/scripts/witness/`.

> **External-friendly version** (no ruflo-specific paths): [agentic-validation-system gist](https://gist.github.com/ruvnet/ee7763c36f7a9a1c1886da783abc872b). This document is the in-repo home with cross-references to ruflo's own implementation.

---

## 💡 概述

Three regressions filed on 2026-05-08 (#1859, #1862, #1867) all passed unit tests + typecheck on the broken commits. Each had a different root cause but the same gap: **unit tests verify code paths, not user-visible failure modes.**

| Regression | What broke | Why CI passed | What user saw |
|---|---|---|---|
| `#1867` | `@claude-flow/memory` had `better-sqlite3` as a hard dep + static import | CI ran on Node 20 where prebuilds existed, so the static import evaluated fine | `npm install ruflo@latest` failed on Node 26 with `node-gyp` errors |
| `#1862` | `ruflo-core` plugin's `hooks.json` called `--format true` (not a real flag) | No CI test invoked the plugin's `hooks.json` against the CLI with realistic stdin | Every Write/Edit tool use printed `[ERROR] Invalid value for --format: true` |
| `#1859` | CLI parser preferred stray positionals over named flags (14 sites in `hooks.ts`) | Unit tests passed flags individually, never combined `--flag` + boolean-shaped value | `post-edit --file X --success true` recorded `"true"` as the file path |

The validation stack adds three layers that each test a *user-visible* failure mode against a real artifact, not a code path.

---

## ⚡ 功能

### Three-Layer Architecture

```
                 ┌─────────────────────────────────────────────────┐
                 │  Layer 1: Behavioral smoke tests                │
                 │  ─────────────────────────────                  │
                 │  Fresh `npm install` on real Node versions      │
                 │  Real subprocess invocation with real JSON      │
                 │  Asserts user-visible signal, not code path     │
                 └─────────────────────────────────────────────────┘
                                       ↓
                 ┌─────────────────────────────────────────────────┐
                 │  Layer 2: Cryptographic witness manifest        │
                 │  ─────────────────────────────                  │
                 │  SHA-256 + marker substring per fix             │
                 │  Ed25519-signed with deterministic seed         │
                 │  Anyone can re-derive the public key            │
                 └─────────────────────────────────────────────────┘
                                       ↓
                 ┌─────────────────────────────────────────────────┐
                 │  Layer 3: Append-only temporal history (JSONL)  │
                 │  ─────────────────────────────                  │
                 │  One snapshot per regen                         │
                 │  Per-fix status timeline                        │
                 │  Regression-introduction commit identification  │
                 └─────────────────────────────────────────────────┘
```

- **Layer 1** catches the regression *as a user would experience it*.
- **Layer 2** confirms every documented fix is *still in the code*, even if Layer 1 has no specific test for it.
- **Layer 3** answers *when the regression was introduced*, so triage doesn't require manual `git bisect`.

---

## 📊 指标

### Capabilities matrix

| Failure class | Layer | Example |
|---|---|---|
| Install fails on platform without prebuilds | Layer 1 (install smoke) | `npm install` errors out during native build |
| Wrong CLI flag handling, parser ambiguity | Layer 1 (subprocess smoke) | `--flag value` records the wrong value |
| Plugin calls flag the CLI doesn't have | Layer 1 (subprocess smoke) | Hook prints `Invalid value for --format: true` |
| Documented fix silently removed | Layer 2 (witness markers) | Refactor deletes the load-bearing line, code still compiles |
| Fix regressed: which commit? | Layer 3 (history) | `git bisect` reduced to 3 commits in 18-hour window |
| Marker too brittle, flaps pass↔regressed | Layer 3 (history) | Status timeline shows oscillation |

---

## 🏛️ 设计

### Layer 1 — Behavioral smoke tests

Build the artifact under test in CI, drive it through the *user-visible* failure path with a real subprocess, assert on the user-visible signal.

| Instance | Source | CI job |
|---|---|---|
| Install smoke | `v3/@claude-flow/memory/scripts/smoke-no-bsqlite.mjs` | `smoke-install-no-bsqlite` (`v3-ci.yml`) |
| Hook smoke | `plugins/ruflo-core/scripts/test-hooks.mjs` | `plugin-hooks-smoke` (`v3-ci.yml`) |

See **ADR-102** (`v3/docs/adr/ADR-102-plugin-hook-cli-flag-regression-ci-guard.md`) for the full smoke-harness design.

### Layer 2 — Cryptographic witness manifest

Every documented fix gets an entry with SHA-256 + marker substring. Signed via Ed25519 with deterministic seed from git commit.

- **Pass** — file's SHA-256 matches
- **Drift** — SHA-256 changed but marker present (acceptable)
- **Regressed** — marker missing (real regression)
- **Missing** — file no longer exists

### Layer 3 — Append-only temporal history

Every regen appends one line to `verification-history.jsonl`. Queries answer *when* a regression was introduced, *what* fixes flapped, *which* fixes are drifting.

See **ADR-103** (`v3/docs/adr/ADR-103-witness-temporal-history.md`) for the full design.

### CI Integration Pitfalls

1. **pnpm isolated linker hides `@noble/ed25519`** — verify.mjs probes must include workspace package paths
2. **Don't dogfood the CLI in CI's witness-verify step** — use standalone verify.mjs
3. **`npm pack` chokes on `workspace:*` deps** — use `pnpm pack` instead
4. **Always print verify output** — never trust silent exit codes

---

## 📁 目录

「待补充」

---

## 🔗 资源

- [verification.md](../../verification.md) — the witness manifest itself + how-to-verify
- [verification.md.json](../../verification.md.json) — current signed manifest (82 fixes)
- [verification-history.jsonl](../../verification-history.jsonl) — temporal log
- [witness-fixes.json](../../witness-fixes.json) — ruflo's project-specific NEW_FIXES
- [ADR-102](../../v3/docs/adr/ADR-102-plugin-hook-cli-flag-regression-ci-guard.md)
- [ADR-103](../../v3/docs/adr/ADR-103-witness-temporal-history.md)
- [Public gist](https://gist.github.com/ruvnet/ee7763c36f7a9a1c1886da783abc872b)

---

## 🚀 快速开始

### Bootstrap (any project)

```bash
node plugins/ruflo-core/scripts/witness/init.mjs --root .
npm i @noble/ed25519
node plugins/ruflo-core/scripts/witness/regen.mjs \
  --manifest verification.md.json \
  --history  verification-history.jsonl \
  --fixes    witness-fixes.json
```

### Register a fix

1. Identify a distinctive marker substring
2. Append `{ id, desc, file, marker }` to `witness-fixes.json`
3. Run `regen.mjs --dry-run` to confirm `verified: N/N`
4. Run without `--dry-run` to write + append
5. Commit all three files together

### Investigate a regression

```bash
node plugins/ruflo-core/scripts/witness/history.mjs \
  --history verification-history.jsonl regressions

git log lastPassCommit..regressedAtCommit -- $(jq -r '.manifest.fixes[] | select(.id == "F12") | .file' verification.md.json)
```

---

## ⚙️ 技术栈

- `@noble/ed25519` (~15KB minified)
- SHA-256 hashing
- Ed25519 signatures (deterministic key derivation)
- JSONL temporal history
- pnpm (workspace builds)