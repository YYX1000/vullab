# SeaCMS 10.x 时代环境（PHP7.4 + MySQL5.7）

对应 SeaCMS **10.x 发展阶段**（2019–2020）：search / type 等参数 SQL 注入多发时期。

- 默认 `10.1` —— 10 线唯一可获得完整安装包的版本（GitHub 归档）

## 架构

```
mysql ── 内部（MySQL 5.7，数据存容器层）
web   ── PHP7.4 + apache，构建时按 .env 注入的地址拉取完整安装包（解压取 */Upload/*）
```

对外仅暴露 `WEB_PORT`。

## 启动

```bash
cp .env.example .env   # 首次使用
docker compose up -d --build
```

## 访问

- 安装向导：<http://localhost:8090>
- 数据库地址填 **`mysql`**，库名 / 账密：`seacms_db / seacms / seacms123`（见 `.env`）
- 管理后台：安装完成后访问 `/admin.php`

## 版本说明

10.x 线公开归档仅 `10.1` 一个完整安装包，本变体不做版本切换；
相邻时代请使用 `v6x/`（6.x）或 `v12x/`（12.x）变体。

## 常用命令

```bash
docker compose logs -f web      # 查看站点日志
docker compose down             # 停止并移除容器
docker compose down          # 停止并移除容器（数据库数据随之清除）
```
