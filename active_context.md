# Active Context

> 当前工作状态，每次 session 的起点。

## 当前优先级

OpenClaw Debugger UI 走查迭代中（ralph loop）

## 最近的关键决策

### OpenClaw Debugger 设计系统 (2026-03-11)

完成了对 `/root/.openclaw/debugger/` 的全面重构：

**已完成：**
- 完整 CSS 设计系统：3层 surface (--bg/--bg-2/--bg-3)，3层 ink，2层 border，type scale
- Login page 统一设计系统
- Graph SESSION_NODE 颜色匹配 CSS 变量
- tool_use 块添加 category 颜色（orange/blue/green/dim）
- tool-cat-file CSS 类补全
- loadMoreMsgs bug 修复（DOM 替代 regex）
- sessions 侧边栏：搜索框 + ↻ 刷新按钮 + 缓存绕过
- 键盘导航（↑/↓ 切换 session）
- 消息 hover copy 按钮
- 内存侧面板动画修复（box-shadow 替代 border-width）
- Nav-right 显示 session 总数

**文件路径：**
- 本地：/tmp/debugger_index.html, /tmp/debugger_main.py
- 服务器：/root/.openclaw/debugger/
- 访问：http://43.160.242.46/debugger/ (token: x_w-NfYtLw57mkbsipyifd9WIoGUR8vP)

## 阻塞项

Ralph loop 仍在运行，iteration 2 继续中
