# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

- OpenClaw Debugger v4 可用，性能已优化

## 最近的关键决策

- **2026-03-11**: OpenClaw Debugger v4 性能优化完成
  - 问题根因: Sessions列表每次读所有文件(1.7s)、Session详情全量解析3MB(8s)、大图无缩略图(2.4s)
  - 修复:
    1. 内存缓存(TTL 30s): sessions/memory/assets 列表缓存，热请求0.46s
    2. Session详情分页: 默认返回最后100条，payload 3MB→660KB，8s→1.3s
    3. 缩略图API /api/thumb: Pillow生成320px JPEG，磁盘缓存，6.6MB→3KB，2.4s→0.6s
  - 剩余~0.5s为 Cloudflare tunnel 不可避免的网络延迟
  - 文件: /root/.openclaw/debugger/main.py + index.html
  - thumbcache: /root/.openclaw/debugger/.thumbcache/
  - token: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP
  - 启动tunnel: pkill cloudflared; nohup cloudflared tunnel --url http://localhost:8899 --no-autoupdate > /tmp/cloudflared.log 2>&1 &

## 阻塞项

- Tunnel URL 每次重启随机变化，需 named tunnel 固定
