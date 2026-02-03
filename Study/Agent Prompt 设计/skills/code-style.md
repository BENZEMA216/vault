# Skill: code-style

## SKILL.md 模板

```yaml
---
name: code-style
description: |
  代码风格与最佳实践技能。当用户需要编写代码、代码审查、
  或需要遵循特定编码规范时激活此技能。
---
```

```markdown
# 代码风格技能

## 核心原则

1. **可读性优先**：代码是写给人看的
2. **简单直接**：最简单的方案往往是最好的
3. **一致性**：遵循项目现有风格

## 命名规范

### 变量命名

```python
# Bad - 太短、无意义
d = get_data()
for i in d:
    if i['t'] == 'a':
        r.append(i)

# Good - 清晰、自解释
user_data = get_user_data()
for record in user_data:
    if record['type'] == 'active':
        active_records.append(record)
```

### 函数命名

```python
# Bad
def proc(d): ...
def do_stuff(x): ...

# Good - 动词 + 名词，表明意图
def process_payment(order): ...
def validate_user_input(form_data): ...
def calculate_shipping_cost(items, destination): ...
```

### 常见命名模式

| 类型 | 模式 | 示例 |
|------|------|------|
| 布尔值 | is_/has_/can_/should_ | is_valid, has_permission |
| 获取 | get_ | get_user_by_id |
| 设置 | set_ | set_config_value |
| 计算 | calculate_/compute_ | calculate_total |
| 验证 | validate_/check_ | validate_email |
| 转换 | to_/from_/parse_ | to_json, parse_date |
| 集合 | 复数形式 | users, order_items |

## 控制流

### Guard Clause（卫语句）

```python
# Bad - 嵌套深
def process_order(order):
    if order:
        if order.is_valid:
            if order.has_items:
                # 实际逻辑
                return result
    return None

# Good - 提前返回
def process_order(order):
    if not order:
        return None
    if not order.is_valid:
        return None
    if not order.has_items:
        return None

    # 实际逻辑
    return result
```

### 避免深层嵌套

```python
# Bad - 嵌套 4 层
for user in users:
    if user.is_active:
        for order in user.orders:
            if order.status == 'pending':
                process(order)

# Good - 提取函数 + 提前跳过
def get_pending_orders(users):
    for user in users:
        if not user.is_active:
            continue
        for order in user.orders:
            if order.status == 'pending':
                yield order

for order in get_pending_orders(users):
    process(order)
```

## 错误处理

### 只捕获预期的异常

```python
# Bad - 吞掉所有错误
try:
    result = complex_operation()
except:
    pass

# Bad - 捕获太宽泛
try:
    result = complex_operation()
except Exception as e:
    logger.error(e)
    return None

# Good - 明确异常类型
try:
    result = api_client.fetch_data(url)
except ConnectionError:
    logger.warning(f"Failed to connect to {url}, using cache")
    result = cache.get(url)
except TimeoutError:
    raise ServiceUnavailableError("API timeout")
```

### 有意义的错误信息

```python
# Bad
raise ValueError("Invalid input")

# Good
raise ValueError(f"Expected positive integer, got {value!r}")
```

## 注释原则

### 什么时候加注释

```python
# Good - 解释「为什么」
# 使用二分查找优化，因为数据量可能达到百万级
index = bisect.bisect_left(sorted_data, target)

# Good - 解释业务规则
# 根据财务部要求，超过 30 天的订单不参与退款计算
if (now - order.created_at).days > 30:
    continue

# Bad - 解释「是什么」（代码已经说明了）
# 遍历用户列表
for user in users:
    ...
```

### 什么时候不加注释

```python
# Bad - 代码自解释，注释多余
# 检查用户是否已激活
if user.is_active:
    # 获取用户的订单
    orders = user.get_orders()
    # 计算订单总数
    total = len(orders)
```

## 函数设计

### 单一职责

```python
# Bad - 做太多事
def process_user(user):
    validate_user(user)
    save_to_db(user)
    send_welcome_email(user)
    update_analytics(user)
    return user

# Good - 拆分职责
def create_user(user_data):
    user = validate_and_build_user(user_data)
    save_user(user)
    return user

def onboard_user(user):
    send_welcome_email(user)
    track_signup_event(user)
```

### 参数数量

```python
# Bad - 参数太多
def create_report(title, author, date, format, include_charts,
                  include_summary, page_size, orientation):
    ...

# Good - 使用配置对象
@dataclass
class ReportConfig:
    title: str
    author: str
    date: datetime
    format: str = 'pdf'
    include_charts: bool = True
    include_summary: bool = True
    page_size: str = 'A4'
    orientation: str = 'portrait'

def create_report(config: ReportConfig):
    ...
```

## 代码组织

### 文件结构

```
module/
├── __init__.py      # 公开 API
├── core.py          # 核心逻辑
├── models.py        # 数据模型
├── utils.py         # 工具函数
├── exceptions.py    # 自定义异常
└── tests/
    ├── test_core.py
    └── test_models.py
```

### 导入顺序

```python
# 1. 标准库
import os
import sys
from datetime import datetime

# 2. 第三方库
import requests
from pydantic import BaseModel

# 3. 本地模块
from .models import User
from .utils import validate_email
```

## 检查清单

代码提交前检查：

- [ ] 命名是否清晰、一致
- [ ] 是否有不必要的嵌套
- [ ] 异常是否被正确处理
- [ ] 是否有重复代码可以提取
- [ ] 注释是否解释「为什么」而非「是什么」
- [ ] 函数是否只做一件事
- [ ] 是否有未使用的代码/导入
```
