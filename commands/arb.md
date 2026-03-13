---
name: arb
description: "Execute a 4-step Architecture Review Board (ARB) workflow: role casting (SRE/Security/DBA) → merge → devil's advocate → decisive checks. Usage: /arb [design-doc-path]"
argument-hint: "[design-doc-path]"
disable-model-invocation: true
allowed-tools:
  - Agent
  - TaskOutput
  - Read
  - Bash
---

# Architecture Review Board (ARB) Workflow

Current branch: !`git branch --show-current`

Recent commits for architectural context (last 5):
```
!`git log --oneline -5`
```

Design document path: $ARGUMENTS

## 执行步骤

当用户运行此命令时，你必须按顺序执行以下 4 步工作流：

### Step 1: 并行角色投影评审

读取设计文档后，启动 3 个并行后台 Agent 任务：

1. **SRE Reviewer** — 使用 `Agent` 工具，`run_in_background=true`，提示词包含："扮演 SRE 角色，使用 role_casting_reviewer 技能对设计进行评审..."
2. **Security Reviewer** — 使用 `Agent` 工具，`run_in_background=true`，提示词包含："扮演 Security 角色，使用 role_casting_reviewer 技能对设计进行评审..."
3. **DBA Reviewer** — 使用 `Agent` 工具，`run_in_background=true`，提示词包含："扮演 DBA 角色，使用 role_casting_reviewer 技能对设计进行评审..."

等待所有后台任务完成后，使用 `TaskOutput` 工具收集每个任务的结果。

### Step 2: 合并 Acceptance Packs

使用 `Agent` 工具（`run_in_background=false`），提示词包含："使用 acceptance_pack_merger 技能，将 3 份 Acceptance Pack 合并为统一的 Consolidated Architecture Review"

### Step 3: 魔鬼代言人挑战

使用 `Agent` 工具（`run_in_background=false`），提示词包含："使用 devils_advocate_reviewer 技能，对合并后的评审结果进行系统性挑战，揭示隐藏假设和致命缺陷"

### Step 4: 决定性校验设计

使用 `Agent` 工具（`run_in_background=false`），提示词包含："使用 decisive_check_designer 技能，为每个致命缺陷设计可执行的验证方案"

## 输出要求

返回一份完整的 ARB 评审报告（使用中文），包含：

1. SRE/Security/DBA Acceptance Packs（并行评审结果）
2. Consolidated Architecture Review（合并后的 Blockers/Gates/Recommendations）
3. Devil's Advocate Report（致命缺陷分析）
4. Decisive Check Plan（可执行的验证方案）
5. Final Go/No-Go Decision（最终决策）

如果用户提供了设计文档路径，先读取文档内容，在所有评审中引用。