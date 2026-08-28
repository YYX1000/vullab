# WordPress 多版本 Docker 环境

> 项目总文档 · 发展历程 · 版本对照表 · 法律免责 · 整体使用说明

本仓库汇集 WordPress 按**发展历程划分的三个时代环境**（4.x 时代 / 5.x 时代 / 当前主线），每个子目录独立部署、互不干扰。通过 `.env` 修改镜像 tag 即可在时代内任意切换 WordPress 版本，用于**授权安全研究、漏洞复现与学习测试**。

---

## 一、目录结构

```
labs/cms/wordpress
├── README.md                  # 项目总文档、发展历程、版本对照表、法律免责
├── wp4x/                      # 4.x 时代：REST API 引入（PHP5.6 + MySQL5.6）
│   ├── .env.example           # ⭐版本变量模板，复制为 .env 后修改 WORDPRESS_IMAGE 切换版本
│   ├── docker-compose.yml
│   └── README.md
├── wp5x/                      # 5.x 时代：Gutenberg 块编辑器（PHP7.2 + MySQL5.7）
│   ├── .env.example
│   ├── docker-compose.yml
│   └── README.md
└── latest/                    # 当前主线：块主题 / 全站编辑（PHP8.4 + MySQL8.0）
    ├── .env.example
    ├── docker-compose.yml
    └── README.md
```

> 每个变体目录统一包含 `.env.example`、`docker-compose.yml`、`README.md` 三个文件。
> 直接使用 Docker Hub 官方镜像（apache 变体），无需构建；`.env` 由 `.env.example` 复制，不入库。

---

## 二、WordPress 发展历程与版本对照表

| WordPress 里程碑 | 年份 | 与漏洞研究相关的关键变化 |
|------|------|------|
| 3.x | 2010 | 自定义文章类型、主题体系成熟，插件生态爆发 |
| 4.x | 2014–2017 | **REST API 引入**（4.4），4.7.0/4.7.1 内容注入漏洞（CVE-2017-1001000） |
| 5.x | 2018–2022 | **Gutenberg 块编辑器**成为默认（5.0），REST API 全面化 |
| 6.x | 2022–2025 | 块主题、全站编辑（FSE），PHP 8.x 适配 |
| 7.x | 2026– | 当前主线，PHP 8.4 |

| 目录 | 时代 | 默认版本 | PHP | MySQL | 默认端口 |
|------|------|----------|-----|-------|----------|
| `wp4x/` | 4.x 时代 | `4.7.1`（CVE-2017-1001000 漏洞版本，可切 4.7.2 修复版对照） | 5.6 | 5.6 | 8086 |
| `wp5x/` | 5.x 时代 | `5.0.3`（Gutenberg 首发版本线） | 7.2 | 5.7 | 8087 |
| `latest/` | 当前主线 | `7.1`（可切回 6.4.3 / 6.5.0 复现 6.x 线漏洞） | 8.4 | 8.0 | 8088 |

> 说明：以上端口为**默认规划**，如需调整请修改对应子目录下 `.env` 的 `WEB_PORT`。
> 时代内 PHP 运行时可随镜像 tag 一并切换（如 `php5.6` → `php7.1`），具体可用 tag 见各子目录 `.env.example` 注释或 [Docker Hub 官方 tag 列表](https://hub.docker.com/_/wordpress/tags)。

---

## 三、整体使用说明

### 1. 环境要求

- Docker Engine 20.10+
- Docker Compose v2（`docker compose` 命令）
- 可访问 Docker Hub 的网络（直接拉取官方镜像，无需构建）

### 2. 切换 WordPress 版本（重要）

每个子环境将 **`.env.example`** 复制为 **`.env`** 后，修改其中的 **`WORDPRESS_IMAGE`** 镜像 tag 即可切换版本：

```bash
cp .env.example .env
# 编辑 .env，例如（wp4x 目录下）：
#   WORDPRESS_IMAGE=wordpress:4.7.2-php5.6-apache   # 切到修复版本做对照复现
```

修改后重新启动即可生效：

```bash
docker compose up -d
```

> ⚠️ 切换版本后建议 `docker compose down` 清空数据库再启动，
> 避免旧版本数据库结构/站点状态残留影响新版本安装向导。

### 3. 快速启动

进入目标子环境目录，执行：

```bash
cp .env.example .env   # 首次使用
docker compose up -d
```

常用命令：

```bash
docker compose logs -f          # 查看日志
docker compose down             # 停止并移除容器
docker compose down          # 停止并移除容器（数据库数据存于容器层，随之清除）
```

### 4. 访问与初始化

- 访问 `http://<宿主机IP>:<对应端口>`（端口见版本对照表），按**安装向导**选择语言 → 设置站点标题与管理员账号密码 → 完成安装
- 管理后台：`http://<宿主机IP>:<对应端口>/wp-admin`
- 数据库账号密码见 `.env`（默认 `wordpress / wordpress`，root 密码 `root`）

---

## 四、法律免责声明

> ⚠️ **本环境仅限授权安全测试、教学研究与个人学习使用。**

1. 本仓库提供的 Docker 环境仅用于**合法授权的渗透测试、漏洞复现、安全研究及教学**场景。
2. 使用者应确保在**拥有明确授权的目标环境**中开展测试，严格遵守《中华人民共和国网络安全法》《数据安全法》《个人信息保护法》及相关法律法规。
3. 严禁利用本环境及相关技术对**未经授权的系统、网站或服务**进行任何形式的攻击、入侵、破坏或数据窃取。
4. 因使用者违反法律法规或超出授权范围所产生的一切后果与法律责任，**由使用者自行承担**，与本项目及作者无关。
5. 本项目不提供任何漏洞利用工具或攻击脚本，仅提供目标运行环境，用于复现与验证已知漏洞。
