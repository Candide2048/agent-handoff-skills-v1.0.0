---
name: agent-handoff
description: "Persistent project context transfer for Claude Code. Maintains a living PROJECT.md that auto-logs milestones, decisions, blockers, and failed paths. 4 commands: /handoff-init, /handoff-log, /handoff, /handoff-receive. Triggers: handoff, 交接, 切换agent, 保存上下文, context transfer."
metadata:
  version: "1.0.0"
  last_updated: "2026-06-16"
  status: active
  data_access_level: redacted
  task_type: open-ended
---

# Agent Handoff Skills

A persistent project context transfer system for Claude Code.

## Commands

| Command | Purpose | When to use |
|---------|---------|-------------|
| `/handoff-init` | Initialize PROJECT.md | Project start |
| `/handoff-log` | Manual single-entry log | Agent auto-trigger missed it |
| `/handoff` | Full handoff + snapshot | Switching agents |
| `/handoff-receive` | Import & restore context | New agent session |

## Auto-Trigger Rules

When `.handoff/PROJECT.md` exists, the agent **automatically** updates it at:

| Trigger | Section | Format |
|---------|---------|--------|
| Milestone completed | 进展日志 | `[date] - [what]` |
| Technical decision | 决策日志 | Table row |
| Error/blocker/failure | 阻塞与失败路径 | `- [desc] — impact: [scope]` |
| User says "don't do X" | 约束条件 | `- [constraint]` |
| Key file discovered | 关键文件索引 | Table row |

## Quick Start

```bash
/handoff-init          # Initialize
# work normally       # Agent auto-logs
/handoff               # Before switching
/handoff-receive       # In new session
```
