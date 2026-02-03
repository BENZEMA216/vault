---
name: tool-call-failed
trigger: tool_call_error
variables: [TOOL_NAME, ERROR_MESSAGE]
---

工具 `${TOOL_NAME}` 调用失败：${ERROR_MESSAGE}

请：
1. 分析失败原因
2. 尝试替代方案
3. 如果无法解决，告知用户
