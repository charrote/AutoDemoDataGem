# 12 · 系统CRUD与DB连接测试

> 对应《02.技术实现方案.md》§9 任务 **1.2**
> **状态：未开始**（未开始 / 进行中 / 完成）  
> 负责人：孔明（调度）+ 待分配执行  
> 创建：2026-09-21

---

## 1. 目标

接入目标 Demo 系统（DB 连接）：systems 管理 + DB 连接测试（探 schema + 抽样）。

## 2. 验收标准

- 填错连接：报错清晰（区分网络/认证/库不存在）
- 正确连接：探出 schema（列名/类型/主键/外键）+ 抽样 N 行 → 回健康报告
- schema 探测结果缓存到 systems.schema_cache（JSON 列）

## 3. 范围

**In**
- systems 表（name / system_type / db_driver / db_conn_enc / api_base / api_auth_enc / schema_cache / swagger_cache / configurable_ifaces）
- DBDriver 抽象（mysql / sqlite / postgres）+ 参数化 SQL
- 连接测试（建连接→探 schema→抽样）
- 健康报告结构

**Out**
- API 连接测试（1.3）
- 真实数据灌入

## 4. 关键实现点

- 目标库一律参数化 SQL，不拼字符串
- 驱动适配层解耦不同目标库

## 5. 进度

- [ ] 建 systems 表 + CRUD
- [ ] 实现 DBDriver 抽象（mysql/sqlite/postgres）
- [ ] 实现 DB 连接测试（探 schema+抽样）
- [ ] 实现健康报告 + schema_cache 缓存
- [ ] 验证：错连接报错清晰 / 对连接回健康报告

## 6. 验证记录

| 时间 | 动作 | 结果 | 验证方式 |
|------|------|------|---------|
| - | - | - | - |

## 7. 审计日志

> 每次实质变更追加一行（时间 / 动作 / 结果 / 证据或链接）

- 2026-09-21 — 创建实现跟踪文件（待 YOO 审阅后开工）
