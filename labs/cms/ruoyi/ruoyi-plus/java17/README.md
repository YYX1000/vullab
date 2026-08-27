# RuoYi-Vue-Plus · java17 环境（Docker）

基于 [RuoYi-Vue-Plus](https://github.com/dromara/RuoYi-Vue-Plus) `v5.6.2` 的一键 Docker 环境。

- JDK17 + SpringBoot3 + **Sa-Token**（jakarta 体系）
- 后端：`dromara/RuoYi-Vue-Plus` tag `v5.6.2`
- 前端：独立仓库 [plus-ui](https://github.com/JavaLionLi/plus-ui) tag `v5.6.2-v2.6.2`（Vue3 + ElementPlus + Vite 7）

> v5.x 起前端已拆分为独立仓库 plus-ui，因此本环境前端与后端从两个仓库分别拉取构建。

## 架构

```
mysql    ── 内部（初始化导入 ry-vue 库）
redis    ── 内部（验证码 / 缓存 / 会话）
backend  ── SpringBoot3 后端（内部 8080，不对外暴露）
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

- 前端入口：<http://localhost:8083>
- 默认账号：`admin / admin123`

## 切换版本

编辑本目录 `.env`，修改 `RUOYI_BRANCH`（后端）与 `FRONTEND_BRANCH`（前端）：

```bash
RUOYI_BRANCH=v5.6.2            # v5.x 系列：JDK17 + SpringBoot3 + Sa-Token（默认）
FRONTEND_BRANCH=v5.6.2-v2.6.2  # plus-ui 对应 tag（v{后端}-v{前端} 格式）
```

然后重新构建：

```bash
docker compose up -d --build
```

> ⚠️ 注意：`sql/init-mysql.sql` 对应 `v5.6.2` 的表结构。
> 切换后端版本时，请同步替换 `sql/init-mysql.sql` 为对应版本的
> `script/sql/ry_vue_5.X.sql + ry_job.sql`。

## 配置说明

| 文件 | 用途 |
|------|------|
| `.env.example` | 后端/前端仓库与版本、端口、数据库等变量模板（复制为 `.env` 使用，`.env` 不入库） |
| `docker-compose.yml` | 四服务编排，后端环境变量覆盖 dynamic-datasource 与 redis |
| `sql/init-mysql.sql` | MySQL 初始化脚本（首次启动自动导入） |
| `nginx/nginx.conf` | 前端 nginx 反向代理配置 |

## 说明

- v5 定时任务采用 SnailJob，其服务端**未随本环境启动**（`snail-job.server` 指向
  127.0.0.1:17888 不可达），客户端仅记录重连日志，不影响登录与增删改查。
- 前端接口加密（`VITE_APP_ENCRYPT=true`）与后端 `api-decrypt` 默认密钥配对，
  开箱即用，无需额外配置。

## 常用命令

```bash
docker compose logs -f backend    # 查看后端日志
docker compose logs -f frontend   # 查看前端日志
docker compose down               # 停止并移除容器
docker compose down -v            # 停止并移除容器与数据卷（清空数据库）
```
