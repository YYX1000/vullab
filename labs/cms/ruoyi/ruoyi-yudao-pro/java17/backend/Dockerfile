# ============================================================
# ruoyi-vue-pro（java17）后端构建
#   阶段1: git clone 源码（master-jdk17）
#   阶段2: Maven 全量 reactor 打包 yudao-server（JDK17，root modules 已裁剪为 server+system+infra+framework）
#   阶段3: JRE 运行（JDK17，内部端口 48080）
# ============================================================

# ---------- 阶段1: 拉取源码 ----------
FROM alpine/git AS src
# 安装 CA 证书，保证 https 克隆成功
RUN apk add --no-cache ca-certificates

# 仓库地址 / 分支(或标签)：由 docker-compose build.args 从 .env 注入
ARG YUDAO_REPO
ARG YUDAO_BRANCH

WORKDIR /src
RUN git clone --depth 1 --branch "${YUDAO_BRANCH}" "${YUDAO_REPO}" .

# ---------- 阶段2: Maven 打包 ----------
FROM maven:3.9-eclipse-temurin-17 AS builder

WORKDIR /build

# 复用全局国内镜像配置（build context 为 ruoyi 根目录）
COPY common/maven-settings.xml /usr/share/maven/conf/settings.xml

COPY --from=src /src/ .

# 全量 reactor 构建（root modules 已裁剪为 server + system + infra + framework，与上游 CI 一致）
RUN mvn -B -Dmaven.test.skip=true clean package

# ---------- 阶段3: 运行 ----------
FROM eclipse-temurin:17-jre

LABEL maintainer="vullab"

ENV TZ=Asia/Shanghai \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    SERVER_PORT=48080 \
    JAVA_OPTS="-Xms256m -Xmx1024m"

# 运行目录 / 日志目录 / 临时目录
RUN mkdir -p /yudao/server/logs /yudao/server/temp
WORKDIR /yudao/server

COPY --from=builder /build/yudao-server/target/yudao-server.jar ./app.jar

EXPOSE 48080

# exec 使 java 成为 1 号进程，便于接收 SIGTERM 优雅退出
ENTRYPOINT ["sh", "-c", "exec java -Djava.security.egd=file:/dev/./urandom -Dserver.port=${SERVER_PORT} ${JAVA_OPTS} -jar app.jar"]
