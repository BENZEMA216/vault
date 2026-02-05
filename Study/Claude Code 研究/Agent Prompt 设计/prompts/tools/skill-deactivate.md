---
name: deactivate_skill
description: 停用技能
---

停用一个技能，从上下文中移除其指引。

**参数**：
- `name` (必需): 技能名称

**使用场景**：
- 技能相关任务已完成
- 需要切换到其他技能
- 释放上下文空间

**注意**：
- 及时释放不需要的技能
- 切换技能时先 deactivate 旧的，再 activate 新的
