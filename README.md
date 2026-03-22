# MiroFish ARM64

[![Build](https://github.com/slashinchi/mirofish-arm64/actions/workflows/build.yml/badge.svg)](https://github.com/slashinchi/mirofish-arm64/actions/workflows/build.yml)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

为 ARM64 平台适配的 MiroFish Docker 镜像。

## 这是什么

MiroFish 是一个 AI 驱动的客户访谈模拟系统。本项目提供 ARM64 架构的 Docker 镜像和本地化 OpenZep 知识图谱服务，方便在 ARM64 平台（如 Apple Silicon Mac、ARM64 Linux）工作的用户使用。

## 快速开始

```bash
# 1. 克隆仓库
git clone https://github.com/slashinchi/mirofish-arm64.git
cd mirofish-arm64

# 2. 准备数据目录
mkdir -p data/neo4j data/openzep uploads

# 3. 配置环境变量
cp .env.example .env
# 编辑 .env，填入 LLM API Key

# 4. 启动服务
docker compose up -d

# 5. 访问系统
open http://localhost:3000
```

## 系统要求

- **硬件**: ARM64 架构设备（Apple Silicon Mac M1/M2/M3/M4 或 ARM64 Linux）
- **内存**: 8GB 最低，16GB 推荐
- **Docker**: OrbStack 或 Docker Desktop

## 服务端口

| 端口 | 服务 | 说明 |
|------|------|------|
| 3000 | MiroFish | Web 界面 |
| 5001 | MiroFish API | 后端服务 |
| 8000 | OpenZep | 知识图谱服务 |
| 7474 | Neo4j | 图数据库 HTTP 控制台 |

## 环境变量

| 变量名 | 说明 | 示例 |
|--------|------|------|
| `LLM_API_KEY` | LLM API Key | `sk-xxxxxx` |
| `LLM_MODEL` | 模型名称 | `gpt-4o` |

## 模型选择

OpenZep 知识图谱服务需要 LLM 支持 JSON Schema 结构化输出。任何支持 JSON Schema 的 LLM 都可以使用。

### JSON Schema 支持判断标准

选择模型时，请确认以下能力：

| 能力 | 说明 |
|------|------|
| **结构化输出** | 支持通过 JSON Schema 约束响应格式 |
| **响应格式参数** | API 支持 `response_format: { type: "json_schema", ... }` |
| **严格模式** | 能够严格遵循 Schema 定义，不添加额外字段 |

### 配置示例

在 `.env` 文件中配置模型：

```bash
# 通用配置
LLM_API_KEY=your-api-key
LLM_MODEL=your-model-name

# 如果模型需要特殊参数（如关闭思考模式）
LLM_ENABLE_THINKING=false
```

### 常见问题

**Q: 为什么某些模型无法正常工作？**  
A: OpenZep 知识图谱服务需要 LLM 返回严格的 JSON 格式数据。只有支持 JSON Schema 响应格式的模型才能满足这一要求。

**Q: 如何判断模型是否支持 JSON Schema？**  
A: 查阅模型提供商的 API 文档，确认是否支持 `json_schema` 响应格式。如果启动后 OpenZep 服务报错或知识图谱功能异常，可能是当前模型不支持 JSON Schema。

**Q: 某些模型输出推理过程导致 JSON 解析失败怎么办？**  
A: 部分模型在思考模式下会输出推理过程，干扰结构化输出。尝试关闭思考模式或选择原生支持结构化输出的模型。

更多详细信息请参考 [部署指南](docs/deployment.md)。

## 文档

- [详细部署指南](docs/deployment.md)
- [更新日志](UPDATES.md) - 追踪上游项目的变更

## 技术栈

- https://github.com/666ghj/MiroFish
- https://github.com/N1nEmAn/openzep
- https://neo4j.com/

## 许可证

MIT License