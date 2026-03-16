# Active Context

## 当前优先级

**AI Chronicle（AI 创业编年史）— 数据采集启动阶段**

## 最近的关键决策 (2026-03-17)

### AI Chronicle 项目启动
- 定位：ChatGPT 以来所有 AI 创业项目的时间叙事数据库
- Schema 已确认：公司名、成立时间、融资轮次(每轮)、投资人(每轮)、产品主页、创始团队及变化、milestones(关键事件时间线)
- 数据采集策略：Crunchbase API ($499/月订一个月批量导出) + IT桔子(中国) + ProductHunt/YC(免费补充)
- 预算：~10000 RMB / ~$1000 USD
- 差异化关键：milestones 字段（完整故事线，不只是融资数据）
- 目标用户：创业者、研究者、自己 — 可被 Agent 调用的数据库
- 技术方案：SQLite + JSON 存储，后续产品化再迁移

### Agent Cloud 项目（上次 session）
- 定位为 "Agent Reverse API Gateway"，Web UI first
- Plan 需要根据 "Web UI first" 重写后再执行

## 下一步行动

1. AI Chronicle: 选择 A/B/C 路径启动（建 repo + schema / 竞品研究 / 免费数据先试手）
2. Agent Cloud: 重新设计 plan，Web UI 先行

## 阻塞项

- AI Chronicle: 等用户决定下一步路径（A/B/C）

