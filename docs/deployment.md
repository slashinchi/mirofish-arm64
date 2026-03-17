# MiroFish ARM64 部署指南

本文档详细说明如何在 Mac mini M4 (ARM64) 上部署 MiroFish + OpenZep + Neo4j 系统。

## 目录

1. [系统要求](#系统要求)
2. [前置准备](#前置准备)
3. [镜像构建](#镜像构建)
4. [本地部署](#本地部署)
5. [服务验证](#服务验证)
6. [常见问题](#常见问题)

---

## 系统要求

| 项目 | 最低要求 | 推荐配置 |
|------|----------|----------|
| **CPU** | Apple Silicon (M1/M2/M3/M4) | Mac mini M4 |
| **内存** | 8GB | 16GB+ |
| **存储** | 20GB 可用空间 | 50GB+ |
| **Docker** | OrbStack 或 Docker Desktop | OrbStack (性能更好) |

---

## 前置准备

### 1. 获取阿里百炼 API Key

1. 访问 [阿里云百炼控制台](https://bailian.console.aliyun.com/)
2. 登录阿里云账号
3. 在「API-KEY 管理」中创建新的 API Key
4. 记录 API Key，后续配置需要使用

**费用说明**：
- qwen-plus 模型：约 0.004 元/千 tokens
- 每次模拟（40 轮）：约 20-40 元
- 建议先充值 100 元测试

### 2. 安装 OrbStack（如未安装）

```bash
# 使用 Homebrew 安装
brew install orbstack

# 或从官网下载
# https://orbstack.dev/
```

### 3. 验证 Docker 环境

```bash
docker --version
docker info | grep Architecture
# 应显示: Architecture: aarch64
```

---

## 镜像构建

### 方式一：使用 GitHub Actions 自动构建（推荐）

镜像会自动构建并推送到 ghcr.io：

```bash
# 触发构建：推送 tag
git clone https://github.com/slashinchi/mirofish-arm64.git
cd mirofish-arm64
git tag v1.0.0
git push origin v1.0.0
```

或手动触发：
1. 访问 https://github.com/slashinchi/mirofish-arm64/actions
2. 选择 "Build ARM64 Images" workflow
3. 点击 "Run workflow"

### 方式二：本地构建镜像

如果 GitHub Actions 不可用，可以本地构建：

```bash
# 构建 MiroFish
git clone https://github.com/666ghj/MiroFish.git
cd MiroFish
docker buildx build --platform linux/arm64 -t mirofish:local .

# 构建 OpenZep
git clone https://github.com/N1nEmAn/openzep.git
cd openzep
docker buildx build --platform linux/arm64 -t openzep:local .
```

构建完成后修改 `docker-compose.yml` 使用本地镜像：

```yaml
services:
  mirofish:
    image: mirofish:local
  openzep:
    image: openzep:local
```

---

## 本地部署

### 1. 创建部署目录

```bash
# 创建项目目录
mkdir -p /Volumes/ITGZ/orbstack/mirofish
cd /Volumes/ITGZ/orbstack/mirofish

# 创建数据目录
mkdir -p data/neo4j data/openzep uploads
```

### 2. 下载配置文件

```bash
# 下载 docker-compose.yml
curl -O https://raw.githubusercontent.com/slashinchi/mirofish-arm64/main/docker-compose.yml

# 下载 .env.example
curl -O https://raw.githubusercontent.com/slashinchi/mirofish-arm64/main/.env.example
```

### 3. 配置环境变量

```bash
# 复制模板
cp .env.example .env

# 编辑配置
vim .env
```

**必须修改的配置项**：

```env
# 阿里百炼 API Key
LLM_API_KEY=sk-xxxxxxxxxxxx

# OpenZep API Key（自定义一个，用于内部服务认证）
OPENZEP_API_KEY=your-secret-key-here

# Neo4j 密码（建议修改为强密码）
NEO4J_PASSWORD=your-strong-password
```

### 4. 启动服务

```bash
# 拉取镜像
docker compose pull

# 启动服务
docker compose up -d

# 查看日志
docker compose logs -f
```

---

## 服务验证

### 1. 检查服务状态

```bash
# 查看容器状态
docker compose ps

# 应显示 3 个容器均为 "running" 状态
```

### 2. 验证各服务

```bash
# Neo4j Web Console
open http://localhost:7474
# 用户名: neo4j, 密码: .env 中设置的 NEO4J_PASSWORD

# OpenZep 健康检查
curl http://localhost:8000/healthz
# 应返回: {"status":"ok"}

# MiroFish Backend
curl http://localhost:5001
# 应返回: MiroFish API response

# MiroFish Frontend
open http://localhost:3000
```

### 3. 端口说明

| 端口 | 服务 | 说明 |
|------|------|------|
| 3000 | MiroFish Frontend | Web 界面 |
| 5001 | MiroFish Backend | API 服务 |
| 8000 | OpenZep | 知识图谱 API |
| 7474 | Neo4j HTTP | Web Console |
| 7687 | Neo4j Bolt | 数据库连接 |

---

## 常见问题

### Q1: 镜像拉取失败

```bash
# 检查是否登录 ghcr.io
echo $GITHUB_PAT_TOKEN | docker login ghcr.io -u slashinchi --password-stdin

# 或使用本地构建的镜像
```

### Q2: Neo4j 启动失败

```bash
# 检查数据目录权限
chmod -R 755 data/neo4j

# 查看详细日志
docker compose logs neo4j
```

### Q3: OpenZep 连接 Neo4j 失败

```bash
# 等待 Neo4j 完全启动（约 30-60 秒）
docker compose logs neo4j | grep "Started"

# 重启 OpenZep
docker compose restart openzep
```

### Q4: LLM API 调用失败

```bash
# 检查 API Key 是否正确
echo $LLM_API_KEY

# 测试 API 连通性
curl -X POST "https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions" \
  -H "Authorization: Bearer $LLM_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"qwen-plus","messages":[{"role":"user","content":"hello"}]}'
```

### Q5: 内存不足

```bash
# 增加 Docker 内存限制
# OrbStack: Settings > Resources > Memory

# 或在 docker-compose.yml 中添加资源限制
services:
  neo4j:
    deploy:
      resources:
        limits:
          memory: 4G
```

---

## 备份与恢复

### 备份数据

```bash
# 备份 Neo4j 数据
docker compose exec neo4j neo4j-admin database dump neo4j --to-path=/backup

# 备份上传文件
tar -czvf uploads-backup.tar.gz uploads/
```

### 恢复数据

```bash
# 恢复 Neo4j 数据
docker compose exec neo4j neo4j-admin database load neo4j --from-path=/backup

# 恢复上传文件
tar -xzvf uploads-backup.tar.gz
```

---

## 更新镜像

```bash
# 拉取最新镜像
docker compose pull

# 重启服务
docker compose up -d
```

---

## 停止服务

```bash
# 停止但保留数据
docker compose down

# 停止并删除数据（谨慎操作）
docker compose down -v
rm -rf data/ uploads/
```