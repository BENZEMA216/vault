---
name: execute
description: 执行 shell 命令
---

在 shell 中执行命令。

**参数**：
- `command` (必需): 要执行的命令
- `cwd`: 工作目录

**使用场景**：
- 运行构建/测试命令
- 安装依赖（npm install, pip install）
- Git 操作
- 启动服务

**注意**：
- 文件操作优先用专用工具（read_file, edit_file, glob, grep）
- 避免破坏性命令（`rm -rf`）除非明确要求
- 长时间运行的命令考虑后台执行
- 包含空格的路径要用引号包裹

**禁止**：
- `cat` 读文件 → 用 `read_file`
- `grep` 搜索 → 用 `grep` 工具
- `find` 查找 → 用 `glob`
- `echo >` 写文件 → 用 `write_file`
