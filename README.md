# vullab

> 漏洞复现实验靶场合集（Vulnerability Lab）

一套基于 Docker 的**漏洞靶场与复现实验环境**，按漏洞类型分类组织，每个靶场独立部署、开箱即用，用于**授权安全测试、漏洞研究、红蓝对抗训练与安全教学**。

---

## 一、项目简介

`vullab` 汇集了常见 Web 漏洞的**可复现靶场环境**，覆盖 CMS、SQL 注入、XXE、文件上传、RCE 等多个方向。每个靶场均提供：

- 独立的 `docker-compose.yml`，一键启动、互不干扰
- 漏洞环境源码 / 镜像构建文件
- 复现步骤说明与 PoC（部分靶场附带）
- 明确的启动命令与访问方式

所有靶场统一存放于 [`labs/`](labs/) 目录，按漏洞类型分门别类。

---

## 二、目录结构

```
vullab
├── README.md            # 项目总文档、靶场清单、使用说明、法律声明
├── .github/             # CI 工作流（自动校验并构建全部靶场）
└── labs/                # 靶场合集（按漏洞类型分类）
    ├── cms/             # CMS 漏洞环境
    │   ├── ruoyi/       #   若依 RuoYi 多版本环境（官方 / Plus / 芋道 Pro）
    │   └── seacms/      #   SeaCMS 海洋 CMS 环境
    ├── sqli/            # SQL 注入
    │   └── wordpress/   #   wp2shell（SQLi → RCE 全链路）
    ├── xxe/             # XXE 外部实体注入
    │   ├── CVE-2025-66516/   # Apache Tika XXE
    │   └── CVE-2025-68493/   # Apache Struts S2-069 XXE
    ├── fileupload/      # 文件上传（规划中）
    ├── rce/             # 远程命令执行（规划中）
    └── others/          # 其他类型（规划中）
```

---

## 三、靶场清单

| 类型 | 目录 | 目标 / 漏洞 | 说明 |
|------|------|-------------|------|
| CMS | [`labs/cms/ruoyi`](labs/cms/ruoyi) | RuoYi 多版本环境 | 官方 RuoYi-Vue / RuoYi-Vue-Plus / 芋道 Pro，多 JDK 版本 |
| CMS | [`labs/cms/seacms`](labs/cms/seacms) | SeaCMS | 海洋 CMS 安装与漏洞环境 |
| SQLi | [`labs/sqli/wordpress`](labs/sqli/wordpress) | CVE-2026-63030 & CVE-2026-60137 | WordPress SQLi → RCE 全链路（wp2shell） |
| XXE | [`labs/xxe/CVE-2025-66516`](labs/xxe/CVE-2025-66516) | XXE | Apache Tika 外部实体注入 |
| XXE | [`labs/xxe/CVE-2025-68493`](labs/xxe/CVE-2025-68493) | XXE（S2-069） | Apache Struts 外部实体注入 |
| 文件上传 | `labs/fileupload` | 规划中 | — |
| RCE | `labs/rce` | 规划中 | — |
| 其他 | `labs/others` | 规划中 | — |

> 更多细节请查看各靶场目录内的 `README.md`。

---

## 四、使用说明

### 1. 环境要求

- Docker Engine 20.10+
- Docker Compose v2（`docker compose` 命令）
- 可访问外网的构建网络（部分靶场需拉取源码与依赖）

### 2. 启动靶场

进入目标靶场目录，执行：

```bash
docker compose up -d --build
```

查看日志 / 停止：

```bash
docker compose logs -f     # 查看日志
docker compose down        # 停止并移除容器
docker compose down -v     # 停止并移除容器与数据卷（会清空数据）
```

### 3. 批量校验

根目录已配置 GitHub Actions（`.github/workflows/ci.yml`），会自动遍历 `labs/` 下所有 `docker-compose.yml`，执行 `config` 校验、构建、启动与清理，保证各靶场可正常部署。

---

## 五、法律说明（免责声明）

> ⚠️ **本仓库所有内容仅限合法授权的安全测试、教学研究与个人学习使用。**

1. 本项目提供的靶场环境、源码与 PoC **仅用于授权范围内的渗透测试、漏洞复现、安全研究与教学场景**。
2. 使用者必须确保在**拥有明确授权的目标环境**中开展测试，严格遵守《中华人民共和国网络安全法》《中华人民共和国数据安全法》《中华人民共和国个人信息保护法》及其他适用的法律法规。
3. **严禁**利用本项目及相关技术对**未经授权的系统、网站、服务或他人设备**进行任何形式的攻击、入侵、破坏、数据窃取或干扰。
4. 因使用者违反法律法规、超出授权范围或滥用本项目所产生的一切后果与法律责任，**均由使用者自行承担**，与本项目及贡献者无关。
5. 本项目不提供任何面向真实目标的攻击工具或武器化脚本，仅提供**本地隔离的实验环境**，用于学习与验证已知漏洞的成因、危害及修复方法。
6. 若发现本仓库内容涉及侵权或不当之处，请联系维护者处理。

---

**请遵守法律，安全研究，合法合规使用。**
