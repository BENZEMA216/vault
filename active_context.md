# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

- 用 mudrii/openclaw-dashboard 替换自制 debugger

## 最近的关键决策

- **2026-03-05**: 搭建 Claude Code session 初始化系统

- **2026-03-11**: 发现 mudrii/openclaw-dashboard，决定放弃自制 debugger
  - 项目: https://github.com/mudrii/openclaw-dashboard
  - 功能完整：12个面板，费用/session/cron/sub-agent/模型用量/趋势图/AI Chat
  - 安装: `curl -fsSL https://raw.githubusercontent.com/mudrii/openclaw-dashboard/main/install.sh | bash`
  - 默认 127.0.0.1:8080，本地访问，systemd 服务
  - Go 单二进制 or Python stdlib，零外部依赖
  - 自制 debugger (/root/.openclaw/debugger/) 可废弃

- **自制 debugger 现状**（可保留备用，或直接删）
  - /root/.openclaw/debugger/main.py + index.html
  - systemd: openclaw-debugger，port 8899，token: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP
  - cloudflared 已安装: /usr/local/bin/cloudflared

## 下一步

- 在服务器上运行 openclaw-dashboard 的一键安装脚本
