---
name: github-release-management
description: "Standardized GitHub release workflow: audit, cleanup, full-repo doc sync, commit, tag, push, gh release, verify. Trigger on: release, publish version, bump version, tag, deploy new version. Enforces anti-bloat constraints and verification gates at every phase."
version: 1.1.0
---

# GitHub Release Management

## Trigger

Activate when user says: release, publish, bump version, new version, tag, deploy, version iteration, or any request to ship a new release.

## Prerequisites

Before starting, verify ALL of the following (stop and report if any fail):

```bash
git status                    # must be inside a git repo
gh auth status                # must be logged in
git remote -v                 # must have origin remote
```

## Flow Control

After Phase 1 audit, classify the release:
- **Hotfix path** (<=3 commits since last tag, only fixes): skip Phase 3 full-repo scan, only update CHANGELOG + version refs.
- **Full path** (features, breaking changes, minor/major): execute all phases.

## Phase 1: AUDIT

```bash
git status --porcelain
git log --oneline $(git describe --tags --abbrev=0 2>nul || echo "HEAD~20")..HEAD
git tag --sort=-v:refname | head -5
git fetch origin && git rev-list --left-right --count origin/HEAD...HEAD
```

Determine: unpushed commit count, latest tag, working tree state, CI status, local↔remote divergence.

**GATE 1:** Working tree must be clean (or user approves stash). CI must be green (or user acknowledges). If gate fails, STOP and report.

## Phase 2: CLEANUP

If uncommitted changes exist:
- Ask user: commit them or stash?
- Execute user's choice.

**GATE 2:** `git status --porcelain` outputs nothing.

## Phase 3: FULL REPO SYNC (full path only)

Execute each sub-step. Use `dir_path` parameter for all commands (never `cd /d`).

### 3a. Version reference consistency

```bash
grep -rn "version" --include="*.py" --include="*.toml" --include="*.cfg" --include="*.yml" --include="*.yaml" --include="*.json" . | grep -v ".git/" | grep -v "node_modules/" | grep -v "__pycache__/"
```

Update all version references to the new version (after Phase 4 confirms the number).

### 3b. README Chinese

- Compare each section against actual code/features.
- DELETE descriptions of removed/nonexistent features.
- ADD descriptions for new features since last release.
- Keep total <= 300 lines. If over, trim ruthlessly.

### 3c. README English

- Align paragraph-by-paragraph with Chinese README.
- Same content, same structure, same line budget.

### 3d. CHANGELOG.md

Add new version entry using this exact format:

```markdown
## [X.Y.Z] - YYYY-MM-DD

### Added
- one line per item

### Changed
- one line per item

### Fixed
- one line per item

### Removed
- one line per item
```

Rules: each entry <= 1 line. No paragraphs. No explanations. Omit empty sections.

### 3e. Other docs

Check DEPLOY.md, CONTRIBUTING.md, docs/ directory. Fix stale references. Delete empty/TODO-only files.

### 3f. Config and examples

Verify requirements.txt / pyproject.toml / ci.yml dependency versions are current. Fix stale paths or commands in example scripts.

### 3g. LICENSE

Confirm LICENSE file exists. Verify year and entity are correct. Default: BSL 1.1 (use whatever the repo already has).

### 3h. Anti-bloat scan

Apply ALL of the following (see Anti-Bloat Rules section below).

### 3i. Pre-release quality & privacy scan

```bash
# Code quality
grep -rn "TODO\|FIXME\|HACK\|XXX" --include="*.py" --include="*.js" --include="*.ts" . | grep -v ".git/" | grep -v "test"
grep -rn "print(\|console\.log" --include="*.py" --include="*.js" . | grep -v ".git/" | grep -v "test" | grep -v "__main__"
# Secrets
grep -rn "password\|api_key\|secret\|token" --include="*.py" --include="*.env" --include="*.yml" . | grep -v ".git/" | grep -v "example" | grep -v ":-"
# Privacy: hardcoded local paths (allow C:\Program Files for standard SDKs)
grep -rn --include="*.py" --include="*.yaml" --include="*.yml" -E "[A-Z]:\\\\" . | grep -v ".git/" | grep -v "Program Files" | grep -v "local_data_paths"
# Privacy: personal identifiers (emails, client/project names in non-doc files)
grep -rn --include="*.py" -E "@(gmail|qq|163|outlook)\.com" . | grep -v ".git/"
```

Severity classification:
- **blocking**: hardcoded secrets, credentials, client/customer names, user-specific disk paths (E:\, D:\project) in tracked files -> STOP release. Fix before proceeding.
- **important**: TODO/FIXME in changed files, debug prints, personal emails -> report to user, user decides.
- **nit**: TODO in unchanged files -> note but do not block.

