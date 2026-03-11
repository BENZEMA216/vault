# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

- OpenClaw Debugger 需要重新规划产品方向（当前体验差，需确认用户场景再动手）

## 最近的关键决策

- **2026-03-05**: 搭建 Claude Code session 初始化系统
  - init.sh / session-wrap.sh / init_repos.conf

- **2026-03-11**: OpenClaw Debugger Console 已部署但体验差
  - 文件: /root/.openclaw/debugger/main.py + index.html
  - systemd: openclaw-debugger，port 8899，token: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP
  - cloudflared 已安装，临时访问: `nohup cloudflared tunnel --url http://localhost:8899 > /tmp/cloudflared.log 2>&1 &`
  - URL 随机变化，非持久

- **2026-03-11**: 产品反思
  - 问题根因：没想清楚部署模型就动手，在"网络可达性"上翻车
  - 竞品参照：Langfuse（最直接）、Flowise、Dify —— 都是让用户自己配反代，不自己解决公网访问
  - 下一步需先确认：自用 vs 做成 OpenClaw 正式功能？个人用推荐 Tailscale（稳定零配置）

## 阻塞项

- 确认 Debugger 的用户定位（自用 or 产品功能）再决定下一步实现方向
