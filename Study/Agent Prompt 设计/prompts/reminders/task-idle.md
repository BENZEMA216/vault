---
name: task-idle
trigger: task_tools_idle > 5min
---

任务工具最近未使用。

如果当前工作涉及多个步骤，考虑：
- 用 `write_todos` 创建任务列表来跟踪进度
- 用 `update_todo` 更新任务状态
- 完成后标记为 `completed`

如果不需要任务跟踪，请忽略此提醒。
