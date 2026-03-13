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

**如果用户提供了设计文档路径，先读取文档内容。**

启动 3 个并行后台 Agent 任务（使用 `Agent` 工具，`subagent_type` 参数设置为对应 agent 名称，`run_in_background=true`）：

1. **SRE Reviewer** — 调用 `arb-sre-reviewer` agent，提示词包含设计文档内容
2. **Security Reviewer** — 调用 `arb-security-reviewer` agent，提示词包含设计文档内容
3. **DBA Reviewer** — 调用 `arb-dba-reviewer` agent，提示词包含设计文档内容

等待所有后台任务完成后，使用 `TaskOutput` 工具收集每个任务的结果。

### Step 2: 合并 Acceptance Packs

使用 `Agent` 工具（`subagent_type="arb-merger"`，`run_in_background=false`），提示词包含：
- Step 1 收集的 SRE Acceptance Pack
- Step 1 收集的 Security Acceptance Pack
- Step 1 收集的 DBA Acceptance Pack

### Step 3: 魔鬼代言人挑战

使用 `Agent` 工具（`subagent_type="arb-devils-advocate"`，`run_in_background=false`），提示词包含：
- Step 2 产出的 Consolidated Architecture Review
- 原始设计文档内容

### Step 4: 决定性校验设计

使用 `Agent` 工具（`subagent_type="arb-decisive-checker"`，`run_in_background=false`），提示词包含：
- Step 3 产出的 Devil's Advocate Report（Fatal Flaws）
- Step 2 产出的 Consolidated Architecture Review（Blockers）
- 原始设计文档内容

## 输出要求

返回一份完整的 ARB 评审报告（使用中文），包含：

1. **SRE/Security/DBA Acceptance Packs**（并行评审结果）
2. **Consolidated Architecture Review**（合并后的 Blockers/Gates/Recommendations）
3. **Devil's Advocate Report**（致命缺陷分析）
4. **Decisive Check Plan**（可执行的验证方案）
5. **Final Go/No-Go Decision**（最终决策）

## 架构说明

此命令编排以下专用 agents：

| Agent | Skill | 职责 |
|-------|-------|------|
| `arb-sre-reviewer` | role_casting_reviewer | SRE 视角评审 |
| `arb-security-reviewer` | role_casting_reviewer | Security 视角评审 |
| `arb-dba-reviewer` | role_casting_reviewer | DBA 视角评审 |
| `arb-merger` | acceptance_pack_merger | 合并 Acceptance Packs |
| `arb-devils-advocate` | devils_advocate_reviewer | 魔鬼代言人挑战 |
| `arb-decisive-checker` | decisive_check_designer | 设计验证方案 |

每个 agent 预加载了对应的 skill，确保评审质量和一致性。