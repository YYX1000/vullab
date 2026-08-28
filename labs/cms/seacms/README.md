# SeaCMS（海洋CMS）多版本 Docker 环境

> 项目总文档 · 发展历程 · 版本对照表 · 法律免责 · 整体使用说明

本仓库汇集 SeaCMS 按**发展历程划分的四个时代环境**（6.x 前台 GetShell 时代 / 10.x SQL 注入时代 / 12.x 时代 / 13.x 当前版本线），每个子目录独立部署、互不干扰。通过 `.env` 修改源码下载地址即可在时代内切换版本，用于**授权安全研究、漏洞复现与学习测试**。

---

## 一、目录结构

```
labs/cms/seacms
├── README.md                  # 项目总文档、发展历程、版本对照表、法律免责
├── v6x/                       # 6.x 时代：前台 GetShell 多发（PHP5.6 + MySQL5.6）
│   ├── .env.example           # ⭐版本变量模板，复制为 .env 后修改切换版本
│   ├── docker-compose.yml
│   ├── php/Dockerfile         # PHP5.6（jessie 归档源修复）+ 源码注入
│   └── README.md
├── v10x/                      # 10.x 时代：SQL 注入多发（PHP7.4 + MySQL5.7）
│   ├── .env.example
│   ├── docker-compose.yml
│   ├── php/Dockerfile
│   └── README.md
├── v12x/                      # 12.x 时代（PHP7.4 + MySQL5.7）
│   ├── .env.example
│   ├── docker-compose.yml
│   ├── php/Dockerfile
│   └── README.md
└── v13x/                      # 13.x 当前版本线（PHP7.4 + MySQL5.7）
    ├── .env.example
    ├── docker-compose.yml
    ├── php/Dockerfile
    └── README.md
```

> 每个变体目录统一包含 `.env.example`、`docker-compose.yml`、`php/Dockerfile`、`README.md` 四个文件。
> 源码在**构建时按 `.env` 注入的地址拉取**（官方 seacms.net 安装包 / GitHub 归档仓库），`.env` 不入库。

---

## 二、SeaCMS 发展历程与版本对照表

| 时代 | 年份 | 与漏洞研究相关的关键特征 |
|------|------|------|
| 6.x | 2016–2018 | 前台 GetShell 多发时代（v6.45 / v6.54 / v6.55 均有公开研究），PHP 5.x 运行时 |
| 9.x–10.x | 2019–2020 | search / type 等参数 SQL 注入多发 |
| 11.x–12.x | 2021–2022 | 后台与注入类问题持续，12.9 为 12 线末期版本 |
| 13.x | 2023–至今 | 当前官方维护线（官网仅保留 13.3–13.5 完整安装包），13.3 有公开 SQL 注入研究 |

| 目录 | 时代 | 默认版本 | 可切换版本 | PHP | MySQL | 默认端口 |
|------|------|----------|-----------|-----|-------|----------|
| `v6x/` | 6.x 时代 | 6.54 | 6.58（GitHub 归档） | 5.6 | 5.6 | 8089 |
| `v10x/` | 10.x 时代 | 10.1 | —（该线仅此版本可得） | 7.4 | 5.7 | 8090 |
| `v12x/` | 12.x 时代 | 12.9 | 12.5 | 7.4 | 5.7 | 8091 |
| `v13x/` | 13.x 当前线 | 13.3 | 13.4 / 13.5 | 7.4 | 5.7 | 8092 |

> 源码获取渠道（构建时自动下载）：
> - 官网安装包目录：`https://www.seacms.net/download/安装包/`（仅 13.3–13.5）
> - GitHub 归档 [`seacms-net/CMS`](https://github.com/seacms-net/CMS)：10.1 / 11.x / 12.5 / 12.9 / 13.0–13.5 完整安装包
> - GitHub 归档 `V1ntLyn/seacms6.54`、`MyPHPTools/seacms`（6.58）：6.x 源码树

---

## 三、整体使用说明

### 1. 环境要求

- Docker Engine 20.10+
- Docker Compose v2（`docker compose` 命令）
- 可访问 seacms.net / GitHub 的网络（构建时拉取源码）

### 2. 切换 SeaCMS 版本（重要）

每个子环境将 **`.env.example`** 复制为 **`.env`** 后，修改其中的源码地址变量即可切换版本：

```bash
cp .env.example .env
# 编辑 .env，例如（v13x 目录下）：
#   SEACMS_ZIP_URL=https://www.seacms.net/download/安装包/SeaCMS_V13.5_install.zip
```

修改后重新构建即可生效：

```bash
docker compose up -d --build
```

> ⚠️ 切换版本后建议 `docker compose down` 清空数据库再启动，
> 避免旧版本数据库结构残留影响新版本安装向导。

### 3. 快速启动

进入目标子环境目录，执行：

```bash
cp .env.example .env   # 首次使用
docker compose up -d --build
```

常用命令：

```bash
docker compose logs -f          # 查看日志
docker compose down             # 停止并移除容器
docker compose down          # 停止并移除容器（数据库数据存于容器层，随之清除）
```

### 4. 访问与初始化

- 访问 `http://<宿主机IP>:<对应端口>`（端口见版本对照表），按**安装向导**完成安装：
  - 数据库地址填 **`mysql`**（compose 内部服务名），数据库名 / 账号 / 密码见 `.env`（默认 `seacms_db / seacms / seacms123`）
- 管理后台：`http://<宿主机IP>:<对应端口>/admin.php`（或安装后按提示进入）
- SeaCMS 为 GBK 编码系统，页面与数据库编码保持默认即可

---

## 四、法律免责声明

> ⚠️ **本环境仅限授权安全测试、教学研究与个人学习使用。**

1. 本仓库提供的 Docker 环境仅用于**合法授权的渗透测试、漏洞复现、安全研究及教学**场景。
2. 使用者应确保在**拥有明确授权的目标环境**中开展测试，严格遵守《中华人民共和国网络安全法》《数据安全法》《个人信息保护法》及相关法律法规。
3. 严禁利用本环境及相关技术对**未经授权的系统、网站或服务**进行任何形式的攻击、入侵、破坏或数据窃取。
4. 因使用者违反法律法规或超出授权范围所产生的一切后果与法律责任，**由使用者自行承担**，与本项目及作者无关。
5. 本项目不提供任何漏洞利用工具或攻击脚本，仅提供目标运行环境，用于复现与验证已知漏洞。
