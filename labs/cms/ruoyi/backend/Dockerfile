# ============================================================
# RuoYi-Vue 后端构建（官方若依）
#   阶段1: git clone 源码
#   阶段2: Maven 打包
#   阶段3: JRE 运行
# ============================================================

# ---------- 阶段1: 拉取源码 ----------
FROM alpine/git AS src
# 安装 CA 证书，保证 https 克隆成功
RUN apk add --no-cache ca-certificates

# 仓库地址 / 分支(或标签)：由 docker-compose build.args 从 .env 注入
# （此处不设默认值，唯一配置源是 .env）
ARG RUOYI_REPO
ARG RUOYI_BRANCH

WORKDIR /src
RUN git clone --depth 1 --branch "${RUOYI_BRANCH}" "${RUOYI_REPO}" .

# ---------- 阶段2: Maven 打包 ----------
FROM maven:3.9-eclipse-temurin-21 AS builder

WORKDIR /build
COPY --from=src /src/ .

# 跳过测试打包（application.yml 默认激活 druid 数据源配置）
RUN mvn -B -DskipTests clean package

# ---------- 阶段3: 运行 ----------
FROM eclipse-temurin:21-jre

LABEL maintainer="vullab"

ENV TZ=Asia/Shanghai \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    SERVER_PORT=8080 \
    JAVA_OPTS="-Xms256m -Xmx1024m"

# 运行目录 / 日志目录 / 临时目录
RUN mkdir -p /ruoyi/server/logs /ruoyi/server/temp
WORKDIR /ruoyi/server

COPY --from=builder /build/ruoyi-admin/target/ruoyi-admin.jar ./app.jar

EXPOSE 8080

# exec 使 java 成为 1 号进程，便于接收 SIGTERM 优雅退出
ENTRYPOINT ["sh", "-c", "exec java -Djava.security.egd=file:/dev/./urandom -Dserver.port=${SERVER_PORT} ${JAVA_OPTS} -jar app.jar"]
