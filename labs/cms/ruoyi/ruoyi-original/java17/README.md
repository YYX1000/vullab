# RuoYi-Vue 官方原版 · java17 环境（Docker）

基于官方 [RuoYi-Vue](https://github.com/yangzongzhuan/RuoYi-Vue)（前后端分离版）`springboot3` 分支的一键 Docker 环境。

- JDK17 + SpringBoot3（jakarta 体系）
- 后端：官方 `springboot3` 分支（v3.9.2）
- 前端：独立仓库 [RuoYi-Vue2](https://github.com/yangzongzhuan/RuoYi-Vue2)（Vue2 + Element UI + Vue CLI）

> springboot3 分支已将前端拆分为独立仓库，因此本环境前端与后端从两个仓库分别拉取构建。

## 架构

```
mysql    ── 内部（初始化导入 ry-vue 库）
redis    ── 内部（验证码 / 缓存 / 会话）
backend  ── SpringBoot3 后端（内部 8080，不对外暴露）
frontend ── nginx 托管 Vue 前端，并反向代理 /prod-api → backend
```

对外仅暴露前端 `WEB_PORT`，前端页面与后端接口统一经 nginx 访问。

## 启动

```bash
cp .env.example .env   # 首次使用
docker compose up -d --build
```

首次构建会拉取源码、Maven 打包并 npm 编译前端，耗时较长。

## 访问

- 前端入口：<http://localhost:8081>
- 默认账号：`admin / admin123`

## 切换版本

将 `.env.example` 复制为 `.env` 后编辑本目录 `.env`，修改 `RUOYI_BRANCH`（后端）或 `FRONTEND_BRANCH`（前端）：

```bash
RUOYI_BRANCH=springboot3    # SpringBoot 3.x + JDK17（默认）
FRONTEND_BRANCH=master      # RuoYi-Vue2 前端（默认）
```

然后重新构建：

```bash
docker compose up -d --build
```

> ⚠️ 注意：`sql/init-mysql.sql` 对应 `springboot3`（v3.9.2）的表结构。
> 切换后端版本时，请同步替换 `sql/init-mysql.sql` 为对应版本的 `quartz.sql + ry_*.sql`
> （不同版本表结构存在差异，如 `sys_job_log`、`sys_notice_read` 等）。

## 配置说明

| 文件 | 用途 |
|------|------|
| `.env.example` | 后端/前端仓库与版本、端口、数据库等变量模板（复制为 `.env` 使用，`.env` 不入库） |
| `config/application.yml` | 后端主配置（已修正 Linux 上传路径与 redis 地址），compose 挂载进容器 |
| `sql/init-mysql.sql` | MySQL 初始化脚本（首次启动自动导入） |
| `nginx/nginx.conf` | 前端 nginx 反向代理配置 |

## 常用命令

```bash
docker compose logs -f backend    # 查看后端日志
docker compose logs -f frontend   # 查看前端日志
docker compose down               # 停止并移除容器
docker compose down -v            # 停止并移除容器与数据卷（清空数据库）
```
