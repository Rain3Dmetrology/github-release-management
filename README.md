# github-release-management

Standardized GitHub release workflow skill for QoderWork.

## Usage

Install to `~/.qoderworkcn/skills/github-release-management/` and trigger with: release, publish version, bump version, tag, deploy.

## Workflow

Audit -> Cleanup -> Full Repo Doc Sync -> Version Decision (user-confirmed) -> Commit -> Tag -> Push -> gh release -> Verify

## Constraints

- SKILL.md <= 250 lines
- Anti-bloat rules enforced on every release
- Verification gates at every phase
- Version number requires explicit user confirmation
