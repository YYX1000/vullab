# vullab 新增靶场 / CMS 环境规范

> 本文档定义在 `labs/` 下新增靶场或 CMS 多版本环境的统一标准。
> 所有新增 / 调整均须遵循本文档与根目录 `AGENT.md`（AI 协作红线以 `AGENT.md` 为准）。

---

## 一、定位与目录

### 1.1 两类目录的定位

| 目录                        | 定位                                                          | 示例                                                      |
|-----------------------------|---------------------------------------------------------------|-----------------------------------------------------------|
| `labs/<漏洞类型>/<靶场名>/` | 围绕**具体 CVE / 漏洞链**的靶场，带复现步骤与 PoC             | `labs/sqli/wp2shell`、`labs/xxe/CVE-2025-66516`           |
| `labs/cms/<cms名>/`         | 常见 CMS 的**完整多版本运行环境**（不含 PoC），供漏洞研究复用 | `labs/cms/wordpress`、`labs/cms/seacms`、`labs/cms/ruoyi` |

### 1.2 命名规范

- 靶场名：小写，单词间用 `-`；CVE 靶场直接用 CVE 编号（`CVE-2025-68493`）
- CMS 变体目录：按**时代 / 运行时**命名，如 `wp4x`、`latest`、`v6x`、`java8`、`java17`
- 全路径禁止空格与中文

### 1.3 文件构成

**单环境漏洞靶场（必须）：**

```
labs/<类型>/<靶场名>/
├── docker-compose.yml      # 文件名固定（CI 仅匹配该名称，.yaml 不校验）
└── README.md               # 启动方式 + 复现步骤 / PoC 用法
```

**CMS 多版本环境（必须）：**

```
labs/cms/<cms名>/
├── README.md               # 总文档：发展历程、版本对照表、切换说明、法律免责
└── <变体>/                 # 每个时代 / 运行时一个子目录，独立部署互不干扰
    ├── .env.example        # ⭐ 版本变量模板（含可切换值与漏洞对应关系注释）
    ├── docker-compose.yml
    ├── php/Dockerfile      # 按需；纯官方镜像拉取时可省略
    └── README.md           # 变体独立使用文档
```

---

## 二、版本切换机制（.env 规范）

> 多版本环境的核心：**修改 `.env` 中一个语义化变量即可切换版本**，参考 `RUOYI_BRANCH`、`WORDPRESS_IMAGE`、`SEACMS_ZIP_URL`。

1. `.env.example` 提交入库，`.env` 由使用者复制生成、**不入库**（根 `.gitignore` 已覆盖）
2. 版本变量命名语义化，按来源选择形式：
   - git 仓库 / 分支：`<项目>_BRANCH`、`<项目>_REPO`
   - 镜像 tag（官方镜像复用）：`<项目>_IMAGE`
   - 安装包直链：`<项目>_ZIP_URL`
3. **所有可切换值必须实测可用**（Docker Hub tag 经 API 验证、源码 URL 经下载验证），并在 `.env.example` 注释中列出：可用值 + 版本性质（漏洞版本 / 修复对照版）+ 对应 CVE
4. compose 中的默认值必须与 `.env.example` 完全一致（CI 无 `.env` 运行）
5. README 须注明：跨版本切换建议先 `docker compose down -v` 清空数据卷

---

## 三、网络与端口

1. **网络暴露基线**：仅暴露必要的前端 / Web 端口；数据库、缓存、后端 API 一律仅内部组网，不映射宿主机
2. `depends_on` 必须配合 `healthcheck` 使用条件启动（`condition: service_healthy`），保证 Web 启动时依赖已就绪
3. **端口分配**：靶场间端口独占，新增时先 `docker ps` 检查占用，按现有分配顺延

   | 区间 | 归属 |
   |------|------|
   | 8080–8085 | ruoyi 系（含共用默认 8080 的单环境靶场） |
   | 8086–8088 | cms/wordpress（wp4x / wp5x / latest） |
   | 8089–8092 | cms/seacms（v6x / v10x / v12x / v13x） |
   | 8093+ | 后续新增顺延 |

4. 容器名全局唯一，统一 `<项目>-<变体>-<服务>` 格式（如 `seacms-v6x-mysql`、`wp2shell-web`）
5. 数据卷使用 compose 默认项目前缀即可（目录名隔离），无需全局唯一命名

---

## 四、存储与挂载（靶场无状态原则）

> 靶场是**一次性环境**，没有持久化需求。持久化只会让清理变复杂：bind mount 使数据散落在工作区，named volume 依赖使用者记得 `down -v`，漏掉即残留。

