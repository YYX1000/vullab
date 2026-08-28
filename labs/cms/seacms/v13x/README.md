# SeaCMS 13.x 当前版本线环境（PHP7.4 + MySQL5.7）

对应 SeaCMS **13.x 当前维护阶段**（2023–至今）：官网安装包目录仅保留 13.3–13.5。

- 默认 `13.3` —— 有公开 SQL 注入研究
- 可切换 `13.4` / `13.5`（当前最新）

## 架构

```
mysql ── 内部（MySQL 5.7，数据存容器层）
web   ── PHP7.4 + apache，构建时按 .env 注入的地址拉取官方完整安装包（解压取 */Upload/*）
```

对外仅暴露 `WEB_PORT`。

## 启动

```bash
cp .env.example .env   # 首次使用
docker compose up -d --build
```

## 访问

- 安装向导：<http://localhost:8092>
- 数据库地址填 **`mysql`**，库名 / 账密：`seacms_db / seacms / seacms123`（见 `.env`）
- 管理后台：安装完成后访问 `/admin.php`

## 切换 SeaCMS 版本

将 `.env.example` 复制为 `.env` 后编辑 `SEACMS_ZIP_URL`：

```bash
SEACMS_ZIP_URL=https://www.seacms.net/download/%E5%AE%89%E8%A3%85%E5%8C%85/SeaCMS_V13.3_install.zip  # v13.3（默认）
SEACMS_ZIP_URL=https://www.seacms.net/download/%E5%AE%89%E8%A3%85%E5%8C%85/SeaCMS_V13.4_install.zip  # v13.4
SEACMS_ZIP_URL=https://www.seacms.net/download/%E5%AE%89%E8%A3%85%E5%8C%85/SeaCMS_V13.5_install.zip  # v13.5（当前最新）
```

然后 `docker compose up -d --build` 重新构建；**跨版本切换建议先 `docker compose down` 清空数据库**。

## 常用命令

```bash
docker compose logs -f web      # 查看站点日志
docker compose down             # 停止并移除容器
docker compose down          # 停止并移除容器（数据库数据随之清除）
```
