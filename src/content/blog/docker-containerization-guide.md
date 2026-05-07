---
title: "Docker 容器化部署完全指南"
description: "学习 Docker 的核心概念和实践，掌握容器化部署技术。"
date: 2019-04-25
tags: ["Docker", "容器化", "DevOps"]
author: "zeng"
---

2019年，容器化技术已经成为现代软件开发的标准实践。Docker 作为最流行的容器化平台，彻底改变了我们构建、分发和运行应用程序的方式。

## Docker 基础概念

### 什么是容器？

容器是一种轻量级的虚拟化技术，它将应用程序及其依赖项打包在一起，确保应用在任何环境中都能一致地运行。

### Docker 核心组件

- **镜像（Image）**：只读模板，包含创建容器所需的所有内容
- **容器（Container）**：镜像的运行实例
- **仓库（Registry）**：存储和分发镜像的地方
- **Dockerfile**：定义如何构建镜像的文本文件

## 快速开始

### 安装 Docker

`ash
# Ubuntu
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# macOS
brew install docker
`

### 第一个容器

`ash
# 拉取镜像
docker pull nginx

# 运行容器
docker run -d -p 80:80 nginx

# 查看运行中的容器
docker ps
`

## 编写 Dockerfile

### 基础示例

`dockerfile
# 使用官方 Node.js 镜像
FROM node:14-alpine

# 设置工作目录
WORKDIR /app

# 复制 package.json
COPY package*.json ./

# 安装依赖
RUN npm install

# 复制源代码
COPY . .

# 暴露端口
EXPOSE 3000

# 启动应用
CMD ["npm", "start"]
`

### 多阶段构建

`dockerfile
# 构建阶段
FROM node:14-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# 运行阶段
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
`

## Docker Compose

使用 Docker Compose 管理多容器应用：

`yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    depends_on:
      - db
  
  db:
    image: postgres:13
    environment:
      POSTGRES_PASSWORD: example
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
`

## 最佳实践

### 镜像优化

1. **使用精简基础镜像**：如 lpine 版本
2. **减少层数**：合并多个 RUN 指令
3. **利用缓存**：合理安排指令顺序
4. **多阶段构建**：减小最终镜像大小

### 安全实践

`dockerfile
# 使用非 root 用户
RUN addgroup -g 1000 appuser && \
    adduser -D -u 1000 -G appuser appuser
USER appuser
`

## 实战案例

### 部署 Node.js 应用

`ash
# 构建镜像
docker build -t myapp:1.0 .

# 运行容器
docker run -d -p 3000:3000 --name myapp myapp:1.0

# 查看日志
docker logs myapp

# 进入容器
docker exec -it myapp sh
`

## 总结

Docker 的容器化技术为现代软件开发带来了革命性的变化。通过本文的学习，你已经掌握了 Docker 的核心概念和基本使用方法。容器化不仅能提高开发效率，还能确保应用在不同环境中的一致性。继续深入学习和实践，你会发现 Docker 的更多强大功能！
