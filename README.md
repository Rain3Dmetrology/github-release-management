# github-release-management

Standardized GitHub release workflow skill for QoderWork. Enforces gated verification, anti-bloat rules, and privacy scanning at every release.

## Usage

Install to `~/.qoderworkcn/skills/github-release-management/` and trigger with: release, publish version, bump version, tag, deploy.

## Workflow

```
Audit → Cleanup → Full Repo Doc Sync → Version Decision (user-confirmed)
  → Commit → Tag → Push → gh release → Verify
```

7 phases, each with a hard gate. Gate outcomes: PASS / PASS_WITH_WARNINGS / BLOCK.

## Key Features

- Privacy & local-state scan (blocking): detects client names, hardcoded disk paths, personal emails, credentials before push
- Anti-bloat enforcement: zombie features, excess badges, duplication, line budgets
- Flow control: hotfix path (≤3 commits) skips full scan; full path executes all phases
- Version number requires explicit user confirmation — never auto-decided
- Post-push leak remediation guidance (git filter-repo)

## Constraints

- SKILL.md ≤ 280 lines (current: 233)
- 8 hard constraints, non-negotiable
- Every release must pass Phase 7 verification

## License

BSL 1.1 — see [LICENSE](LICENSE).
