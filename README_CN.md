# github-release-management

QoderWork 标准化 GitHub 发布工作流技能。每次发布强制执行门禁验证、反膨胀规则和隐私扫描。

## 使用方式

安装至 `~/.qoderworkcn/skills/github-release-management/`，触发词：release、publish version、bump version、tag、deploy、发布版本。

## 工作流

```
审计 → 清理 → 全仓库文档同步 → 版本决策(用户确认)
  → 提交 → 打标签 → 推送 → gh release → 验证
```

7 个阶段，每阶段设硬门禁。门禁结果：PASS / PASS_WITH_WARNINGS / BLOCK。

## 核心能力

- 隐私与本机状态扫描（阻断级）：推送前检测客户名、硬编码磁盘路径、个人邮箱、凭据
- 反膨胀执行：僵尸功能、过多徽章、内容重复、行数预算
- 流程控制：热修复路径（≤3 提交）跳过全扫描；完整路径执行全部阶段
- 版本号必须用户显式确认，绝不自动决定
- 推送后泄露补救指引（git filter-repo）

## 约束

- SKILL.md ≤ 280 行（当前 233 行）
- 8 条硬约束，不可协商
- 每次发布必须通过 Phase 7 验证

## 许可证

BSL 1.1 — 见 [LICENSE](LICENSE)。
