# MiroFish ARM64

[![Build](https://github.com/slashinchi/mirofish-arm64/actions/workflows/build.yml/badge.svg)](https://github.com/slashinchi/mirofish-arm64/actions/workflows/build.yml)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

为 Apple Silicon (M1/M2/M3/M4) 优化的 MiroFish 保险客户访谈模拟系统。

## 这是什么

MiroFish 是一个 AI 驱动的保险客户访谈模拟系统，帮助保险从业者通过模拟对话提升客户沟通技巧。本项目提供 ARM64 架构的 Docker 镜像，让 Mac 用户无需复杂配置即可本地运行。

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
| 8000 | OpenZep | 知识图谱服务 |
| 7474 | Neo4j | 图数据库 HTTP 控制台 |

## 环境变量

| 变量名 | 说明 | 示例 |
|--------|------|------|
| `LLM_API_KEY` | 阿里百炼 API Key | `sk-xxxxxx` |
| `LLM_MODEL` | 模型名称 | `qwen-plus` |

## 模型选择

MiroFish 依赖阿里百炼 API 提供 AI 能力。不同模型对结构化输出（JSON Schema）的支持程度不同，这直接影响 OpenZep 知识图谱服务的正常工作。

### 推荐模型（已验证）

以下模型已验证支持 `json_schema` 模式，可确保系统正常运行：

| 模型 | 状态 | 备注 |
|------|------|------|
| `glm-5` | ✅ 推荐 | 非思考模式 |
| `glm-4.7` | ✅ 推荐 | 非思考模式 |
| `qwen3-max-2026-01-23` | ✅ 可用 | 必须设置 `enable_thinking=false` |
| `qwen3.5-plus` | ✅ 可用 | 必须设置 `enable_thinking=false` |

### 待确认模型

以下模型尚未完全验证，可能支持 `json_schema`：

| 模型 | 状态 | 备注 |
|------|------|------|
| `kimi-k2.5` | ⚠️ 待确认 | 需要测试验证 |
| `MiniMax-M2.5` | ⚠️ 待确认 | 需要测试验证 |

### 配置示例

在 `.env` 文件中配置模型：

```bash
# 使用 GLM 系列（推荐）
LLM_MODEL=glm-5

# 或使用 Qwen 系列（需关闭思考模式）
LLM_MODEL=qwen3-max-2026-01-23
LLM_ENABLE_THINKING=false
```

### 常见问题

**Q: 为什么某些模型无法正常工作？**  
A: OpenZep 知识图谱服务需要 LLM 返回严格的 JSON 格式数据。只有支持 `json_schema` 响应格式的模型才能满足这一要求。

**Q: 如何判断模型是否支持 json_schema？**  
A: 如果启动后 OpenZep 服务报错或知识图谱功能异常，可能是当前模型不支持 `json_schema`。请切换到上述推荐模型。

**Q: Qwen 系列为什么要关闭思考模式？**  
A: Qwen 模型在思考模式下会输出推理过程，这会干扰 JSON Schema 的结构化输出。设置 `enable_thinking=false` 可禁用思考模式，确保输出格式正确。

更多详细信息请参考 [部署指南](docs/deployment.md)。

## 文档

- [详细部署指南](docs/deployment.md)

## 技术栈

- **MiroFish**: AI 保险访谈模拟系统
- **OpenZep**: 自托管知识图谱服务
- **Neo4j**: 图数据库存储
- **阿里百炼**: LLM API

## 许可证

MIT License