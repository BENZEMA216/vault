---
name: code-style
description: 代码风格与最佳实践。当用户需要编写代码、代码审查或需要遵循编码规范时激活。
tokens: ~600
---

# 代码风格技能

## 核心原则

1. **可读性优先**：代码是写给人看的
2. **简单直接**：最简单的方案往往是最好的
3. **一致性**：遵循项目现有风格

## 命名规范

### 变量命名

```python
# Bad
d = get_data()
for i in d:
    if i['t'] == 'a':
        r.append(i)

# Good
user_data = get_user_data()
for record in user_data:
    if record['type'] == 'active':
        active_records.append(record)
```

### 常见模式

| 类型 | 模式 | 示例 |
|------|------|------|
| 布尔 | is_/has_/can_ | is_valid, has_permission |
| 获取 | get_ | get_user_by_id |
| 计算 | calculate_ | calculate_total |
| 验证 | validate_ | validate_email |
| 集合 | 复数 | users, order_items |

## 控制流

### Guard Clause

```python
# Bad - 嵌套深
def process(order):
    if order:
        if order.is_valid:
            if order.has_items:
                return do_something()
    return None

# Good - 提前返回
def process(order):
    if not order:
        return None
    if not order.is_valid:
        return None
    if not order.has_items:
        return None
    return do_something()
```

## 错误处理

```python
# Bad - 吞掉所有错误
try:
    result = operation()
except:
    pass

# Good - 明确异常类型
try:
    result = api.fetch(url)
except ConnectionError:
    logger.warning(f"Connection failed: {url}")
    result = cache.get(url)
except TimeoutError:
    raise ServiceUnavailableError("API timeout")
```

## 注释原则

```python
# Good - 解释「为什么」
# 使用二分查找，因为数据量可能达到百万级
index = bisect.bisect_left(sorted_data, target)

# Bad - 解释「是什么」（代码已说明）
# 遍历用户列表
for user in users:
    ...
```

## 检查清单

- [ ] 命名清晰、一致
- [ ] 无不必要的嵌套
- [ ] 异常被正确处理
- [ ] 无重复代码
- [ ] 无未使用的代码/导入
