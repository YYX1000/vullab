# ============================================================
# RuoYi-Vue3 前端构建（官方若依 Vue3 前端）
#   阶段1: git clone 源码
#   阶段2: npm 打包 (生产环境)
#   阶段3: nginx 托管 dist (编译产物交给 nginx)
# ============================================================

# ---------- 阶段1: 拉取源码 ----------
FROM alpine/git AS src
# 安装 CA 证书，保证 https 克隆成功
RUN apk add --no-cache ca-certificates

# 仓库地址 / 分支(或标签)：由 docker-compose build.args 从 .env 注入
# （此处不设默认值，唯一配置源是 .env）
ARG RUOYI_UI_REPO
ARG RUOYI_UI_BRANCH

WORKDIR /src
RUN git clone --depth 1 --branch "${RUOYI_UI_BRANCH}" "${RUOYI_UI_REPO}" .

# ---------- 阶段2: npm 打包 ----------
FROM node:22-alpine AS builder

WORKDIR /app

# 使用国内镜像加速依赖下载
RUN npm config set registry https://registry.npmmirror.com

COPY --from=src /src/ .

# 安装依赖并打包（.env.production -> VITE_APP_BASE_API=/prod-api）
RUN npm install && npm run build:prod

# ---------- 阶段3: nginx 托管 ----------
FROM nginx:1.25-alpine

LABEL maintainer="vullab"

# nginx 配置（根目录 nginx/nginx.conf，完整主配置）
COPY nginx/nginx.conf /etc/nginx/nginx.conf

# 编译产物交给 nginx 托管
COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80