1. **禁止 bind mount 仓库 / 宿主机目录做数据持久化**（如 `./www:/var/www/html`、`./data:/var/lib/mysql`）
2. **默认不使用 named volume**：数据库等易失数据直接写容器可写层，`docker compose down` 即随容器彻底清除，无需 `-v`
3. 配置与初始化脚本的注入方式优先级：环境变量 → 构建时注入（ARG / RUN 拷贝）→ 只读挂载**单个文件**（`:ro`，如初始化 SQL、独立配置文件，须在 README 说明用途）
4. 靶场内需要"预置数据"时（如初始化数据库），放入镜像层或只读单文件挂载，不落运行时可写挂载

---

## 五、构建规范

**方案优先级**（从上到下取首个满足需求的）：

1. **官方镜像直接复用**（如 `wordpress` 官方镜像 + `.env` 切 tag）——零构建，首选
2. **构建时拉取源码**（`ARG` 注入仓库 / 安装包地址，容器内 clone / 下载解压）——适合无官方多版本镜像的项目
3. **本地源码构建**——仅当源码无法远程获取时使用

硬性要求：

- 采用**多阶段构建**，极致压缩镜像体积；构建依赖不带入最终层
- 密钥、敏感配置全部经环境变量注入，禁止硬编码进代码、配置文件或镜像；**PoC / EXP 不得打包进镜像**
- 禁止挂载宿主机根目录或系统敏感目录
- 使用已 EOL 的基础镜像（如 `php:5.6-apache` → Debian stretch 归档）时，必须在 Dockerfile 内处理归档源（覆盖 `archive.debian.org` + `[trusted=yes]` + `Check-Valid-Until=false`），并注明原因
- 处理第三方安装包时注意内部目录结构差异，用通配符（如 `*/Upload/*`）或显式路径统一解压逻辑

---

## 六、文档规范

**变体 README 必含**：简介与时代 / 版本说明 → 架构图（内部 / 对外暴露关系）→ 启动命令 → 访问与初始化（含默认数据库账密）→ 切换版本方法 → 常用命令

**CMS 总 README 必含**：目录结构 → **发展历程与版本对照表**（版本 / 时代 / PHP / 数据库 / 端口 / 漏洞对应）→ 整体使用说明（切换方法、初始化）→ **法律免责声明**

**漏洞靶场 README 必含**：漏洞编号与影响范围 → 启动 → 初始化 → 复现步骤 / PoC 用法 → 修复建议

- 全部使用中文；引用 CVE 时附修复版本，便于对照复现
- 新增 / 调整靶场后，**必须同步更新根 `README.md`** 的目录树与靶场清单

---

## 七、验收标准（硬性）

新增 / 修改靶场后，提交前须完成：

1. `docker compose config` 通过（无 `.env` 情况下，即 compose 默认值自洽）
2. `docker compose up -d --build` 成功，页面可访问（安装向导 / 服务端口返回 200）
3. **版本切换机制实测**：至少完成一次跨版本切换，确认目标版本真实生效（如镜像内 `wp_version`、`ver.txt` 等标识文件）
4. 验证后 `docker compose down` 即彻底清理（数据存于容器层，随容器移除），删除本地 `.env` 及临时文件
5. Dockerfile 符合多阶段构建规范；`git status` 复核无构建产物（`__pycache__`、`node_modules`、`.env` 等）混入
6. Push 后 CI（遍历全部 `docker-compose.yml` 执行 `config / build / up / ps / logs / down`）必须通过；**失败时修复问题重推，禁止 `--no-verify` 绕过**

---

## 八、既有模式速查

| 项目 | 模式 | 版本切换变量 | 目录 |
|------|------|--------------|------|
| RuoYi | 3 体系 × java8/java17，构建时 git clone | `RUOYI_BRANCH` / `YUDAO_BRANCH` | `labs/cms/ruoyi` |
| WordPress | 3 时代，官方镜像 + tag 切换 | `WORDPRESS_IMAGE` | `labs/cms/wordpress` |
| SeaCMS | 4 时代，构建时拉安装包 / 源码树 | `SEACMS_ZIP_URL` / `SEACMS_REPO` | `labs/cms/seacms` |
| wp2shell | 漏洞靶场复用 cms/wordpress 镜像模式 | `WORDPRESS_IMAGE`（受影响版本） | `labs/sqli/wp2shell` |

> 新增 CMS 多版本环境前，先调研：官方是否保留历史版本下载、GitHub 归档仓库（tags / 完整安装包）、各时代对应的 PHP / 数据库运行时，再按本文档拆分变体。
