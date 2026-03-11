# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

- OpenClaw Debugger v4 可用，brutalist UI 已完成

## 最近的关键决策

- **2026-03-11**: OpenClaw Debugger v4 性能优化完成
  - Sessions列表缓存(TTL 30s)，热请求0.46s
  - Session详情分页(最后100条)，8s→1.3s
  - 缩略图API /api/thumb: Pillow 320px JPEG，磁盘缓存

- **2026-03-11**: Brutalist UI 重设计完成 (本次 session)
  - 风格: operator-terminal，参考 design-6621f4ed-e68e-4d09-8baf-f5d04955d7da.html
  - CSS: --bg: #f0f0eb, --ink: #000, 1px 黑边，uppercase，monospace
  - Body: 黑色背景+6px margin = 外框效果；.app-shell: 浅灰内容区
  - Hover 全部 invert（黑底白字）
  - 登录页同步改为 brutalist 风格
  - 文件: /root/.openclaw/debugger/main.py + index.html

## 固定信息

- token: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP
- 启动tunnel: pkill cloudflared; nohup cloudflared tunnel --url http://localhost:8899 > /tmp/cf_tunnel.log 2>&1 & sleep 8; grep -o 'https://[a-z0-9-]*\.trycloudflare\.com' /tmp/cf_tunnel.log
- thumbcache: /root/.openclaw/debugger/.thumbcache/

## 阻塞项

- Tunnel URL 每次重启随机变化，需 named tunnel 固定
