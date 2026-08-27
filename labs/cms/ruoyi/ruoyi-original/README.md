# RuoYi-Vue 官方原版（ruoyi-original）

> 本目录为若依（RuoYi-Vue）**官方原版**环境，与 `ruoyi-plus`（社区增强版）、`ruoyi-yudao-pro`（芋道 Pro 版）相互独立，用于授权安全研究、漏洞复现与学习测试。

---

## 一、目录结构

```
ruoyi-original/
├── java8/                 # JDK 8 + SpringBoot 2.x（若依 v3.x 系列）
│   ├── .env.example       # ⭐版本变量模板，复制为 .env 后修改 RUOYI_BRANCH 切换版本
│   ├── docker-compose.yml
│   ├── README.md          # java8 环境独立使用文档
│   ├── backend/           # JDK8 镜像，容器内 git clone + maven 打包
│   ├── frontend/          # 容器内 git clone + npm build 编译前端
│   ├── nginx/             # nginx 反向代理配置
│   ├── config/            # ruoyi 后端配置文件（compose 挂载）
│   └── sql/               # MySQL 初始化脚本
└── java17/                # JDK 17 + SpringBoot 3.x（springboot3 分支，jakarta 体系）
    └── ...（目录结构同 java8）
```

---

## 二、版本说明

若依官方原版（RuoYi-Vue）按 JDK 与 Spring Boot 版本划分为两大体系：

| 子目录 | JDK | Spring Boot | 分支 / 版本 | 安全框架 |
|--------|-----|-------------|-------------|----------|
| `java8` | 8 | 2.x | **v3.x 系列**（默认 v3.7.0 / v3.9.0） | Shiro / SpringSecurity-JWT |
| `java17` | 17 | 3.x | `springboot3` 分支 | SpringSecurity（jakarta 体系） |

- **从 v3.0.x 到 v3.9.x 使用的是 Java 8 版本**（Spring Boot 2.x）：
  - `v3.7.0` 及更早：Shiro 安全框架
  - `v3.9.0`：切换为 SpringSecurity + JWT
- **springboot3 分支**：升级为 JDK 17 + Spring Boot 3.x（jakarta 体系），对应 `java17` 环境。

---

## 三、切换若依版本（重要）

**需要修改具体版本时，将 `.env.example` 复制为 `.env` 后编辑**，通过 `RUOYI_BRANCH` 变量指定构建时拉取的分支 / 标签：

```bash
# 首次使用：复制模板（.env 已被 .gitignore 忽略，不入库）
cp .env.example .env

# 编辑 ruoyi-original/java8/.env
RUOYI_BRANCH=v3.9.0        # 示例：修改此处即可切换目标版本
```

常用版本示例：

| 目标版本 | RUOYI_BRANCH | 使用环境 |
|----------|--------------|----------|
| v3.7.0（Shiro） | `v3.7.0` | java8 |
| v3.9.0（SpringSecurity-JWT） | `v3.9.0` | java8 |
| springboot3（JDK17） | `springboot3` | java17 |

修改后重新构建即可生效：

```bash
docker compose up -d --build
```

---

## 四、快速启动

进入目标子环境目录（`java8` 或 `java17`），执行：

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

---

## 五、访问与默认账号

- 前端访问：`http://<宿主机IP>:<对应端口>`（`java8` 默认 8080，`java17` 默认 8081，以 `docker-compose.yml` 为准）
- 默认账号：`admin / admin123`

> 详细构建与配置说明见各子目录下的 `README.md`。
