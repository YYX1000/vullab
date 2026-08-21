# wp2shell (CVE-2026-63030 & CVE-2026-60137) - full RCE chain

## 漏洞环境启动
```bash
docker compose build
docker compose build
```

## 安装

访问漏洞地址 http://127.0.0.1:8080 进行安装:
数据库ip: db
数据库: vuln_wp
数据库账密: **wpuser/wppass123**
## 漏洞验证
```bash
cd wp2shell-poc
python3 check http://127.0.0.1:8080
```