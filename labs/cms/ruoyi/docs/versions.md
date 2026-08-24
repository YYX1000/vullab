# 若依（RuoYi）版本 & 部署速查

> 数据来源：若依官网 <http://ruoyi.vip>、Gitee <https://gitee.com/y_project>、GitCode 官方仓库（整理时间 2026-08）。


## 一、版本全景

| 版本          | 定位         | 最新版本   | 前端                                | 后端                                                              | 说明             |
|-------------|------------|--------|-----------------------------------|-----------------------------------------------------------------|----------------|
| RuoYi       | 单体（前后端不分离） | v4.8.3 | Thymeleaf + Bootstrap + Hplus(H+) | Spring Boot + MyBatis + Shiro                                   | 轻量级，经典单应用      |
| RuoYi-Vue   | 前后端分离（后端）  | v3.9.2 | 见「前端版本」                           | Spring Boot + Spring Security + Redis & JWT                     | 官方主推           |
| RuoYi-Cloud | 微服务        | v3.6.8 | RuoYi-Vue 前端                      | Spring Boot + Spring Cloud & Alibaba + Nacos + Sentinel + Seata | 分布式微服务         |
| RuoYi-App   | 移动端        | v1.2.0 | UniApp（Vue2 / Vue3）               | 对接 RuoYi-Vue / RuoYi-Cloud                                      | APP / 小程序 / H5 |

## 二、后端分支（Spring Boot 版本）

RuoYi-Vue / RuoYi / RuoYi-Cloud 均提供多版本分支并行维护：

| 分支          | Spring Boot   | JDK | 备注                  |
|-------------|---------------|-----|---------------------|
| master（默认）  | 4.x（当前 4.1.0） | 17+ | Cloud 版对应 Nacos 3.x |
| springboot3 | 3.x           | 17+ | Cloud 版对应 Nacos 3.x |
| springboot2 | 2.x           | 8+  | Cloud 版对应 Nacos 2.x |

## 三、前端版本（RuoYi-Vue 配套）

| 项目                    | Vue   | 语言         | 构建工具    | UI 组件库       | 状态管理  | 路由           | 说明           |
|-----------------------|-------|------------|---------|--------------|-------|--------------|--------------|
| RuoYi-Vue2            | Vue 2 | JavaScript | Vue CLI | Element UI   | Vuex  | Vue Router 3 | 经典稳定，维护重心已转移 |
| RuoYi-Vue3            | Vue 3 | JavaScript | Vite    | Element Plus | Pinia | Vue Router 4 | 官方主推活跃版本     |
| RuoYi-Vue3-TypeScript | Vue 3 | TypeScript | Vite    | Element Plus | Pinia | Vue Router 4 | 类型加持，适合企业级协作 |

## 四、本环境选型

| 组件 | 选型                                                                    |
|----|-----------------------------------------------------------------------|
| 后端 | RuoYi-Vue v3.9.2，master 分支（Spring Boot 4.1.0 / JDK 17+，镜像构建使用 JDK 21） |
| 前端 | RuoYi-Vue3（Vue 3 + Vite + Element Plus），master 分支                     |
| 部署 | Docker Compose：mysql + redis + backend + frontend(nginx)              |

## 五、相关地址

| 项目        | 地址                     |
|-----------|------------------------|
| 官网        | <http://ruoyi.vip>     |
| 文档        | <http://doc.ruoyi.vip> |
| 演示（Vue 版） | <http://vue.ruoyi.vip> |
| 移动端 H5 体验 | <http://h5.ruoyi.vip>  |
