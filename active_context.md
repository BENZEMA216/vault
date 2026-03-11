# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

- OpenClaw Debugger 已验证可用，有一个 bug 待修

## 最近的关键决策

- **2026-03-11**: OpenClaw Debugger v2 验证通过
  - Sessions: 24条，正确解析 group/dm/probe 类型、channel名、消息数、模型
  - Memory: 4分组，21文件，内容读取正常
  - 文件: /root/.openclaw/debugger/main.py + index.html
  - token: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP
  - cloudflare tunnel: pkill cloudflared && nohup cloudflared tunnel --url http://localhost:8899 --no-autoupdate > /tmp/cloudflared.log 2>&1 &
  - URL: grep https:// /tmp/cloudflared.log | grep trycloudflare

- **已知 Bug**: Memory 重复文件
  - workspace/memory/ 和 memory/ 存在同名文件（如 aesthetic-fragments.md）
  - 导致主题记忆和品牌记忆分组出现重复
  - 修法：按内容 hash 去重，或在文件名后加来源路径标注

## 阻塞项

- Tunnel URL 不固定，需 Cloudflare named tunnel 或 Tailscale
