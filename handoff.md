---
name: handoff-log
description: >-
  Quick-log a single progress entry, decision, or blocker to the project working file.
  Lightweight alternative to /handoff — no full context dump, just append one entry.
  Use when the user says "log", "记录", "记一下", "note this", "handoff-log",
  or wants to quickly record a milestone without generating a full handoff.
---

# Handoff Log Skill

Append a single entry to the project's persistent working file (PROJECT.md).

**自动记录 vs 手动记录：**
当 `.handoff/PROJECT.md` 存在时，Agent 会在关键节点（里程碑、决策、阻塞、失败路径）**自动记录**，无需用户干预。
`/handoff-log` 用于用户**主动**想要记录某个特定内容的场景 — 比如 Agent 没有自动捕捉到的、或者用户认为特别重要的事项。

## When to use

- Agent 的自动记录没有捕捉到你想记的内容
- 你想强调某个特定的决策或发现
- 用户说: "log this", "记录一下", "记一下", "note this", "handoff-log"
- 用户想手动添加一条约束或待办

## Workflow

### Step 1: Locate PROJECT.md

Look for `.handoff/PROJECT.md`:

- **If found** → proceed to append
- **If not found** → suggest running `/handoff-init` first, or offer to create it now with minimal context

### Step 2: Classify the entry

Determine what type of entry this is:

| Type | Section to update | Format |
|------|-------------------|--------|
| Progress/milestone | 进展日志 | `[日期] - [完成内容]` |
| Decision | 决策日志 | Table row: date, decision, rationale, status |
| Blocker | 阻塞与失败路径 → 当前阻塞 | `- [描述] — 影响: [范围] — 建议: [方向]` |
| Failed approach | 阻塞与失败路径 → 已验证无效的路径 | `- [方案] — 失败原因: [原因] — **不要重复尝试**` |
| New todo | 待办事项 | `- [ ] [任务] — 优先级: 高/中/低` |
| Completed todo | 待办事项 | Mark existing item as `[x]` |
| New constraint | 约束条件 | `- [约束]` |
| New key file | 关键文件索引 | Table row |

If the entry type is ambiguous, ask one clarifying question.

### Step 3: Append to PROJECT.md

Read the current PROJECT.md, find the appropriate section, and append the entry:

- **Prepend** to log sections (newest first)
- **Append** to list sections (constraints, todos)
- **Update** the "最后更新" timestamp
- Keep entries concise — one line per item when possible

### Step 4: Confirm

Output a brief confirmation:

```
✅ 已记录到 .handoff/PROJECT.md
📝 类型: [进度/决策/阻塞/失败路径/待办]
📋 内容: [一句话概括]
```

No need to output the full document or create a snapshot.

## Entry format examples

User says: "我们决定用 PostgreSQL 而不是 MongoDB"
→ Decision log: `| 2026-06-16 | 使用 PostgreSQL 替代 MongoDB | 关系型更适合当前查询模式 | 有效 |`

User says: "API 调用超时了，可能是网络问题"
→ Blocker: `- API 调用超时 — 影响: 数据获取流程 — 建议: 检查网络/添加重试机制`

User says: "试了方案A不行，内存溢出"
→ Failed path: `- 方案A: 直接加载全量数据到内存 — 失败原因: 数据量过大导致 OOM — **不要重复尝试**`

User says: "数据清洗完成了"
→ Progress: `### 2026-06-16\n- 完成数据清洗流程`

## Notes

- This skill does NOT create timestamped snapshots — it only updates PROJECT.md
- For a full handoff document (with copy-paste context block), use `/handoff`
- If the user wants to log multiple items at once, suggest using `/handoff` instead
