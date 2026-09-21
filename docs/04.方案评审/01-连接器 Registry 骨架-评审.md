# 01 · 连接器 Registry 骨架 — 评审

> 评审对象：`docs/03.实现/01-连接器 Registry 骨架.md`（对应《02.技术实现方案.md》§9 任务 0.1）
> 评审人：Agent-395-Hermes ｜ 日期：2026-09-21
> 评审维度：合理性 / 实现与运营风险 / 后续成本（统一评审报告见 [README.md](./README.md)）

## 结论

**✅ 通过** — 插件注册机制设计合理，无阻塞项；两处实现细节建议补齐。

## 1. 合理性

- manifest 驱动注册、「加包即扩展」直接兑现产品「支持新系统 = 加一个连接器」的承诺。
- BaseConnector 接口集合（routes / build / validate_connection / reset / trigger_recompute）覆盖引擎对连接器的全部诉求，职责边界清晰。

## 2. 实现风险

- 扫描方式未指定（文件系统扫描 vs entry_points）。建议 MVP 用 `importlib` 扫描 `connectors/` 包；**manifest 非法（缺 system_type / 重复 system_type）应启动期报错**，不能静默跳过，否则「空插件注册成功」的验收无法与「坏插件被吞掉」区分。
- 插件进程内共享：一个连接器的异常可能拖垮 API 进程。Registry 本身不处理，但应声明边界：插件异常由编排器（21）捕获并转为 Run 失败，不崩溃进程。

## 3. 运营风险

低。

## 4. 后续成本

低。Registry 一次性；后续扩展成本在连接器侧（每系统一个包），正是设计目的。

## 5. 整改建议

| # | 建议 | 优先级 |
|---|------|--------|
| 1 | 指定 importlib 扫描 + manifest 校验（非法即启动错误，不静默跳过） | 中 |
| 2 | 提供 registry 健康接口：列出已注册连接器 + manifest 解析状态 | 低 |
