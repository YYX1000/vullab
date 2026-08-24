# RuoYi-Vue 环境（Docker 搭建）

基于官方若依 [RuoYi-Vue](https://gitee.com/y_project/RuoYi-Vue)（Java 后端）
+ [RuoYi-Vue3](https://gitcode.com/yangzongzhuan/RuoYi-Vue3)（Vue3 前端）的一键 Docker 环境。

> 若依各版本、前后端技术栈与部署详情见 [docs/versions.md](docs/versions.md)。

## 启动

```bash
docker compose up -d --build
```

首次构建会拉取源码并下载依赖，耗时较长。

## 访问

- 前端入口：<http://localhost:8080>
- 默认账号：`admin / admin123`

## 配置

端口、版本、仓库地址等均可在 `.env` 中修改。
