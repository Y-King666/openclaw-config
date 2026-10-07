# openclaw-config

Configuration snapshot of an OpenClaw setup — runtime config, installed skills, and agent workspace.

OpenClaw 配置快照 —— 运行时配置、已安装技能与智能体工作区。

[English](#english) · [中文](#中文)

---

## English

### Overview

This repository holds the contents of an OpenClaw `.openclaw` directory. It is a **template and backup**, not a runnable product: cloned as-is it will not start, because all credentials have been replaced with placeholders.

### Repository structure

```
.openclaw/
├── openclaw.json          # Main runtime config: models, agents, tools, channels, gateway, cron, hooks
├── .env.example           # Template listing required environment variable names
├── .gitignore             # Excludes .env (real credentials)
├── skills/                # 33 installed skills (docx, pdf, pptx, devops, code-review, ...)
└── workspace/             # Agent workspace
    ├── AGENTS.md          # Team layout and operating rules
    ├── SOUL.md            # Agent personality and values
    ├── IDENTITY.md        # Agent name, role, avatar reference
    ├── USER.md            # User profile and preferences
    ├── MEMORY.md          # Long-term memory
    ├── TOOLS.md           # Available tools
    ├── ERRORS.md          # Known failure modes
    ├── HEARTBEAT.md       # Periodic self-check routine
    ├── avatars/           # Avatar images
    ├── memory/            # Memory store
    ├── outcomes/          # Task outcome records
    ├── skills/            # Workspace-scoped skills
    └── workspace-{coder,editor,tester}/   # Sub-agent workspaces with their own persona files
```

### Getting started

1. Copy the environment template and fill in your own credentials:

   ```bash
   cp .env.example .env
   ```

   Required keys: `BAILIAN_API_KEY`, `ANTIGRAVITY_API_KEY`, `MINIMAX_CN_API_KEY`, `GPTSAPI_API_KEY`, `GITHUB_API_KEY`, `TAVILY_API_KEY`, `ANYCRAWL_API_KEY`, `BRAVE_API_KEY`, `GATEWAY_TOKEN`.

2. Edit `openclaw.json` and replace the placeholder credentials.

   **Important:** `openclaw.json` does *not* read from environment variables. Its `apiKey` fields are literal strings (`"sk-XXXXXX"`, `"XXXXXX"`) and must be edited in place. Filling `.env` alone will not make the providers work.

3. Review the config before running. Adjust `models.providers`, `agents`, and `channels` to match your own endpoints and accounts.

### Security

- **`.env` is git-ignored and must never be committed.** It holds live API keys.
- All credentials in this repository have been sanitized to placeholders. If you fork or reuse it, keep it that way.
- This repository is public and its `workspace/` directory contains agent persona and memory files. Do not commit private data, chat transcripts, or personal identifiers into it.

### Updating

```bash
git add -A && git commit -m "..." && git push
```

Before pushing, confirm no new credential-bearing files slipped past `.gitignore` — it currently excludes the exact name `.env` only.

---

## 中文

### 概述

本仓库保存了一个 OpenClaw `.openclaw` 目录的内容，用途是**模板与备份**，而非可直接运行的产品：直接克隆无法启动，因为所有凭据都已替换为占位符。

### 仓库结构

```
.openclaw/
├── openclaw.json          # 主运行时配置：模型、智能体、工具、频道、网关、定时任务、钩子
├── .env.example           # 环境变量名模板
├── .gitignore             # 排除 .env（真实凭据）
├── skills/                # 33 个已安装技能（docx、pdf、pptx、devops、code-review 等）
└── workspace/             # 智能体工作区
    ├── AGENTS.md          # 团队分工与运行规则
    ├── SOUL.md            # 智能体性格与价值观
    ├── IDENTITY.md        # 智能体名称、角色、头像引用
    ├── USER.md            # 用户档案与偏好
    ├── MEMORY.md          # 长期记忆
    ├── TOOLS.md           # 可用工具
    ├── ERRORS.md          # 已知故障模式
    ├── HEARTBEAT.md       # 周期性自检流程
    ├── avatars/           # 头像图片
    ├── memory/            # 记忆存储
    ├── outcomes/          # 任务结果记录
    ├── skills/            # 工作区级技能
    └── workspace-{coder,editor,tester}/   # 子智能体工作区，各自拥有独立人格文件
```

### 快速开始

1. 复制环境变量模板并填入你自己的凭据：

   ```bash
   cp .env.example .env
   ```

   需要的键：`BAILIAN_API_KEY`、`ANTIGRAVITY_API_KEY`、`MINIMAX_CN_API_KEY`、`GPTSAPI_API_KEY`、`GITHUB_API_KEY`、`TAVILY_API_KEY`、`ANYCRAWL_API_KEY`、`BRAVE_API_KEY`、`GATEWAY_TOKEN`。

2. 编辑 `openclaw.json`，替换其中的占位凭据。

   **重要：** `openclaw.json` **不会读取环境变量**。它的 `apiKey` 字段是字面字符串（`"sk-XXXXXX"`、`"XXXXXX"`），必须就地修改。只填 `.env` 无法让模型提供方正常工作。

3. 运行前请通读配置，按你自己的服务端点和账号调整 `models.providers`、`agents`、`channels`。

### 安全须知

- **`.env` 已被 git 忽略，绝不可提交。** 其中存放真实 API 密钥。
- 本仓库内所有凭据均已脱敏为占位符。若你复刻或复用本仓库，请保持这一状态。
- 本仓库为公开仓库，且 `workspace/` 目录包含智能体人格与记忆文件。请勿向其中提交隐私数据、对话记录或个人身份信息。

### 日常更新

```bash
git add -A && git commit -m "..." && git push
```

推送前，请确认没有新的含凭据文件绕过 `.gitignore` —— 它目前仅按精确名称排除 `.env` 这一个文件。
