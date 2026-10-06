---
name: skills-install-workflow
description: Standardized workflow for discovering, installing, scanning, documenting, and testing new skills. Use whenever adding or evaluating any new skill.
---

# Skills Install Workflow

> **Purpose**：把“装技能”从一次性临时操作，升级为**可重复、可审计、可回滚**的标准流程。

## 何时必须使用本技能

**任何**涉及以下行为时，必须先调用本技能，再开始动作：

- 安装新技能（默认安装到 `C:\Users\32027\.openclaw\skills`，即当前 OpenClaw 工作空间的 `skills/` 目录）
- 替换现有技能（同名覆盖、迁移到新仓库等）
- 对“不熟悉的技能”做试用性安装 / 评估

包括但不限于：

- 用户说：“安装 X 技能”“下个 skill 做 Y”
- 助手自己认为“这个问题可以用某个新技能解决”
- 迁移/升级现有技能版本（例如：browser-use、healthcheck 等）

> ❗ **例外**：只有当用户明确要求“全局安装 / 特定路径”时，才可以偏离默认路径（例如安装到 `~/.agents/skills`）。

---

## 总体流程

1. **澄清需求**：确认要解决的具体问题和场景
2. **技能发现**：用 `find-skills` + `npx skills find` 搜索候选技能
3. **候选筛选**：根据安装量、安全评估、仓库质量选择目标技能
4. **安装执行**：遵守默认安装位置规则安装技能
5. **安全 & 结构扫描**：用 `skills-scanner`（或后续指定工具）进行检查
6. **功能测试**：跑一个最小可验证用例，确认技能可用
7. **注册与文档更新**：更新 TOOLS.md / 相关说明文件
8. **失败处理与回滚**：不通过时，卸载 + 记录经验

---

## Step 0：前置约束

在执行流程前：

- 必须确认当前终端/工作目录为 **OpenClaw 主工作空间**：
  - `C:\Users\32027\.openclaw\workspace\main-workspace`
- 默认技能安装目录为：
  - `C:\Users\32027\.openclaw\skills`（即 repo 内的 `skills/` 目录）
- 禁止直接手写技能目录内容（`skills/`、`~/.agents/skills/`），所有新增技能必须通过 `npx skills` 引入。

只有当用户明确指定“全局安装”或明确路径时，才允许：

- 使用 `-g` 安装到 `~/.agents/skills/`
- 或安装到用户要求的其他目录

---

## Step 1：澄清需求

**目标**：用两三句话明确“为什么要装这个技能”。

助手应主动总结：

- 要解决的问题（如：“浏览器自动操作网页截图”）
- 预期使用频率（一次性 / 经常使用）
- 预期主要服务的 agent（例如：OpenClaw 主 agent / coder / tester 等）

如果需求模糊（例如“装个好用的前端技能”），必须先通过 1~2 轮追问澄清后再进行搜索。

---

## Step 2：技能发现（使用 find-skills）

**统一要求**：

1. 先使用 **find-skills** 技能给出的流程
2. 由它指导使用 `npx skills find` 命令

示例命令：

```bash
npx skills find <关键词>
# 例：npx skills find browser-use
# 例：npx skills find testing
```

记录关键信息（至少一项）：

- `owner/repo@skill` 标识
- 安装量（例如 42.9K installs）
- skills.sh 页面链接

如果搜索不到合适技能：

- 明确告知用户：“当前技能生态暂无现成技能，可以先用通用能力解决”
- 不继续执行安装流程

---

## Step 3：候选筛选与安全评估

对于搜索到的技能列表，需要：

1. 按照以下优先级进行评估：
   - 安装量更高（代表使用更广）
   - 维护者/仓库更可信（组织账号 / 活跃度）
   - 功能描述与实际需求更匹配
2. 必须查看 `npx skills add` 过程中出现的 **Security Risk Assessments**：

典型输出示例：

- Gen: Safe / Med Risk
- Socket: 1 alert
- Snyk: Low Risk / High Risk

流程要求：

- 如果评估结果是 **Safe / Low Risk**：可以继续安装，但仍需后续测试
- 如果评估结果包含 **High Risk**：
  - 必须在回复中明确标记风险
  - 用户未明确同意，不得继续安装
  - 如果用户坚持安装，需要在记忆/日志中记录该决策

