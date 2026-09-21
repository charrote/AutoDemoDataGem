# 30 · QM-AI连接器

> 对应《02.技术实现方案.md》§9 任务 **3.0**
> **状态：未开始**（未开始 / 进行中 / 完成）  
> 负责人：孔明（调度）+ 待分配执行  
> 创建：2026-09-21

---

## 1. 目标

QM-AI 连接器端到端（DB + API 双通道）：主数据/历史走 DB + 流程态走 API + 触发重算。

## 2. 验收标准

- QM-AI 连接测试通过（DB + API）
- 场景可 build 出执行计划（含路由标签）
- Q1 写 API 清单 / Q2 DB schema 核实完毕（YOO 确认 Swagger，孔明抓取）

## 3. 范围

**In**
- 核实 Q1（写 API 清单 + 鉴权）/ Q2（DB schema：表/外键/字段语义）
- 填 schema.yaml（表/字段/外键/语义）
- 填 api_map.yaml（写接口/鉴权/调用顺序/依赖）
- 填 scenes/：inspection（检验全链路）、eight_d（8D）等
- 路由：主数据/历史检验→DB；活跃/SPC/8D 流程态→API；SPC/KPI/不良率→compute（触发重算+校验）

**Out**
- 其他连接器（EM-AI / MES，阶段 3）

## 4. 关键实现点

- **Q1 Swagger：由 YOO 确认，孔明负责抓取**（本地 QM-AI 有 qmai-full.sql + backend/ 源码，可先实测）
- 引擎与连接器解耦：核实后只改 QM-AI 配置（yaml），不动核心引擎
- 双通道是产品灵魂，必须证明流程态走 API 不露馅

## 5. 进度

- [ ] 核实 Q2：导出 QM-AI schema（qmai-full.sql / database 迁移）
- [ ] 核实 Q1：等 YOO 确认 Swagger → 孔明抓 swagger + 探写接口 + 鉴权
- [ ] 填 schema.yaml
- [ ] 填 api_map.yaml
- [ ] 写 scenes/（inspection / eight_d）
- [ ] 验证：连接测试通过 + 场景可 build 计划

## 6. 验证记录

| 时间 | 动作 | 结果 | 验证方式 |
|------|------|------|---------|
| - | - | - | - |

## 7. 审计日志

> 每次实质变更追加一行（时间 / 动作 / 结果 / 证据或链接）

- 2026-09-21 — 创建实现跟踪文件（待 YOO 审阅后开工）
