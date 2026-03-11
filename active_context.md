# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

- OpenClaw Debugger v3 可用（Sessions + Memory + Assets）

## 最近的关键决策

- **2026-03-11**: OpenClaw Debugger v3 完成
  - 新增 Assets tab：图片资产浏览器，支持缩略图网格 + 大图预览
  - 7个文件夹分组：SEEK WITHIN产品原图、1747品牌素材、workspace AI生成等
  - API: /api/assets (列表) + /api/image (serve图片文件)
  - 文件: /root/.openclaw/debugger/main.py + index.html
  - token: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP
  - 启动tunnel: pkill cloudflared; nohup cloudflared tunnel --url http://localhost:8899 --no-autoupdate > /tmp/cloudflared.log 2>&1 &
  - 获取URL: grep https:// /tmp/cloudflared.log | grep trycloudflare

- **已知 Bug**: Memory 主题记忆/品牌记忆有重复文件（待修）

## 阻塞项

- Tunnel URL 每次重启随机变化，需 named tunnel 固定
