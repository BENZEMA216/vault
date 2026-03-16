# Active Context

## 当前优先级

**AI Chronicle（AI 创业编年史）— 数据源调研完成，待建 repo**

## 最近的关键决策 (2026-03-17)

### AI Chronicle 数据源调研完成
- 完成 40 个数据源的全面调研（8 大类：付费数据库、免费开放、中国特有、政府学术、新闻媒体、AI 榜单、开源数据集、另类数据）
- **预算内最优组合确定**（~$500-800）：
  - 免费骨架：YC Directory + GitHub Awesome Lists + a16z Top 100 + AICPB + CB Insights AI 100 + Stanford AI Index
  - 付费核心：Crunchbase Pro ($49/月) + IT桔子 VIP (~2000 RMB/年)
  - 免费补充 milestones：36氪 PitchHub + 量子位 + TechCrunch + NewsAPI + SEC EDGAR + ProductHunt API
  - 按需：天眼查/企查查 API（工商验证）
- **差异化方向确认**：
  1. 中美数据打通（Crunchbase 不覆盖中国，IT桔子不覆盖美国）
  2. Milestones 时间线（现有平台只有融资数据，缺事件追踪）
  3. 产品维度（公司级 → 产品级追踪）
  4. 编年史叙事角度（vs 数据库查询体验）
- Schema 已确认：公司名、成立时间、融资轮次(每轮)、投资人(每轮)、产品主页、创始团队及变化、milestones

### Agent Cloud 项目（之前 session）
- Web UI first，Plan 待重写

## 下一步行动

1. **AI Chronicle: 建 GitHub repo + 定义正式 schema + 搭采集框架**
2. 先用免费数据源搭骨架（YC、GitHub lists、a16z Top 100）
3. 接 Crunchbase Pro + IT桔子 VIP 补融资数据
4. 竞品深度对比（Crunchbase/CB Insights 实际 AI 数据覆盖程度）

## 阻塞项

- 等用户确认开始建 repo

