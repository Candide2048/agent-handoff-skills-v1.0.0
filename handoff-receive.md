---
name: handoff-init
description: >-
  Initialize a persistent project working file (PROJECT.md) for cross-agent context continuity.
  Creates the .handoff/ directory and a living document that accumulates project state over time.
  Use when starting a new project, beginning a long-term task, or user says "init handoff",
  "初始化交接", "开始记录", "start tracking".
---

# Handoff Init Skill

Create a persistent project working file that acts as a "project brain" — a living document
that accumulates context across multiple AI agent sessions.

## When to use

- Starting a new project that will span multiple sessions
- Beginning a complex task that may require agent switching
- User says "初始化交接", "开始记录", "init handoff", "start tracking"
- User wants to establish a shared context file for multiple agents

## Workflow

### Step 1: Check existing state

Before initializing, check if `.handoff/PROJECT.md` already exists:

- If it exists → inform the user, show the last update timestamp, ask if they want to reset or continue
- If it doesn't exist → proceed with initialization

### Step 2: Gather initial context

Ask or infer the following (keep it conversational, not a questionnaire):

1. **Project name** — a short identifier
2. **Project goal** — what does "done" look like?
3. **Tech stack** — languages, frameworks, tools in use
4. **Key constraints** — deadlines, style guides, platform requirements
5. **Starting context** — what's already known or decided

If the user doesn't provide all of these, fill in what you can from the conversation context
and mark the rest as `[待定]`.

### Step 3: Create PROJECT.md

Create `.handoff/` directory if needed, then write `.handoff/PROJECT.md`:

```markdown
# 项目工作文件 (Project Working File)

> 项目: [项目名称]
> 初始化: [YYYY-MM-DD HH:MM]
> 最后更新: [YYYY-MM-DD HH:MM]
> 状态: 进行中

---

## 项目目标

[终极目标描述]

## 技术栈

- [语言/框架/工具]

## 约束条件

- [约束1]
- [约束2]

## 决策日志 (Decision Log)

| 日期 | 决策 | 理由 | 状态 |
|------|------|------|------|
| [日期] | [决策内容] | [为什么这样选] | 有效/已变更 |

## 进展日志 (Progress Log)

### [YYYY-MM-DD] — 初始化
- 项目启动，建立工作文件
- [初始上下文摘要]

## 阻塞与失败路径 (Blockers & Dead Ends)

### 当前阻塞
- [阻塞描述] — 影响: [范围] — 建议: [方向]

### 已验证无效的路径 ⚠️
- [方案] — 失败原因: [原因] — **不要重复尝试**

## 待办事项 (Todo)

- [ ] [任务1] — 优先级: 高/中/低
- [ ] [任务2] — 优先级: 高/中/低

## 关键文件索引

| 文件 | 用途 | 备注 |
|------|------|------|
| [路径] | [用途] | [备注] |

## 交接历史

| 时间 | 来源 | 快照文件 |
|------|------|----------|
| [时间] | [Agent/Session] | `handoff-[timestamp].md` |
```

### Step 4: Confirm and guide

Output:
- Where the file was created
- A reminder: "使用 `/handoff-log` 记录单条进展，使用 `/handoff` 生成完整交接文档"
- The initial content summary

## Notes

- PROJECT.md is the **single source of truth** for project state
- It should be concise enough to scan in 30 seconds, but complete enough to resume work
- New entries are prepended (newest first) in the Progress Log section
- The "已验证无效的路径" section is critical — it prevents repeated mistakes
- Keep the file under 200 lines; if it grows too long, archive old entries to a separate file
