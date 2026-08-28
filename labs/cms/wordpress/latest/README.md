# WordPress 当前主线环境（PHP8.4 + MySQL8.0）

对应 WordPress **当前主线**（6.x → 7.x）：块主题、全站编辑（FSE）成熟，PHP 8.x 全面适配。

- 默认 `7.1`（PHP8.4）—— 当前主线最新版本
- 可切回 `6.5.0` / `6.4.3`（PHP8.2）复现 6.x 线已知漏洞
- MySQL 8.0 与当前官方推荐配置一致

## 典型复现场景

| 漏洞 | 影响版本 | 复现版本 |
|------|----------|----------|
| CVE-2024-4439（Avatar 块存储型 XSS） | 6.0 – 6.5.x | `wordpress:6.4.3-php8.2-apache` |
| 插件/主题生态漏洞 | 视插件而定 | 切到对应主线版本后自行安装 |

## 架构

```
mysql      ── 内部（MySQL 8.0，数据存容器层）
wordpress  ── Docker Hub 官方镜像 apache 变体（仅对外暴露 WEB_PORT）
```

## 启动

```bash
cp .env.example .env   # 首次使用
docker compose up -d
```

## 访问

- 站点入口：<http://localhost:8088>
- 首次访问按安装向导完成安装（站点标题、管理员账号自建）
- 管理后台：`http://localhost:8088/wp-admin`
- 数据库：`wordpress / wordpress`（root 密码 `root`）

## 切换 WordPress 版本

将 `.env.example` 复制为 `.env` 后编辑 `WORDPRESS_IMAGE`：

```bash
WORDPRESS_IMAGE=wordpress:7.1.0-php8.4-apache
WORDPRESS_IMAGE=wordpress:7.1-php8.4-apache     # 当前主线最新（默认）
WORDPRESS_IMAGE=wordpress:6.5.0-php8.2-apache   # 6.5 线
WORDPRESS_IMAGE=wordpress:6.4.3-php8.2-apache   # 6.4 分支最终安全版本
```

然后 `docker compose up -d` 重新启动；**跨版本切换建议先 `docker compose down` 清空数据库**。

## 常用命令

```bash
docker compose logs -f wordpress   # 查看站点日志
docker compose down                # 停止并移除容器
docker compose down             # 停止并移除容器（数据库数据随之清除）
```
