# 若依（RuoYi）多版本 Docker 环境

> 项目总文档 · 版本对照表 · 法律免责 · 整体使用说明

本仓库汇集若依（RuoYi）三大体系、多 JDK/SpringBoot 版本的 Docker 一键部署环境，覆盖官方原版、社区增强版与芋道 Pro 版本，每个子目录独立部署、互不干扰，用于**授权安全研究、漏洞复现与学习测试**。

---

## 一、目录结构

```
labs/cms/ruoyi
├── README.md                        # 项目总文档、版本对照表、法律免责、整体使用说明
├── common/
│   └── maven-settings.xml           # 全局 Maven 国内镜像配置，所有 backend 构建复用
│
├── ruoyi-original/                  # 官方原版 RuoYi-Vue
│   ├── java8/                       # SpringBoot2 + JDK8（v3.7.0 Shiro / v3.9.0 SpringSecurity-JWT）
│   │   ├── .env.example             # ⭐版本变量模板，复制为 .env 后修改 RUOYI_BRANCH 切换版本
│   │   ├── docker-compose.yml
│   │   ├── README.md                # 当前环境独立使用文档
│   │   ├── backend/Dockerfile       # 容器内 git clone + maven 打包；JDK8 镜像
│   │   ├── frontend/Dockerfile      # 容器内 git clone + npm build 编译前端
│   │   ├── nginx/nginx.conf         # nginx 反向代理配置
│   │   ├── config/application.yml   # ruoyi 后端配置文件，compose 挂载进容器
│   │   └── sql/init-mysql.sql       # MySQL 初始化脚本
│   └── java17/                      # SpringBoot3 + JDK17（springboot3 分支，jakarta 体系）
│       └── ...（目录结构同 java8）
│
├── ruoyi-plus/                      # Dromara RuoYi-Vue-Plus
│   ├── java8/                       # v4.8.3 + JDK8 + SpringBoot2.7 + Sa-Token
│   │   └── ...（同 ruoyi-original，但**无 config/ 挂载**，后端配置经 compose env 注入）
│   └── java17/                      # v5.6.2 + JDK17 + SpringBoot3 + Sa-Token
│       └── ...（同 java8）
│
└── ruoyi-yudao-pro/                 # 芋道 RuoYi-Vue-Pro
    ├── java8/                       # master + JDK8 + SpringBoot2.7
    │   └── ...（同 ruoyi-plus，后端配置经 compose env 注入，无 config/ 挂载）
    └── java17/                      # master-jdk17 + JDK17 + SpringBoot3
        └── ...（同 java8）
```

> 每个变体目录统一包含 `.env.example`、`docker-compose.yml`、`README.md`、
> `backend/Dockerfile`、`frontend/Dockerfile`、`nginx/nginx.conf`、`sql/init-mysql.sql` 七个文件。
> 其中 `.env`（由 `.env.example` 复制）不入库；`ruoyi-original` 额外有 `config/application.yml`
> 挂载，plus / yudao 后端配置（数据源 / Redis）全部经 compose 环境变量注入。

---

## 二、版本对照表

| 目录 | 项目 | 分支 / 版本 | JDK | Spring Boot | 安全框架 | 前端仓库 | 默认前端端口 |
|------|------|-------------|-----|-------------|----------|----------|-------------|
| `ruoyi-original/java8` | RuoYi-Vue 官方原版 | v3.7.0（Shiro）/ v3.9.0（SpringSecurity-JWT），通过 `RUOYI_BRANCH` 切换 | 8 | 2.x | Shiro / SpringSecurity-JWT | RuoYi-Vue（仓库内 `ruoyi-ui`） | 8080 |
| `ruoyi-original/java17` | RuoYi-Vue 官方原版 | springboot3 | 17 | 3.x | SpringSecurity（jakarta 体系） | RuoYi-Vue2（独立仓库） | 8081 |
| `ruoyi-plus/java8` | RuoYi-Vue-Plus（Dromara） | v4.8.3 | 8 | 2.7 | **Sa-Token** | 仓库内 `ruoyi-ui-vue3`（Vue3+Vite） | 8082 |
| `ruoyi-plus/java17` | RuoYi-Vue-Plus（Dromara） | v5.6.2 | 17 | 3.x | Sa-Token | plus-ui（独立仓库 `JavaLionLi/plus-ui`） | 8083 |
| `ruoyi-yudao-pro/java8` | 芋道 RuoYi-Vue-Pro | master | 8 | 2.7 | SpringSecurity | yudao-ui-admin-vue3（独立仓库） | 8084 |
| `ruoyi-yudao-pro/java17` | 芋道 RuoYi-Vue-Pro | master-jdk17 | 17 | 3.x | SpringSecurity | yudao-ui-admin-vue3（独立仓库） | 8085 |

