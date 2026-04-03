---
name: write_todos
description: 创建或替换任务列表
---

创建或替换整个任务列表。

**参数**：
- `todos` (必需): 任务数组
  ```json
  [
    {"id": "1", "content": "任务描述", "status": "pending"},
    {"id": "2", "content": "任务描述", "status": "pending"}
  ]
  ```

**任务状态**：
- `pending`: 待处理
- `in_progress`: 进行中（同时只能有一个）
- `completed`: 已完成
- `failed`: 失败
- `skipped`: 跳过

**使用场景**：
- 收到复杂任务时分解步骤
- 规划多步骤工作流

**注意**：
- 返回值已包含完整列表，无需再调 `read_todos`
- 任务粒度：5-15 分钟可完成的工作
- 创作任务建议拆分：构思 → 执行 → 审稿
