# 更新日志

本文件自动记录上游项目的更新。

---
## 2026-04-10

### OpenZep

- `697d71b` - Remove: .env.bak
- `ea17d41` - Merge pull request #9 from Samge0/dev-patch-1

---

## 2026-04-05

### MiroFish

- `7c7c7a2` - fix(deps): pin axios version to prevent potential supply chain risks
- `c8a1bd5` - feat(i18n): add shared translation files and language registry
- `2ffadd3` - feat(i18n): add Accept-Language header to all API requests
- `0c18e1a` - feat(i18n): add backend translation utility with shared locale files
- `22bf50f` - feat(i18n): set up vue-i18n with dynamic locale loading
- `8f6110d` - feat(i18n): inject language instruction into LLM system prompts
- `3d5e5d0` - feat(i18n): add language switcher component to navigation
- `74f673a` - feat(i18n): replace hardcoded Chinese in backend API responses with t() calls
- `7083382` - feat(i18n): replace hardcoded Chinese in frontend views with i18n calls
- `fc47ae8` - feat(i18n): replace hardcoded Chinese in frontend components with i18n calls

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

- `985f89f` - fix: resolve 500 error caused by `ылки` tags and markdown code fences in content field from reasoning models
- `a1ff79c` - Update README

---

## 初始版本

- 基于 MiroFish 和 OpenZep 的 ARM64 适配版本
- 支持 Apple Silicon Mac 和 ARM64 Linux
- Docker Compose 一键部署
