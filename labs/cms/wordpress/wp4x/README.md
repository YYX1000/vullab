# WordPress 4.x 时代环境（PHP5.6 + MySQL5.6）

对应 WordPress **4.x 发展阶段**（2014–2017）：REST API 于 4.4 引入并逐步开放，4.7.0 起所有文章类型默认暴露 REST 端点。

- 默认 `4.7.1`（PHP5.6）—— **REST API 内容注入漏洞 CVE-2017-1001000** 漏洞版本
- 可切换 `4.7.2`（修复版对照）或 4.7 线其他版本
- MySQL 5.6 与该时代官方推荐配置一致

## 典型复现场景

| 漏洞 | 影响版本 | 复现版本 |
|------|----------|----------|
| CVE-2017-1001000 REST API 内容注入（未授权篡改文章内容） | 4.7.0 / 4.7.1 | 默认 `4.7.1`；对照 `4.7.2` |

## 架构

```
mysql      ── 内部（MySQL 5.6，数据存容器层）
wordpress  ── Docker Hub 官方镜像 apache 变体（仅对外暴露 WEB_PORT）
```

## 启动

```bash
cp .env.example .env   # 首次使用
docker compose up -d
```

## 访问

- 站点入口：<http://localhost:8086>
- 首次访问按安装向导完成安装（站点标题、管理员账号自建）
- 管理后台：`http://localhost:8086/wp-admin`
- 数据库：`wordpress / wordpress`（root 密码 `root`）

## 切换 WordPress 版本

将 `.env.example` 复制为 `.env` 后编辑 `WORDPRESS_IMAGE`：

```bash
WORDPRESS_IMAGE=wordpress:4.7.1-php5.6-apache   # CVE-2017-1001000 漏洞版本（默认）
WORDPRESS_IMAGE=wordpress:4.7.2-php5.6-apache   # 修复版本（对照复现）
WORDPRESS_IMAGE=wordpress:4.7.5-php5.6-apache   # 4.7 分支最终安全版本
```

然后 `docker compose up -d` 重新启动；**跨版本切换建议先 `docker compose down` 清空数据库**。

## 常用命令

```bash
docker compose logs -f wordpress   # 查看站点日志
docker compose down                # 停止并移除容器
docker compose down             # 停止并移除容器（数据库数据随之清除）
```
