# Skill 描述模板

> 用于定义新 Skill 的描述，供 AGENT 理解和调用

## 基本信息

```yaml
name: skill_name           # Skill 名称（英文，下划线分隔）
display_name: Skill 名称    # 显示名称（中文）
version: 1.0.0
category: generate         # 分类：readonly / edit / execute / generate
```

## 功能描述

[一段话描述这个 Skill 的功能和使用场景]

## 参数

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| param1 | string | 是 | 参数说明 | "示例值" |
| param2 | number | 否 | 参数说明 | 100 |

## 返回值

```json
{
  "success": true,
  "result": {
    "file_path": "outputs/generated_image.png",
    "metadata": {}
  }
}
```

## 使用示例

**场景**：[使用场景描述]

**调用**：
```json
{
  "tool": "skill_name",
  "params": {
    "param1": "value1",
    "param2": 100
  }
}
```

**结果**：[预期结果描述]

## 注意事项

- [注意事项 1]
- [注意事项 2]

## 错误处理

| 错误码 | 说明 | 处理建议 |
|--------|------|---------|
| ERR_001 | 错误描述 | 处理建议 |
