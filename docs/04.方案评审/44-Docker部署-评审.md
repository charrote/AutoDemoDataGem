# 44 · Docker部署 — 评审

> 评审对象：`docs/03.实现/44-Docker部署.md`（对应《02.技术实现方案.md》§9 任务 4.4）
> 评审人：Agent-395-Hermes ｜ 日期：2026-09-21
> 评审维度：合理性 / 实现与运营风险 / 后续成本（统一评审报告见 [README.md](./README.md)）

## 结论

**⚠️ 有条件通过** — 单机 Compose + 独立端口 + 与 MES-v24 隔离的部署模型合理；但「同机 nat.ywapi.com」是未确认假设，且开放注册 + 公网暴露的组合需要安全加固。

## 1. 合理性

- 单机 Docker Compose（web + api + db 按需）对 MVP 够用，方案已预留「量上来再上 Celery/多实例」的扩展点。
- 「独立 Compose 项目 + 独立端口 8860/8861，与 MES-v24 命名空间隔离」——同机多项目部署的隔离思路正确。
- .env 密钥（VAULT_KEY / JWT_SECRET / MYSQL_ROOT_PASSWORD）gitignore + 不设不生效，安全底线正确。

## 2. 实现风险

- **「部署到 nat.ywapi.com（与 MES-v24 同机）是未确认假设**：方案 §8.4 写「默认同机，待确认是否同机」。若实际不是同机，或同机但资源紧张（MES-v24 + 本工具 + QM-AI 三套 MySQL/容器），部署前必须确认：
  - 目标机器的 CPU/内存/磁盘余量（MySQL 8.0 单实例约 1-2GB 内存）；
  - 端口 8860/8861/3306 是否被 MES-v24 占用（3306 冲突概率高）；
  - 磁盘余量（MySQL 数据 + 镜像 + 日志）。
  建议：本卡开工前加一个「部署环境确认」前置项（机器余量 + 端口占用）。
- **Nginx 反代 /api → api:8000**：若 nginx 是 MES-v24 的 nginx（同一台），要确认是「新增 server 块」还是「独立 nginx 容器」。方案 compose 里 web 是独立 nginx 容器（8860），那反代是 web 容器内的 nginx 做的，不依赖宿主 nginx——这个要在卡片里写明（避免与 MES-v24 的 nginx 混淆）。
- **HTTPS/TLS**：方案未提 HTTPS。若 nat.ywapi.com 是公网域名，JWT 存 localStorage + HTTP 明文 = 凭证可被中间人截获。建议：阶段 4 对外开放前必须上 HTTPS（nginx 证书）；MVP 内部用可 HTTP，但要标注。
- **健康检查 GET /api/health**：compose 里 api 服务要加 healthcheck（依赖 db 时），避免 db 未就绪时 api 被误判健康。

## 3. 运营风险

- **开放注册 + 公网 = 真实攻击面**（R9）：注册即用（10）+ 公网部署（44）+ JWT localStorage = 批量注册/撞库/XSS 的组合风险。MVP 内部用（内网/白名单）可接受，**阶段 4「开放注册上线」前必须**：
  - HTTPS；
  - 注册限流（同 IP 限次）；
  - 登录限流（防撞库）；
  - 考虑邮箱验证（防垃圾注册）。
  这些不在 MVP 范围，但要作为阶段 4 的前置 checklist 写进 README。
- **与 MES-v24 的资源竞争**：同机多套 MySQL，内存/CPU 竞争。演示高峰期（多个系统同时演示）可能拖慢。运营上建议：演示日提前检查机器负载。
- **日志/备份**：MySQL 数据（users/systems/runs/manifest）是 SaaS 的核心资产，方案未提备份。建议：加 `mysqldump` 定时备份（crontab）到本地，MVP 最低限度。

## 4. 后续成本

- 部署是一次性的，但**运维是持续成本**：
  - .env 密钥管理（谁持有、备份在哪、轮换）；
  - MySQL 备份/恢复演练；
  - 镜像更新（漏洞 patch）；
  - 磁盘监控（MySQL 数据增长）。
  MVP 单机可接受，但要建立最低运维 SOP（备份 + 磁盘监控 + 密钥保管）。
- 阶段 4 对外开放后的安全加固成本（HTTPS/限流/邮箱验证）——已知，预算在阶段 4。

## 5. 整改建议

| # | 建议 | 优先级 |
|---|------|--------|
| 1 | 开工前加「部署环境确认」：机器余量 + 端口占用（尤其 3306） | 高 |
| 2 | 阶段 4 前置 checklist：HTTPS + 注册/登录限流 + 邮箱验证 | 高 |
| 3 | 加 MySQL 定时备份（crontab mysqldump）+ 密钥保管 SOP | 中 |
| 4 | 写明 nginx 反代在 web 容器内（不依赖宿主 nginx） | 低 |
