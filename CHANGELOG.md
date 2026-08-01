# Changelog

All notable changes to this skill are documented in this file.
Format follows [Keep a Changelog](https://keepachangelog.com/) + [SemVer](https://semver.org/).

## [1.1.0] - 2026-08-02

### Added
- Phase 3i expanded to "quality & privacy scan": drive-path grep, personal email grep, client name detection
- Blocking severity covers client names, user-specific disk paths, credentials
- .gitignore verification extended: `local_*_paths*`, `*.local.*`
- CI privacy lint existence check (flag as important if missing)
- Phase 1: local↔remote divergence check (`git fetch` + `rev-list`)
- Pitfalls: `git filter-repo` emergency remediation guidance
- Hard Constraint #8: privacy scan is BLOCK-level, non-skippable

### Changed
- Phase 5: `git add -A` replaced with explicit staging (prevent accidental inclusion)
- Line budget raised 250 → 280 (actual: 233 lines)
- Secrets grep excludes env-var patterns (`:-`) to reduce false positives

## [1.0.0] - 2026-07-30

### Added
- Initial 7-phase gated release workflow
- Anti-bloat rules (11 items)
- Pre-release quality scan with severity levels
- Flow control: hotfix path vs full path
- Hard constraints with gate enforcement
