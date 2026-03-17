# MiroFish ARM64 Deployment

This repository contains ARM64-compatible Docker configurations for deploying MiroFish with OpenZep.

## Architecture

- **MiroFish**: AI-powered insurance customer interview prediction system
- **OpenZep**: Self-hosted Zep Cloud alternative (knowledge graph storage)
- **Neo4j**: Graph database (bundled with OpenZep)

## Prerequisites

- OrbStack or Docker Desktop (ARM64)
- OpenAI API Key or compatible endpoint
- Qwen API Key (optional, for Qwen models)

## Quick Start

```bash
# Clone repository
git clone https://github.com/slashinchi/mirofish-arm64.git
cd mirofish-arm64

# Copy and configure environment
cp .env.example .env
# Edit .env with your API keys

# Start services
docker-compose up -d
```

## Documentation

See `docs/` directory for detailed deployment guides.

## License

This project is for personal/deployment use. MiroFish and OpenZep have their own licenses.