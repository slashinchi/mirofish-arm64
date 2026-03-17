# MiroFish ARM64 部署指南

本文档详细介绍如何在 Mac mini M4 (ARM64) 上完整部署 MiroFish + OpenZep + Neo4j 系统。

## 目录

- [前置准备](#前置准备)
- [环境配置](#环境配置)
- [部署步骤](#部署步骤)
- [服务验证](#服务验证)
- [故障排查](#故障排查)
- [常见问题 FAQ](#常见问题-faq)
- [维护操作](#维护操作)

---

## 前置准备

### 1. 硬件要求

| 项目 | 最低配置 | 推荐配置 |
|------|----------|----------|
| CPU | Apple M1/M2/M3/M4 | Mac mini M4 |
| 内存 | 8GB | 16GB+ |
| 存储 | 20GB 可用空间 | 50GB+ SSD |
| 网络 | 稳定互联网连接 | 有线网络 |

### 2. 获取阿里百炼 API Key

MiroFish 使用阿里百炼的 qwen-plus 模型进行 AI 对话。

1. 访问 [阿里云百炼控制台](https://bailian.console.aliyun.com/)
2. 登录阿里云账号
3. 进入「API-KEY 管理」页面
4. 点击「创建新的 API Key」
5. 复制生成的 Key（格式：`sk-xxxxxxxxxxxx`）

**费用参考**：
- qwen-plus: 约 0.004 元/千 tokens
- 单次完整模拟（约 40 轮对话）: 20-40 元
- 建议首次充值 100 元用于测试

### 3. 安装 Docker 环境

推荐使用 OrbStack（性能更好）：

```bash
# 通过 Homebrew 安装
brew install orbstack

# 启动 OrbStack
open -a OrbStack
```

或使用 Docker Desktop：

```bash
brew install docker
```

验证安装：

```bash
docker --version
docker info | grep Architecture
# 应显示: Architecture: aarch64 或 arm64
```

---

## 环境配置

### 1. 创建项目目录

```bash
# 创建部署目录（可根据实际情况修改路径）
mkdir -p ~/mirofish
cd ~/mirofish

# 创建数据持久化目录
mkdir -p data/neo4j data/openzep uploads
```

### 2. 下载配置文件

```bash
# 下载 docker-compose.yml
curl -fsSL -o docker-compose.yml \
  https://raw.githubusercontent.com/slashinchi/mirofish-arm64/main/docker-compose.yml

# 下载环境变量模板
curl -fsSL -o .env.example \
  https://raw.githubusercontent.com/slashinchi/mirofish-arm64/main/.env.example
```

### 3. 配置 .env 文件

```bash
# 复制模板
cp .env.example .env

# 编辑配置（使用你喜欢的编辑器）
vi .env
```

**必须修改的配置项**：

```env
# 阿里百炼 API Key（从控制台获取）
LLM_API_KEY=sk-your-actual-api-key

# OpenZep 内部认证密钥（自定义，建议 32 位以上随机字符串）
OPENZEP_API_KEY=your-random-secret-key-min-32-chars

# Neo4j 数据库密码（建议强密码）
NEO4J_PASSWORD=YourStrongPassword123!
```

**可选配置项**：

```env
# LLM 模型选择（默认 qwen-plus）
LLM_MODEL=qwen-plus

# 日志级别（debug/info/warning/error）
LOG_LEVEL=info
```

---

## 部署步骤

### 1. 登录 GitHub Container Registry

由于镜像托管在 ghcr.io，需要登录才能拉取：

```bash
# 使用 GitHub Personal Access Token 登录
echo $GITHUB_TOKEN | docker login ghcr.io -u YOUR_GITHUB_USERNAME --password-stdin

# 或者交互式登录
docker login ghcr.io -u YOUR_GITHUB_USERNAME
```

> 如果没有 GitHub Token，可以在 GitHub Settings > Developer settings > Personal access tokens 中创建。

### 2. 拉取并启动服务

```bash
# 拉取最新镜像
docker compose pull

# 启动所有服务（后台运行）
docker compose up -d

# 查看启动日志
docker compose logs -f
```

首次启动需要下载镜像，可能需要 5-10 分钟，具体取决于网络速度。

### 3. 等待服务就绪

各服务启动时间：

| 服务 | 启动时间 | 检查命令 |
|------|----------|----------|
| Neo4j | 30-60 秒 | `docker compose logs neo4j` |
| OpenZep | 60-90 秒 | `curl http://localhost:8000/healthz` |
| MiroFish | 10-20 秒 | `curl http://localhost:5001` |

当 OpenZep 日志显示 `Connected to Neo4j` 时，表示系统已完全就绪。

---

## 服务验证

### 1. 检查容器状态

```bash
docker compose ps
```

预期输出（所有容器状态为 `running`）：

```
NAME                STATUS
mirofish-neo4j      running
mirofish-openzep    running
mirofish-app        running
```

### 2. 验证各服务端口

```bash
# MiroFish Web 界面
curl -s http://localhost:3000 | head -5

# MiroFish API
curl -s http://localhost:5001

# OpenZep 健康检查
curl -s http://localhost:8000/healthz

# Neo4j HTTP 接口
curl -s http://localhost:7474
```

### 3. 浏览器访问

打开浏览器访问各服务：

- **MiroFish**: http://localhost:3000
- **Neo4j Console**: http://localhost:7474
  - 用户名: `neo4j`
  - 密码: `.env` 中设置的 `NEO4J_PASSWORD`

### 4. 运行测试对话

1. 打开 http://localhost:3000
2. 创建新会话
3. 输入测试消息验证 LLM 连接正常
4. 检查对话是否能正常进行

---

## 故障排查

### 问题 1: 镜像拉取失败

**症状**：`docker compose pull` 报错 `unauthorized` 或 `not found`

**排查步骤**：

```bash
# 1. 检查是否已登录 ghcr.io
docker logout ghcr.io
docker login ghcr.io -u YOUR_USERNAME

# 2. 检查网络连接
curl -I https://ghcr.io

# 3. 手动拉取测试
docker pull ghcr.io/slashinchi/mirofish:latest
```

**解决方案**：
- 确保 GitHub Token 有 `read:packages` 权限
- 检查网络是否可以访问 ghcr.io
- 如无法访问 ghcr.io，考虑本地构建镜像（见下方）

### 问题 2: Neo4j 启动失败或反复重启

**症状**：`docker compose ps` 显示 neo4j 状态为 `Restarting`

**排查步骤**：

```bash
# 查看详细日志
docker compose logs neo4j

# 检查数据目录权限
ls -la data/neo4j

# 检查磁盘空间
df -h
```

**常见原因与解决方案**：

| 原因 | 解决方案 |
|------|----------|
| 权限不足 | `chmod -R 755 data/neo4j` |
| 磁盘空间不足 | 清理磁盘或扩展存储 |
| 内存不足 | 增加 Docker 内存限制 |
| 数据损坏 | `rm -rf data/neo4j/*` 后重新启动 |

### 问题 3: OpenZep 无法连接 Neo4j

**症状**：OpenZep 日志显示 `Failed to connect to Neo4j`

**排查步骤**：

```bash
# 1. 确认 Neo4j 已完全启动
docker compose logs neo4j | grep "Started"

# 2. 检查 Neo4j 端口
docker compose exec neo4j cypher-shell -u neo4j -p $NEO4J_PASSWORD "RETURN 1"

# 3. 检查 OpenZep 配置
docker compose exec openzep env | grep NEO4J
```

**解决方案**：

```bash
# 等待 Neo4j 完全启动后重启 OpenZep
docker compose restart openzep

# 或完全重建
docker compose down
docker compose up -d
```

### 问题 4: LLM API 调用失败

**症状**：MiroFish 界面显示错误，或对话无响应

**排查步骤**：

```bash
# 1. 检查 API Key 是否正确设置
grep LLM_API_KEY .env

# 2. 测试 API 连通性
curl -X POST "https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions" \
  -H "Authorization: Bearer $(grep LLM_API_KEY .env | cut -d= -f2)" \
  -H "Content-Type: application/json" \
  -d '{"model":"qwen-plus","messages":[{"role":"user","content":"hello"}]}'

# 3. 查看 MiroFish 日志
docker compose logs mirofish | tail -50
```

**常见原因**：
- API Key 错误或过期
- 阿里云账号欠费
- 网络无法访问 dashscope.aliyuncs.com

### 问题 5: 内存不足导致服务崩溃

**症状**：容器频繁重启，系统卡顿

**排查步骤**：

```bash
# 检查内存使用
vm_stat

# 检查 Docker 内存限制
docker info | grep -i memory
```

**解决方案**：

1. **增加 Docker 内存限制**（OrbStack）：
   - 打开 OrbStack > Settings > Resources
   - 将 Memory 调整为 8GB 或更高

2. **限制服务内存使用**（docker-compose.yml）：
   ```yaml
   services:
     neo4j:
       deploy:
         resources:
           limits:
             memory: 4G
   ```

---

## 常见问题 FAQ

### Q1: 可以使用其他 LLM 吗？

可以。修改 `.env` 中的以下配置：

```env
LLM_BASE_URL=https://api.openai.com/v1
LLM_API_KEY=sk-your-openai-key
LLM_MODEL=gpt-4
```

### Q2: 如何更新到最新版本？

```bash
# 拉取最新镜像
docker compose pull

# 重启服务
docker compose up -d

# 查看更新日志
docker compose logs -f
```

### Q3: 数据存储在哪里？如何备份？

数据存储在项目目录的 `data/` 文件夹中：

```bash
# 备份数据
tar -czvf mirofish-backup-$(date +%Y%m%d).tar.gz data/ uploads/

# 恢复数据
tar -xzvf mirofish-backup-20240315.tar.gz
```

### Q4: 如何完全卸载？

```bash
# 停止并删除容器
docker compose down

# 删除数据（谨慎操作）
rm -rf data/ uploads/

# 删除项目目录
cd ..
rm -rf mirofish/
```

### Q5: 可以在 Intel Mac 上运行吗？

不可以。本项目仅提供 ARM64 架构镜像。Intel Mac 用户需要：
1. 使用 Rosetta 运行（性能损失）
2. 或从上游仓库自行构建 x86 镜像

### Q6: 如何查看实时日志？

```bash
# 查看所有服务日志
docker compose logs -f

# 查看特定服务日志
docker compose logs -f mirofish
docker compose logs -f openzep
docker compose logs -f neo4j
```

---

## 维护操作

### 查看资源使用

```bash
# 容器资源使用
docker stats

# 磁盘使用
docker system df
```

### 清理旧数据

```bash
# 清理未使用的镜像
docker image prune -a

# 清理构建缓存
docker builder prune
```

### 重启单个服务

```bash
# 重启 MiroFish
docker compose restart mirofish

# 重启 OpenZep
docker compose restart openzep

# 重启 Neo4j
docker compose restart neo4j
```

### 进入容器调试

```bash
# 进入 MiroFish 容器
docker compose exec mirofish sh

# 进入 Neo4j 容器
docker compose exec neo4j bash

# 进入 OpenZep 容器
docker compose exec openzep sh
```

---

## 获取帮助

遇到问题？

1. 查看 [GitHub Issues](https://github.com/slashinchi/mirofish-arm64/issues)
2. 检查上游仓库文档：
   - [MiroFish](https://github.com/666ghj/MiroFish)
   - [OpenZep](https://github.com/N1nEmAn/openzep)
