# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

- Claude Code 初始化系统已搭建完成，进入日常使用阶段

## 最近的关键决策

- **2026-03-05**: 搭建 Claude Code session 初始化系统
  - init.sh: 启动前并行拉 GitHub 热记忆，写入 MEMORY.md（auto-loaded）
  - session-wrap.sh: Stop hook，结束时提醒更新 active_context.md
  - init_repos.conf: tracked repos 可配置
  - 飞书文档已写好可 share: https://www.feishu.cn/docx/O7NqdCDryoCwJ7xxHxMcM5RAnde
  - 核心理念: GitHub 当外脑，init.sh 读，Stop hook 写，MEMORY.md 是中转站

## 阻塞项

<!-- 无 -->
