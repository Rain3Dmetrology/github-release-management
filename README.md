# github-release-management

[English](#english) | [中文](#中文)

---

## 中文

### 解决什么问题

每次发版你是否遇到：文档和代码脱节、README 越写越臃肿、本机路径和客户信息不小心推到云端、版本号改了但 CHANGELOG 忘了同步？

这个 skill 把发布流程固化为 7 阶段门禁流水线，任何一步不达标就硬停，不给"先推了再说"留余地。

### 核心能力

- **隐私阻断门禁**：推送前自动扫描客户名、硬编码磁盘路径、个人邮箱、凭据——命中即 BLOCK，不可跳过
- **反膨胀执行**：僵尸功能描述、过多徽章、内容重复、行数超预算——发现即清理
- **四方同步校验**：代码 ↔ 中英文 README ↔ Release Notes ↔ CHANGELOG/packages 必须内容一致
- **版本确认权归你**：AI 只建议，绝不自动执行版本升级
- **流程控制**：热修复（≤3 提交）走快速路径；功能发布走完整 7 阶段

### 使用方式

安装至 `~/.qoderworkcn/skills/github-release-management/`，触发词：release、publish version、bump version、tag、deploy、发布版本。

### 工作流

```
审计 → 清理 → 全仓库文档同步 → 版本决策(用户确认)
  → 提交 → 打标签 → 推送 → gh release → 验证
```

### 约束

- SKILL.md ≤ 280 行（当前 245 行）
- 9 条硬约束，不可协商
- 每次发布必须通过 Phase 7 验证
- 遵循 Google 工程规范 + spec 工作流（先规划后执行）

### 许可证

BSL 1.1 — 见 [LICENSE](LICENSE)。

---

## English

### What Problem It Solves

Every release, do you face: docs drifting from code, README bloating endlessly, local paths and client names accidentally pushed to remote, version bumped but CHANGELOG forgotten?

This skill locks the release process into a 7-phase gated pipeline. Any gate failure is a hard stop — no "push first, fix later."

### Key Capabilities

- **Privacy blocking gate**: auto-scans for client names, hardcoded disk paths, personal emails, credentials before push — hit = BLOCK, non-skippable
- **Anti-bloat enforcement**: zombie feature descriptions, excess badges, duplication, line budget overruns — detected = cleaned
- **Four-way sync check**: code ↔ CN/EN README ↔ Release Notes ↔ CHANGELOG/packages must be consistent
- **Version confirmation is yours**: AI recommends, never auto-executes version bumps
- **Flow control**: hotfix (≤3 commits) takes fast path; feature release takes full 7 phases

### Usage

Install to `~/.qoderworkcn/skills/github-release-management/`, trigger with: release, publish version, bump version, tag, deploy.

### Workflow

```
Audit → Cleanup → Full Repo Doc Sync → Version Decision (user-confirmed)
  → Commit → Tag → Push → gh release → Verify
```

### Constraints

- SKILL.md ≤ 280 lines (current: 245)
- 9 hard constraints, non-negotiable
- Every release must pass Phase 7 verification
- Follows Google engineering standards + spec workflow (plan before execute)

### License

BSL 1.1 — see [LICENSE](LICENSE).
