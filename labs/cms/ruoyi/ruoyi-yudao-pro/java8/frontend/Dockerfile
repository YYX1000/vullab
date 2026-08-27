# ============================================================
# ruoyi-vue-pro（java8）前端构建
#   阶段1: git clone yudao-ui-admin-vue3 + pnpm build 编译前端
#   阶段2: nginx 托管编译产物（反代配置由 compose 挂载 nginx.conf）
# ============================================================

# ---------- 阶段1: 拉取源码并编译 ----------
# 使用 Debian 版 node（glibc），避免 dart-sass 在 alpine(musl) 上无法运行
# yudao-ui-admin-vue3 基于 Vite 8，要求 Node >= 20.19
FROM node:22-bullseye AS build

# 安装 git / CA 证书
RUN apt-get update \
    && apt-get install -y --no-install-recommends git ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# 前端仓库 / 分支：由 docker-compose build.args 从 .env 注入
ARG FRONTEND_REPO
ARG FRONTEND_BRANCH

WORKDIR /src
RUN git clone --depth 1 --branch "${FRONTEND_BRANCH}" "${FRONTEND_REPO}" .

# 覆盖生产构建：默认 .env.prod 中 VITE_BASE_URL=http://localhost:48080，
# 会让浏览器直连后端端口，绕开 nginx 反代。此处置空使 API 走同源 /admin-api。
RUN printf 'VITE_BASE_URL=\n' > .env.prod.local

# 使用国内 npm 镜像加速依赖下载，并安装 pnpm 9（yudao 前端使用 pnpm 管理依赖；
# 锁定 9 以避免 pnpm10 默认拦截依赖 postinstall 脚本导致 esbuild 等无法安装）
RUN npm config set registry https://registry.npmmirror.com \
    && npm install -g pnpm@9

RUN pnpm install
RUN pnpm run build:prod

# ---------- 阶段2: nginx 托管 ----------
FROM nginx:alpine

LABEL maintainer="vullab"

# 前端编译产物（yudao 生产输出目录为 dist-prod）
COPY --from=build /src/dist-prod /usr/share/nginx/html

EXPOSE 80
