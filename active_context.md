# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

- Claude Code 初始化系统已搭建完成，进入日常使用阶段

## 最近的关键决策

- **2026-03-05**: 搭建 Claude Code session 初始化系统
  - init.sh / session-wrap.sh / init_repos.conf
  - 核心理念: GitHub 当外脑，init.sh 读，Stop hook 写，MEMORY.md 是中转站

- **2026-03-11**: OpenClaw Debugger Console 完成部署
  - 文件: /root/.openclaw/debugger/main.py + index.html
  - systemd 服务: openclaw-debugger，port 8899
  - token: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP
  - nginx 已配 /debugger/ 反代（port 80 也被安全组封了，实际走 cloudflare tunnel）
  - cloudflared 已安装: /usr/local/bin/cloudflared
  - 启动命令: `nohup cloudflared tunnel --url http://localhost:8899 --no-autoupdate > /tmp/cloudflared.log 2>&1 &`
  - URL 从 /tmp/cloudflared.log 读取（每次重启随机，需升级 named tunnel 才能固定）
  - **下一步**: 注册 Cloudflare 账号 → 创建 named tunnel → 固定域名 → 做成 systemd 服务

## 阻塞项

- 腾讯云安全组把所有入站端口都封了（含 80/8899），只能走出站 cloudflare tunnel
- trycloudflare.com URL 重启后变化，需 named tunnel 固定

