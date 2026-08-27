# ruoyi-vue-pro · java8 环境（Docker）

基于 [ruoyi-vue-pro](https://github.com/YunaiV/ruoyi-vue-pro) `master` 分支的一键 Docker 环境。

- JDK8 + SpringBoot2.7 + **SpringSecurity**
- 后端：`YunaiV/ruoyi-vue-pro` 分支 `master`（端口 48080）
- 前端：独立仓库 [yudao-ui-admin-vue3](https://github.com/yudaocode/yudao-ui-admin-vue3)（Vue3 + ElementPlus + Vite + pnpm）

> 芋道源码开源版：后端仅打包 `yudao-server` 及其依赖的 system / infra 两模块。

## 架构

```
mysql    ── 内部（初始化导入 ruoyi-vue-pro 库）
redis    ── 内部（验证码 / 缓存 / 会话）
backend  ── SpringBoot2.7 后端（内部 48080，不对外暴露）
frontend ── nginx 托管 Vue 前端，并反向代理 /admin-api → backend（保留前缀）
```

对外仅暴露前端 `WEB_PORT`，前端页面与后端接口统一经 nginx 访问。
后端数据源 / Redis 配置由 compose 环境变量注入（无 config/ 挂载）。

## 启动

```bash
cp .env.example .env
docker compose up -d --build
```

首次构建会拉取源码、Maven 打包并 pnpm 编译前端，耗时较长。

## 访问

- 前端入口：<http://localhost:8084>
- 默认账号：`admin / admin123`

## 切换版本

编辑本目录 `.env`，修改 `YUDAO_BRANCH`（后端）：

```bash
YUDAO_BRANCH=master   # JDK8 + SpringBoot2.7 + SpringSecurity（默认）
```

> 切换为 JDK17 体系请使用本目录下 `java17` 变体（`master-jdk17` 分支）。

## 配置说明

| 文件 | 用途 |
|------|------|
| `.env.example` | 后端/前端仓库与分支、端口、数据库等变量模板（复制为 `.env` 使用，`.env` 不入库） |
| `docker-compose.yml` | 四服务编排，后端环境变量覆盖 dynamic-datasource 与 redis |
| `sql/init-mysql.sql` | MySQL 初始化脚本（首次启动自动导入） |
| `nginx/nginx.conf` | 前端 nginx 反向代理配置 |

## 说明

- 前端生产构建默认将接口指向 `http://localhost:48080`，本环境在构建时通过
  `.env.prod.local` 将 `VITE_BASE_URL` 置空，使浏览器请求走同源 `/admin-api`，
  由 nginx 反代到后端，符合「后端不对外暴露」基线。

## 常用命令

```bash
docker compose logs -f backend    # 查看后端日志
docker compose logs -f frontend   # 查看前端日志
docker compose down               # 停止并移除容器
docker compose down -v            # 停止并移除容器与数据卷（清空数据库）
```
