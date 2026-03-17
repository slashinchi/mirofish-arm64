# MiroFish ARM64

[![Build](https://github.com/slashinchi/mirofish-arm64/actions/workflows/build.yml/badge.svg)](https://github.com/slashinchi/mirofish-arm64/actions/workflows/build.yml)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

为 Apple Silicon (M1/M2/M3/M4) 优化的 MiroFish 保险客户访谈模拟系统。

## 这是什么

MiroFish 是一个 AI 驱动的保险客户访谈模拟系统，帮助保险从业者通过模拟对话提升客户沟通技巧。本项目提供 ARM64 架构的 Docker 镜像，让 Mac 用户无需复杂配置即可本地运行。

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  MiroFish   │────▶│   OpenZep   │────▶│   Neo4j     │
│   (Web UI)  │     │(知识图谱API) │     │  (图数据库)  │
└─────────────┘     └─────────────┘     └─────────────┘
       │
       ▼
┌─────────────┐
│  阿里百炼    │
│  qwen-plus  │
└─────────────┘
```

## 快速开始

```bash
# 1. 克隆仓库
git clone https://github.com/slashinchi/mirofish-arm64.git
cd mirofish-arm64

# 2. 准备数据目录
mkdir -p data/neo4j data/openzep uploads

# 3. 配置环境变量
cp .env.example .env
# 编辑 .env，填入阿里百炼 API Key

# 4. 启动服务
docker compose up -d

# 5. 访问系统
open http://localhost:3000
```

## 系统要求

- **硬件**: Mac with Apple Silicon (M1/M2/M3/M4) 或 ARM64 Linux
- **内存**: 8GB 最低，16GB 推荐
- **Docker**: OrbStack (推荐) 或 Docker Desktop

## 服务端口

| 端口 | 服务 | 说明 |
|------|------|------|
| 3000 | MiroFish | Web 界面 |
| 5001 | MiroFish API | 后端服务 |
| 8000 | OpenZep | 知识图谱服务，可访问 `/docs` 查看 API 文档 |
| 7474 | Neo4j | 图数据库 HTTP 控制台 |

## 环境变量

| 变量名 | 说明 | 示例 |
|--------|------|------|
| `ZEP_API_URL` | OpenZep 服务地址 | `http://localhost:8000` |
| `OPENZEP_API_KEY` | OpenZep 认证密钥 | 自定义随机字符串 |
| `NEO4J_PASSWORD` | Neo4j 数据库密码 | 强密码 |
| `LLM_API_KEY` | 阿里百炼 API Key | `sk-xxxxxx` |
| `LLM_MODEL` | 模型名称 | `qwen-plus` |

## 文档

- [详细部署指南](docs/deployment.md) - 完整部署步骤、故障排查、FAQ

## 技术栈

- **MiroFish**: AI 保险访谈模拟系统 ([上游仓库](https://github.com/666ghj/MiroFish))
- **OpenZep**: 自托管知识图谱服务 ([上游仓库](https://github.com/N1nEmAn/openzep))
- **Neo4j**: 图数据库存储
- **阿里百炼**: LLM API (qwen-plus)

## 镜像

预构建镜像托管在 GitHub Container Registry:

```
ghcr.io/slashinchi/mirofish:latest
ghcr.io/slashinchi/openzep:latest
```

镜像每周自动更新，支持 `linux/arm64` 平台。

## 许可证

本项目仅包含部署配置，采用 MIT 许可证。MiroFish 和 OpenZep 遵循各自上游仓库的许可证。
