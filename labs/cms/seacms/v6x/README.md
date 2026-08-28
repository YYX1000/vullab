# SeaCMS 6.x 时代环境（PHP5.6 + MySQL5.6）

对应 SeaCMS **6.x 发展阶段**（2016–2018）：前台 GetShell 多发时代，v6.45 / v6.54 / v6.55 均有公开安全研究。

- 默认 `6.54`（PHP5.6）—— 前台 GetShell 公开研究覆盖版本
- 可切换 `6.58`（6 线后期版本）
- MySQL 5.6 与该时代常见部署一致

## 架构

```
mysql ── 内部（MySQL 5.6，数据卷持久化）
web   ── PHP5.6 + apache，构建时按 .env 注入的仓库地址拉取 6.x 源码树（仓库根即站点根）
```

对外仅暴露 `WEB_PORT`。

## 启动

```bash
cp .env.example .env   # 首次使用
docker compose up -d --build
```

> PHP5.6 基础镜像（Debian jessie）已归档，Dockerfile 内已自动切换 archive.debian.org 源，首次构建稍慢。

## 访问

- 安装向导：<http://localhost:8089>
- 数据库地址填 **`mysql`**，库名 / 账密：`seacms_db / seacms / seacms123`（见 `.env`）
- 管理后台：安装完成后访问 `/admin.php`

## 切换 SeaCMS 版本

将 `.env.example` 复制为 `.env` 后编辑 `SEACMS_REPO`：

```bash
SEACMS_REPO=https://github.com/V1ntLyn/seacms6.54.git   # v6.54（默认）
SEACMS_REPO=https://github.com/MyPHPTools/seacms.git    # v6.58
```

然后 `docker compose up -d --build` 重新构建；**跨版本切换建议先 `docker compose down -v` 清空数据卷**。

## 常用命令

```bash
docker compose logs -f web      # 查看站点日志
docker compose down             # 停止并移除容器
docker compose down -v          # 停止并移除容器与数据卷（清空数据库）
```
