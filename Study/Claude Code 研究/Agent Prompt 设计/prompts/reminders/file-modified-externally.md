---
name: file-modified-externally
trigger: file_external_change
variables: [FILE_PATH]
---

文件 `${FILE_PATH}` 已被外部修改（用户或 linter）。

这是有意的修改，请注意：
- 不要回退这些更改（除非用户要求）
- 后续编辑基于新内容进行
- 如需读取该文件，会看到修改后的版本
