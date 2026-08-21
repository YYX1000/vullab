## PR变更类型
- [ ] 🆕 新增靶场
- [ ] 🐛 修复已有靶场Bug
- [ ] 📝 更新文档/说明
- [ ] ♻️ 重构、优化脚本或CI配置

### 漏洞类型
cms / xxe / rce / sqli / fileupload / others

### 靶场完整路径
>示例：`labs/xxe/CVE‑2025‑66516`

### CVE编号 & 漏洞简述
> 如果没有CVE，写漏洞名称

### 自测清单（PR提交前全部确认完成）
- [ ] 靶场已经放到对应漏洞类型分类目录
- [ ] `docker compose config` 语法校验通过
- [ ] `docker compose build` 镜像构建正常
- [ ] `docker compose up` 容器可正常启动
- [ ] 靶场目录内已编写README.md，写明访问地址、账号密码、复现要点
- [ ] 无端口冲突，尽量避免硬编码占用常用端口冲突
- [ ] CI会自动校验全部靶场，请等待CI执行全部通过

### 补充说明&注意事项
<!--写特殊依赖、环境限制等信息，没有可以填无-->

### 关联Issue
<!-- 如果这个PR解决某个issue，填写 close #xxx -->