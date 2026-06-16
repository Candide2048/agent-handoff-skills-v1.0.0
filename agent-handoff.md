---
name: handoff-receive
description: >-
  Import and activate a project handoff document from a previous AI agent session.
  Reads PROJECT.md (persistent) or a handoff snapshot, restores context, and resumes work.
  Use when the user says "handoff-receive", "接收交接", "恢复上下文", "import handoff",
  "load context", "继续之前的工作", or provides a handoff document to continue from.
---

# Handoff Receive Skill

Import a structured handoff document from a previous AI agent and resume work as if you had
been present from the start of the project.

## When to use

- Starting a new session and want to restore previous context
- User pastes a handoff document at the start of a conversation
- User says "接收交接", "恢复上下文", "继续之前的工作"
- User provides a handoff file path (e.g., `.handoff/PROJECT.md` or `.handoff/LATEST.md`)
- User says "read the handoff and continue"

## Workflow

### Step 1: Locate the handoff

Check for handoff documents in this priority order:

1. If user pasted content directly → use that content
2. If user provided a file path → read that file
3. If user said "latest" or gave no specific path:
   a. First check `.handoff/PROJECT.md` (persistent living document — preferred)
   b. Then check `.handoff/LATEST.md` (snapshot copy)
   c. Then check `.handoff/` for the most recent `handoff-*.md` file
4. If no `.handoff/` directory exists → inform the user and ask them to provide the handoff content or run `/handoff-init`

### Step 2: Parse and internalize

Read the entire handoff document and extract:

**From PROJECT.md (persistent mode):**
- Project goal from "项目目标" section
- Current status from "进展日志" (latest entry)
- Decisions from "决策日志" table
- Blockers from "当前阻塞" section
- Failed paths from "已验证无效的路径" section
- Next steps from "待办事项" section
- Constraints from "约束条件" section
- Key files from "关键文件索引" table

**From standalone handoff:**
- Project goal, completed work, current status, blockers, failed paths, next steps, constraints, critical context

### Step 3: Acknowledge and confirm

Output a structured acknowledgment:

```
✅ 项目上下文已恢复 (Project Context Restored)

📋 项目目标: [一句话概括]
📊 当前进度: [上次做到哪了，基于最新日志条目]
🎯 下一步: [最优先的待办事项]

已理解的约束:
- [约束1]
- [约束2]

已记录的失败路径（不会重复）:
- [失败路径1]
- [失败路径2]

关键决策（已生效）:
- [决策1]
- [决策2]

准备就绪，请指示从哪里继续。
```

### Step 4: Resume work

- Start from the highest-priority uncompleted todo item
- Do NOT re-do completed work (check items marked `[x]`)
- Do NOT re-explore failed paths (check "已验证无效的路径")
- Do NOT ask questions already answered in the handoff
- If a blocker exists, propose a solution before asking the user
- Reference key files from the index rather than re-discovering them

## Edge cases

- **Malformed handoff**: If the document is incomplete, ask targeted questions about missing sections.
- **Conflicting context**: If the handoff conflicts with what you observe in the codebase, flag the discrepancy and ask the user which is current.
- **Stale handoff**: Check the "最后更新" timestamp. If it's more than a week old, warn the user that files/code may have changed.
- **Multiple handoffs**: If multiple handoff files exist in `.handoff/`, list them with timestamps and ask which to use.
- **No PROJECT.md but snapshots exist**: Use the most recent snapshot and suggest running `/handoff-init` to set up persistent tracking.

## Language handling

- Respond in the same language as the handoff document
- If the handoff is in Chinese, acknowledge in Chinese
- If mixed, default to the user's message language
