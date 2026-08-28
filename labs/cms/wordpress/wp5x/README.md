# WordPress 5.x 时代环境（PHP7.2 + MySQL5.7）

对应 WordPress **5.x 发展阶段**（2018–2022）：5.0 起 **Gutenberg 块编辑器**成为默认编辑器，REST API 与区块体系全面融入核心。

- 默认 `5.0.3`（PHP7.2）—— Gutenberg 首发版本线（5.0 线）最终安全版本
- 可切换 `5.0.2`、`5.5.0`、`5.8.3` 等版本
- MySQL 5.7 与该时代官方推荐配置一致

## 典型复现场景

| 漏洞 | 影响版本 | 复现版本 |
|------|----------|----------|
| Gutenberg 时代插件/主题生态漏洞 | 视插件而定 | 按需切换 5.x 线版本后自行安装对应插件 |

## 架构

```
mysql      ── 内部（MySQL 5.7，数据存容器层）
wordpress  ── Docker Hub 官方镜像 apache 变体（仅对外暴露 WEB_PORT）
```

## 启动

```bash
cp .env.example .env   # 首次使用
docker compose up -d
```

## 访问

- 站点入口：<http://localhost:8087>
- 首次访问按安装向导完成安装（站点标题、管理员账号自建）
- 管理后台：`http://localhost:8087/wp-admin`
- 数据库：`wordpress / wordpress`（root 密码 `root`）

## 切换 WordPress 版本

将 `.env.example` 复制为 `.env` 后编辑 `WORDPRESS_IMAGE`：

```bash
WORDPRESS_IMAGE=wordpress:5.0.2-php7.2-apache   # Gutenberg 首发 5.0 线
WORDPRESS_IMAGE=wordpress:5.0.3-php7.2-apache   # 5.0 线最终安全版本（默认）
WORDPRESS_IMAGE=wordpress:5.5.0-php7.4-apache   # 5.5 线
WORDPRESS_IMAGE=wordpress:5.8.3-php7.4-apache   # 5.8 分支最终安全版本
```

然后 `docker compose up -d` 重新启动；**跨版本切换建议先 `docker compose down` 清空数据库**。

## 常用命令

```bash
docker compose logs -f wordpress   # 查看站点日志
docker compose down                # 停止并移除容器
docker compose down             # 停止并移除容器（数据库数据随之清除）
```
