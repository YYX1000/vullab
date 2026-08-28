# wp2shell（CVE-2026-63030 & CVE-2026-60137）— SQLi → RCE 全链路靶场

WordPress REST batch 路由混淆导致**未认证 SQL 注入 → 管理员接管 → RCE** 的全链路靶场。

环境复用 [`labs/cms/wordpress`](../../cms/wordpress) 的官方镜像 + `.env` 切版本模式，无需本地构建。

## 受影响版本

| 版本范围      | 状态       |
|---------------|------------|
| <= 6.8.5      | 不受影响   |
| 6.9.0 – 6.9.4 | **受影响** |
| 7.0.0 – 7.0.1 | **受影响** |

默认 `7.0.1`（受影响）；`.env` 可切 `6.9.x` 线受影响版本或 `7.0.2`（修复版对照）。

## 启动

```bash
cp .env.example .env   # 首次使用
docker compose up -d
```

## 安装

访问 <http://127.0.0.1:8093> 按向导完成安装：

- 数据库地址：**`mysql`**
- 数据库 / 账密：`wordpress / wordpress / wordpress`（见 `.env`，root 密码 `root`）

## 漏洞验证

```bash
cd wp2shell-poc
python3 wp2shell.py check http://127.0.0.1:8093
```

[漏洞细节与 PoC 用法](./wp2shell-poc/README.md)

## 切换受影响版本

编辑 `.env` 的 `WORDPRESS_IMAGE` 后 `docker compose up -d`（跨版本建议先 `docker compose down`）：

```bash
WORDPRESS_IMAGE=wordpress:7.0.1-php8.4-apache   # 7.0.1（默认，受影响）
WORDPRESS_IMAGE=wordpress:6.9.4-php8.3-apache   # 6.9 线末期（受影响）
WORDPRESS_IMAGE=wordpress:6.9-php8.3-apache     # 6.9.0（受影响）
WORDPRESS_IMAGE=wordpress:7.0.2-php8.4-apache   # 修复版本（对照）
```

## 常用命令

```bash
docker compose logs -f wordpress   # 查看站点日志
docker compose down                # 停止并移除容器
docker compose down             # 停止并移除容器（数据库数据随之清除）
```
