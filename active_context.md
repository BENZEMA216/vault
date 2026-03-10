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

- **2026-03-11**: OpenClaw Debugger Console 搭建完成
  - 文件: /root/.openclaw/debugger/main.py + index.html
  - systemd 服务: openclaw-debugger，port 8899，已运行
  - token auth 已加入: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP（URL param 或 cookie）
  - 进程已绑定 0.0.0.0:8899，iptables 已放行
  - **阻塞**: 腾讯云安全组未开放 8899 → 需在控制台手动添加入站规则 TCP:8899
  - 临时访问: SSH tunnel `ssh -L 8899:localhost:8899 root@43.160.242.46 -N`，然后 http://localhost:8899

## 阻塞项

- 腾讯云控制台开放安全组 TCP:8899（才能公网直接访问 Debugger）

