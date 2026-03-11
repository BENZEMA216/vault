# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

- OpenClaw Debugger 可用，待改进访问体验（tunnel URL 不固定）

## 最近的关键决策

- **2026-03-05**: 搭建 Claude Code session 初始化系统

- **2026-03-11**: OpenClaw Debugger v2 完成
  - 文件: /root/.openclaw/debugger/main.py + index.html
  - 功能: Sessions tab + Memory tab（砍掉其余）
  - Memory 正确读取三个位置：
    - workspace/MEMORY.md（长期记忆）
    - workspace/memory/*.md（每日日志+主题文件）
    - memory/*.md（brand/creo等品牌记忆）
  - Sessions: 解析channel/类型/消息数，支持展开tool call和thinking
  - systemd: openclaw-debugger, port 8899, token: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP
  - 访问: cloudflare tunnel（URL每次重启随机）
    - 启动: `pkill cloudflared; nohup cloudflared tunnel --url http://localhost:8899 --no-autoupdate > /tmp/cloudflared.log 2>&1 &`
    - 获取URL: `grep https:// /tmp/cloudflared.log | grep trycloudflare`

## 阻塞项

- Tunnel URL 不固定 → 需 Cloudflare named tunnel 或 Tailscale 解决