> 说明：以上端口为**默认规划**，如需调整请修改对应子目录下的 `docker-compose.yml`。具体版本差异与分支细节以各子目录 `README.md` 为准。
> `ruoyi-plus/java8` 安全框架为 **Sa-Token**（非 Shiro），`v4.x` 起若依 Plus 已全面改用 Sa-Token。

---

## 三、整体使用说明

### 1. 环境要求

- Docker Engine 20.10+
- Docker Compose v2（`docker compose` 命令）
- 可访问 GitHub / Gitee 的构建网络（首次构建需拉取源码与依赖）

### 2. 全局 Maven 镜像配置

`common/maven-settings.xml` 提供了国内 Maven 镜像配置，所有 backend 的 Dockerfile 构建均复用该文件，无需在每个环境重复配置。

### 3. 切换若依版本（重要）

每个子环境将 **`.env.example`** 复制为 **`.env`** 后修改其中的版本变量即可切换分支/版本
（变量名因体系而异：`ruoyi-original` 用 `RUOYI_BRANCH`，`ruoyi-plus` 用 `RUOYI_BRANCH`，
`ruoyi-yudao-pro` 用 `YUDAO_BRANCH`）：

```bash
cp .env.example .env
# 编辑 .env，例如：
#   RUOYI_BRANCH=v3.9.0        # ruoyi-original / ruoyi-plus
#   YUDAO_BRANCH=master-jdk17  # ruoyi-yudao-pro
```

> `.env` 已被根目录 `.gitignore` 忽略，仅提交 `.env.example` 模板（含占位值），避免密钥泄露。
> 前端仓库/分支、`WEB_PORT`、数据库账号密码等均可一并调整。

修改后重新执行构建命令即可生效。

### 4. 快速启动

进入目标子环境目录，执行：

```bash
cp .env.example .env   # 首次使用
docker compose up -d --build
```

常用命令：

```bash
docker compose logs -f          # 查看日志
docker compose down             # 停止并移除容器
docker compose down -v          # 停止并移除容器与数据卷（会清空数据库数据）
```

### 5. 访问与默认账号

- 前端访问：`http://<宿主机IP>:<对应端口>`（端口见版本对照表）
- 默认账号：`admin / admin123`（个别版本若不同，以子目录 README 为准）
---

## 四、法律免责声明

> ⚠️ **本环境仅限授权安全测试、教学研究与个人学习使用。**

1. 本仓库提供的 Docker 环境仅用于**合法授权的渗透测试、漏洞复现、安全研究及教学**场景。
2. 使用者应确保在**拥有明确授权的目标环境**中开展测试，严格遵守《中华人民共和国网络安全法》《数据安全法》《个人信息保护法》及相关法律法规。
3. 严禁利用本环境及相关技术对**未经授权的系统、网站或服务**进行任何形式的攻击、入侵、破坏或数据窃取。
4. 因使用者违反法律法规或超出授权范围所产生的一切后果与法律责任，**由使用者自行承担**，与本项目及作者无关。
5. 本项目不提供任何漏洞利用工具或攻击脚本，仅提供目标运行环境，用于复现与验证已知漏洞。
