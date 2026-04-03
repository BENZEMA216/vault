---
name: update_todo
description: 更新单个任务状态
---

更新单个任务的状态。

**参数**：
- `id` (必需): 任务 ID
- `status` (必需): 新状态

**状态流转**：
```
pending → in_progress → completed
                     → failed
                     → skipped
```

**使用时机**：
- 开始处理任务 → `in_progress`
- 任务完成 → `completed`
- 任务失败 → `failed`（说明原因）
- 跳过任务 → `skipped`（说明原因）

**注意**：
- 同时只能有一个 `in_progress` 任务
- 返回值包含完整列表，无需再调 `read_todos`
- 完成一个任务后再开始下一个
