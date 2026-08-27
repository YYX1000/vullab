# RuoYi-Vue-Plus · java8 环境（Docker）

基于 [RuoYi-Vue-Plus](https://github.com/dromara/RuoYi-Vue-Plus) `v4.8.3` 的一键 Docker 环境。

- JDK8 + SpringBoot2.7 + **Sa-Token**（非 Shiro）
- 后端：`dromara/RuoYi-Vue-Plus` tag `v4.8.3`
- 前端：同仓库 `ruoyi-ui-vue3` 子目录（Vue3 + ElementPlus + Vite），与后端同 tag

## 架构

```
mysql    ── 内部（初始化导入 ry-vue 库）
redis    ── 内部（验证码 / 缓存 / 会话）
backend  ── SpringBoot2.7 后端（内部 8080，不对外暴露）
frontend ── nginx 托管 Vue 前端，并反向代理 /prod-api → backend
```

对外仅暴露前端 `WEB_PORT`，前端页面与后端接口统一经 nginx 访问。
后端数据源 / Redis 配置由 compose 环境变量注入（无 config/ 挂载）。

## 启动

```bash
cp .env.example .env
docker compose up -d --build
```

首次构建会拉取源码、Maven 打包并 npm 编译前端，耗时较长。

## 访问

- 前端入口：<http://localhost:8082>
- 默认账号：`admin / admin123`

## 切换版本

编辑本目录 `.env`，修改 `RUOYI_BRANCH`（后端与前端同仓库同 tag）：

```bash
RUOYI_BRANCH=v4.8.3   # v4.x 系列：JDK8 + SpringBoot2.7 + Sa-Token（默认）
```

然后重新构建：

```bash
docker compose up -d --build
```

> ⚠️ 注意：`sql/init-mysql.sql` 对应 `v4.8.3` 的表结构。
> 切换后端版本时，请同步替换 `sql/init-mysql.sql` 为对应版本的
> `script/sql/ry_vue_4.X.sql + tables_xxl_job.sql`。

## 配置说明

| 文件 | 用途 |
|------|------|
| `.env.example` | 后端仓库/版本、端口、数据库等变量模板（复制为 `.env` 使用，`.env` 不入库） |
| `docker-compose.yml` | 四服务编排，后端环境变量覆盖 dynamic-datasource 与 redis |
| `sql/init-mysql.sql` | MySQL 初始化脚本（首次启动自动导入） |
| `nginx/nginx.conf` | 前端 nginx 反向代理配置 |

## 说明

- 项目内置 XXL-JOB 调度中心（端口 9100）**未随本环境启动**，`xxl.job.enabled` 保持默认
  时执行器注册失败仅记录日志，不影响登录与增删改查。
- `spring.boot.admin.client` 监控中心（端口 9090）同样未启动，仅日志提示，不影响业务。

## 常用命令

```bash
docker compose logs -f backend    # 查看后端日志
docker compose logs -f frontend   # 查看前端日志
docker compose down               # 停止并移除容器
docker compose down -v            # 停止并移除容器与数据卷（清空数据库）
```
