# ============================================================
# RuoYi-Vue 官方原版（java8）前端构建
#   阶段1: git clone 源码 + npm build 编译前端
#   阶段2: nginx 托管编译产物（反代配置由 compose 挂载 nginx.conf）
# ============================================================

# ---------- 阶段1: 拉取源码并编译 ----------
# 使用 Debian 版 node（glibc），避免 dart-sass 在 alpine(musl) 上无法运行
FROM node:16-bullseye AS build

# 安装 git / CA 证书
RUN apt-get update \
    && apt-get install -y --no-install-recommends git ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# 仓库地址 / 分支(或标签)：由 docker-compose build.args 从 .env 注入
ARG RUOYI_REPO
ARG RUOYI_BRANCH

WORKDIR /src
RUN git clone --depth 1 --branch "${RUOYI_BRANCH}" "${RUOYI_REPO}" .

WORKDIR /src/ruoyi-ui

# 使用国内 npm 镜像加速依赖下载
RUN npm config set registry https://registry.npmmirror.com

RUN npm install --no-audit --no-fund
RUN npm run build:prod

# ---------- 阶段2: nginx 托管 ----------
FROM nginx:alpine

LABEL maintainer="vullab"

# 前端编译产物（nginx 默认静态目录）
COPY --from=build /src/ruoyi-ui/dist /usr/share/nginx/html

EXPOSE 80
