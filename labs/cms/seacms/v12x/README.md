# SeaCMS 12.x 时代环境（PHP7.4 + MySQL5.7）

对应 SeaCMS **12.x 发展阶段**（2021–2022）：注入类问题持续存在的时期。

- 默认 `12.9`（12 线末期版本）
- 可切换 `12.5`

## 架构

```
mysql ── 内部（MySQL 5.7，数据卷持久化）
web   ── PHP7.4 + apache，构建时按 .env 注入的地址拉取完整安装包（解压取 */Upload/*）
```

对外仅暴露 `WEB_PORT`。

## 启动

```bash
cp .env.example .env   # 首次使用
docker compose up -d --build
```

## 访问

- 安装向导：<http://localhost:8091>
- 数据库地址填 **`mysql`**，库名 / 账密：`seacms_db / seacms / seacms123`（见 `.env`）
- 管理后台：安装完成后访问 `/admin.php`

## 切换 SeaCMS 版本

将 `.env.example` 复制为 `.env` 后编辑 `SEACMS_ZIP_URL`：

```bash
SEACMS_ZIP_URL=https://raw.githubusercontent.com/seacms-net/CMS/master/SeaCMS_12.9_%E6%B5%B7%E6%B4%8BCMS%E5%AE%89%E8%A3%85%E5%8C%85.zip  # v12.9（默认）
SEACMS_ZIP_URL=https://raw.githubusercontent.com/seacms-net/CMS/v12.5/%E6%B5%B7%E6%B4%8BCMS_%E5%AE%89%E8%A3%85%E5%8C%85_v12.5.zip      # v12.5
```

然后 `docker compose up -d --build` 重新构建；**跨版本切换建议先 `docker compose down -v` 清空数据卷**。

## 常用命令

```bash
docker compose logs -f web      # 查看站点日志
docker compose down             # 停止并移除容器
docker compose down -v          # 停止并移除容器与数据卷（清空数据库）
```
