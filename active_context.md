# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

- OpenClaw Debugger v5 可用，brutalist UI + 颜色区分 + session graph 已完成

## 最近的关键决策

- **2026-03-11**: Brutalist UI 重设计完成
  - CSS: --bg: #f0f0eb, --ink: #000, 1px 黑边，uppercase，monospace
  - Body: 黑色背景+6px margin = 外框效果；.app-shell: 浅灰内容区
  - 登录页同步改为 brutalist 风格

- **2026-03-11**: Debugger v5 功能增强（本次 session）
  - 颜色区分: user=蓝(#211dff)左边框, toolResult错误=红色, tool类型上色(exec=橙/image=蓝/search=绿)
  - dm badge=蓝, probe badge=橙
  - 折叠: SESSION CONTEXT面板可折叠, 长消息(>500字)自动截断+展开
  - Session Graph: Graph tab现在显示当前session的记忆拓扑(中心=session, 周围=sys/mem/tool/asset节点), 切换session自动刷新
  - 后端: trace endpoint新增freq字段(memory文件读取次数) + tool_calls汇总
  - 文件: /root/.openclaw/debugger/main.py + index.html

## 固定信息

- token: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP
- 启动tunnel: pkill cloudflared; nohup cloudflared tunnel --url http://localhost:8899 > /tmp/cf_tunnel.log 2>&1 & sleep 8; grep -o 'https://[a-z0-9-]*\.trycloudflare\.com' /tmp/cf_tunnel.log
- thumbcache: /root/.openclaw/debugger/.thumbcache/

## 阻塞项

- Tunnel URL 每次重启随机变化，需 named tunnel 固定