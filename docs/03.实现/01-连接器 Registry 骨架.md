# 01 · 连接器 Registry 骨架

> 对应《02.技术实现方案.md》§9 任务 **0.1**
> **状态：未开始**（未开始 / 进行中 / 完成）  
> 负责人：孔明（调度）+ 待分配执行  
> 创建：2026-09-21

---

## 1. 目标

连接器插件注册/发现机制：一个 Demo 系统 = 一个 Python 包，注册进 Registry，按 system_type 路由。

## 2. 验收标准

- 空插件（仅 manifest.yaml + BaseConnector 子类）能被扫描并注册
- Registry 能按 system_type 查到对应连接器
- 新增一个包 = 支持一个新系统，无需改核心

## 3. 范围

**In**
- BaseConnector 抽象接口（routes / build / validate_connection / reset / trigger_recompute）
- Registry 扫描 connectors/ 包 + 解析 manifest.yaml（system_type / routes / scenes）
- 按 system_type 路由到连接器
- 插件目录约定：manifest.yaml / schema.yaml / api_map.yaml / scenes/*.yaml / connector.py

**Out**
- 具体连接器实现（3.0）
- 场景配方内容

## 4. 关键实现点

- 引擎与连接器解耦：连接器只声明 schema/路由/场景，核心引擎不感知具体系统
- manifest 驱动注册（加包即扩展）

## 5. 进度

- [ ] 定义 BaseConnector 接口
- [ ] 实现 Registry 扫描 + manifest 解析
- [ ] 实现按 system_type 路由
- [ ] 放一个空 QM-AI 包验证注册

## 6. 验证记录

| 时间 | 动作 | 结果 | 验证方式 |
|------|------|------|---------|
| - | - | - | - |

## 7. 审计日志

> 每次实质变更追加一行（时间 / 动作 / 结果 / 证据或链接）

- 2026-09-21 — 创建实现跟踪文件（待 YOO 审阅后开工）