Verify .gitignore covers: `.env`, `*.key`, `credentials*`, `*secret*`, `local_*_paths*`, `*.local.*`.
If CI exists, confirm it has a privacy/path lint step. If missing, flag as **important**.

**GATE 3:** `git diff --stat` shows only expected files changed. No accidental deletions. No blocking-severity findings unresolved.

## Phase 4: VERSION DECISION

Based on changes since last tag, recommend:
- **patch** (x.y.Z): bug fixes only, no new features
- **minor** (x.Y.0): new features, backward compatible
- **major** (X.0.0): breaking changes

Present recommendation with justification. **DO NOT proceed until user explicitly confirms the version number.**

**GATE 4:** User has stated the exact version string (e.g., "v1.2.0").

## Phase 5: COMMIT + TAG

```bash
git status --porcelain          # review what will be staged
git add <specific files>        # stage explicitly, never blind -A
git commit -m "release: vX.Y.Z"
git tag -a vX.Y.Z -m "Release vX.Y.Z"
```

**GATE 5:** `git log --oneline -1` shows the release commit. `git tag -l "vX.Y.Z"` returns the tag.

## Phase 6: PUSH + RELEASE

```bash
git push
git push --tags
```

Then create the GitHub release:

```bash
gh release create vX.Y.Z --title "vX.Y.Z" --notes "<notes>"
```

Release Notes structure (keep concise):

```markdown
## Highlights
- max 5 bullet points, one line each

## What's Changed
- grouped by Added/Changed/Fixed/Removed (mirror CHANGELOG)

## Breaking Changes
- only if any; describe migration step in one line
```

**GATE 6:** `gh release view vX.Y.Z` succeeds and shows correct tag + notes.

## Phase 7: VERIFY

```bash
git log origin/HEAD..HEAD --oneline   # must be empty
gh release view vX.Y.Z               # must show correct info
```

Confirm README version history section includes the new version.

**GATE 7:** All checks pass. Output summary: version, tag, release URL, files changed count.

## Anti-Bloat Rules

During Phase 3h, enforce ALL:

1. **Zombie features**: README describes feature not in code -> DELETE that paragraph.
2. **Excess badges**: more than 5 badges or any dead URL -> remove.
3. **Duplication**: same content in README + docs/ -> keep one, link the other.
4. **CHANGELOG verbosity**: any entry > 1 line -> truncate.
5. **Stale comments**: comment contradicts current logic in changed files -> fix or delete.
6. **Empty files**: .md with only TODO or blank -> delete.
7. **Line budget**: README CN <= 300 lines, README EN <= 300 lines. Over budget -> trim.
8. **No philosophy**: README states WHAT/HOW only. Never WHY-we-designed-it-this-way. No "architecture philosophy" sections.
9. **No history appendix**: version history belongs in CHANGELOG.md only, never embedded in README or SKILL files.
10. **Commented-out code**: blocks of >3 commented lines in changed files -> delete (use git history, not comments).
11. **Empty catch/except**: bare `except:` or `catch {}` with no logging in changed files -> add logging or re-raise.

## Pitfalls

- `cd /d` does not work in QoderWork Bash -> use `dir_path` parameter instead.
- `gh release create` fails if tag already exists on remote -> check `git ls-remote --tags origin` first.
- Windows: confirm `gh auth status` before any gh command.
- Run `git push` and `git push --tags` separately (--follow-tags unreliable on some environments).
- Exclude `.git/`, `node_modules/`, `__pycache__/`, `.venv/` from all grep operations.
- Never execute tag/push/release before user confirms version in Phase 4.
- If privacy leak discovered AFTER push: `git filter-repo --replace-text rules.txt --force` then force-push. Requires user confirmation (destructive).
- `memory replace` operations require exact text match -> always `memory_get` first.

## Hard Constraints (non-negotiable)

1. Version number requires explicit user confirmation. AI must never auto-decide and execute.
2. This SKILL.md must never exceed 280 lines.
3. Every release must pass Phase 7 verification. If any gate fails, report failure clearly.
4. Never add "methodology", "design rationale", or "update history appendix" sections to any repo file.
5. README describes WHAT + HOW ONLY. No design philosophy, no changelog, no acknowledgments bloat.
6. Each phase gate is a hard stop. Do not skip gates even if user says "just push it".
7. Gates have three outcomes: PASS / PASS_WITH_WARNINGS / BLOCK. BLOCK = hard stop (secrets, CI red, user denied). PASS_WITH_WARNINGS = report issue list, user chooses continue or fix. PASS = clean.
8. Privacy scan (3i) is a BLOCK-level gate. Client names, user-specific paths, personal emails in tracked files = hard stop, no exceptions.