---

## Step 4：选择安装范围与方式

**默认规则（优先遵守）**：

- 安装到当前 OpenClaw 工作空间的 `skills/` 目录：

```bash
# 在 C:\Users\32027\.openclaw\workspace\main-workspace 目录下
npx skills add <owner/repo@skill>
```

**仅在用户明确要求时**，才改为：

- 全局安装（供其他 Agent/IDE 共用）：

```bash
npx skills add <owner/repo@skill> -g -y
```

CLI 若弹出交互式选项（选择 Agent / 范围 / 安装方法）：

- 优先选：Universal + Symlink（保持单一来源，方便更新）
- 如涉及覆盖（overwrites）：需要在回复里说明哪个 Agent 被覆盖

---

## Step 5：安全与结构扫描（skills-scanner）

> 注：这里预留给一个扫描工具，例如 `skills-scanner`。实际命令名称需根据你后续确定的工具更新本技能文档。

安装完成后，必须执行一次扫描，例如（伪例）：

```bash
skills-scanner scan <skill-name>
# 或
npx skills scanner <owner/repo@skill>
```

扫描目标：

- 检查依赖、脚本、潜在危险操作（如任意文件写、网络访问）
- 检查与现有技能/工具的潜在冲突

流程要求：

- 若扫描结果无严重警告 → 可以继续到 Step 6
- 若出现高风险项 → 必须停止，并进入“失败处理与回滚”

---

## Step 6：功能测试（最小可用用例）

为每个新技能都设计一个 **最小测试用例**，只验证：

- 能否被当前 Agent 正确识别和调用
- 最核心功能是否工作正常

示例：

- `browser-use`：执行一次 `browser-use open https://example.com` + `browser-use state`
- `feishu-team-tasks`：调用一次最小的任务创建/查询流程
- `find-skills`：用一个简单 query，确认输出格式正常

要求：

- 测试命令不应包含危险操作（删除文件、改配置、对外发送消息等）
- 若测试失败：
  - 记录失败信息（命令、错误输出）
  - 进入“失败处理与回滚”

---

## Step 7：注册与文档更新（TOOLS.md 等）

当技能通过扫描 + 测试后，必须更新：

1. **TOOLS.md**
   - 在合适的分类中加入此技能：
     - 若是流程/自我提升型 → “自我提升与技能发现”
     - 若是领域工具 → 对应的专业技能分类
   - 包含内容：名称、用途、一句使用规范说明

> ⚠️ 注意：**单纯安装技能不允许修改 `AGENTS.md`**。
> 只有当“新的工作流程规范”被你审批为长期协议时，才可以在 `AGENTS.md` 中挂一条说明。

更新规则：

- 修改前：必须先读取 TOOLS.md 全文
- 修改后：保持原有格式和表格对齐
- 如有“最后更新”字段，按实际时间手动更新

---

## Step 8：失败处理与回滚

当任意一步出现“不适合继续使用”的情况，必须执行：

1. 卸载技能：
   - 如果有卸载命令（例如 `npx skills remove` / `npx skills rm`），优先使用
   - 否则：
     - 删除对应技能目录（项目级 `skills/<name>` 或全局 `~/.agents/skills/<name>`）
2. 恢复/修复：
   - 若 TOOLS.md 已被修改，需回滚对应条目
3. 记录经验：
   - 在记忆系统中记一条简短经验（如：某技能风险高/与 X 冲突）

---

## 红线与禁止行为

在使用本流程时，以下行为禁止出现：

- 直接在 `skills/` 或 `~/.agents/skills/` 目录写入/修改文件来“伪造安装”
- 在未执行扫描/测试的前提下，将技能写入 TOOLS.md
- 无视安全评估中的 High Risk 且不告知用户
- 不通过本技能流程就擅自安装技能

---

## 实施建议

首次启用本技能时，建议对现有已安装技能做一次“追认”：

1. 用本流程回顾 browser-use、using-superpowers 等核心技能：
   - 补充缺失的 TOOLS.md 条目
   - 如有记录缺失的风险评估/测试说明，可补写一小段
2. 后续所有新技能一律走本流程，保持生态整洁一致。
