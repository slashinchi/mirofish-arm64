# 更新日志

本文件自动记录上游项目的更新。

---

## 2026-03-20

### MiroFish

- `1536a79` - fix(readme): update Discord link to valid invite URL

### OpenZep

- `e3e1d54` - fix: import add_single_episode and add pydantic dependency for macOS compatibility
- `d50b846` - Merge pull request #6 from mangostar714/fix/add_single_episode_import_macos

---

## 2026-03-17

### OpenZep

- `7805835` - fix: return content and created_at in /graph response for zep-cloud SDK 3.13.0 compatibility
- `84ef0c9` - Auto-fix Docker OpenZep endpoint for MiroFish

---

## 2026-03-05

### MiroFish

- `985f89f` - fix: resolve 500 error caused by `<think>` tags and markdown code fences in content field from reasoning models
- `a1ff79c` - Update README

---

## 初始版本

- 基于 MiroFish 和 OpenZep 的 ARM64 适配版本
- 支持 Apple Silicon Mac 和 ARM64 Linux
- Docker Compose 一键部署