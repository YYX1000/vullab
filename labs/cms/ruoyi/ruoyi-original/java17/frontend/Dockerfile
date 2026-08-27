# ============================================================
# RuoYi-Vue 官方原版（java17）前端构建
#   阶段1: git clone 独立前端仓库(RuoYi-Vue2) + npm build 编译前端
#   阶段2: nginx 托管编译产物（反代配置由 compose 挂载 nginx.conf）
# ============================================================

# ---------- 阶段1: 拉取源码并编译 ----------
# 使用 Debian 版 node（glibc），避免 dart-sass 在 alpine(musl) 上无法运行
FROM node:16-bullseye AS build

# 安装 git / CA 证书
RUN apt-get update \
    && apt-get install -y --no-install-recommends git ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# 前端仓库 / 分支：由 docker-compose build.args 从 .env 注入
# （springboot3 分支已拆分前端为独立仓库 RuoYi-Vue2）
ARG FRONTEND_REPO
ARG FRONTEND_BRANCH

WORKDIR /src
RUN git clone --depth 1 --branch "${FRONTEND_BRANCH}" "${FRONTEND_REPO}" .

# 使用国内 npm 镜像加速依赖下载
RUN npm config set registry https://registry.npmmirror.com

RUN npm install --no-audit --no-fund
RUN npm run build:prod

# ---------- 阶段2: nginx 托管 ----------
FROM nginx:alpine

LABEL maintainer="vullab"

# 前端编译产物（RuoYi-Vue2 默认输出目录 dist，nginx 默认静态目录）
COPY --from=build /src/dist /usr/share/nginx/html

EXPOSE 80
