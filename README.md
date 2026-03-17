# MiroFish ARM64 Deployment

This repository contains ARM64-compatible Docker configurations for deploying MiroFish with OpenZep.

## Architecture

- **MiroFish**: AI-powered insurance customer interview prediction system
- **OpenZep**: Self-hosted Zep Cloud alternative (knowledge graph storage)
- **Neo4j**: Graph database (bundled with OpenZep)

## Prerequisites

- Mac with Apple Silicon (M1/M2/M3/M4) or other ARM64 device
- OrbStack or Docker Desktop
- OpenAI-compatible API (e.g., Alibaba Qwen via 阿里百炼)

## Quick Start

```bash
# 1. Clone repository
git clone https://github.com/slashinchi/mirofish-arm64.git
cd mirofish-arm64

# 2. Create data directories
mkdir -p data/neo4j data/openzep uploads

# 3. Configure environment
cp .env.example .env
# Edit .env with your API keys

# 4. Start services
docker compose up -d

# 5. Access MiroFish
open http://localhost:3000
```

## Documentation

- [详细部署指南](docs/deployment.md) - 完整部署步骤和常见问题

## Ports

| Port | Service | Description |
|------|---------|-------------|
| 3000 | MiroFish Frontend | Web UI |
| 5001 | MiroFish Backend | API |
| 8000 | OpenZep | Knowledge Graph API |
| 7474 | Neo4j HTTP | Web Console |
| 7687 | Neo4j Bolt | Database connection |

## Image Building

Images are automatically built via GitHub Actions:

- **Trigger**: Push tag `v*` or manual workflow dispatch
- **Registry**: `ghcr.io/slashinchi/mirofish` and `ghcr.io/slashinchi/openzep`
- **Platform**: `linux/arm64`

## License

This repository contains deployment configurations only. MiroFish and OpenZep have their own licenses:

- MiroFish: https://github.com/666ghj/MiroFish
- OpenZep: https://github.com/N1nEmAn/openzep