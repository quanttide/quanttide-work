# CHANGELOG

所有显著变更都将记录在此文件中。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。

版本遵循语义化版本规范：0.0.x（探索期）→ 0.x.y（验证期）→ x.y.z（正式期）

---

## [0.1.0] - 2026-09-29

### 新增

- 知识工作领域仓开张：按 apps、packages、examples、docs、data 五区组织，子模块各自独立演进（清单见 [README](README.md)）
- `.agents/skills/` 落 `context-to-profile` 技能：把语境流水认归属、粗加工后归位档案

### 说明

- 单一事实源：每个事实只在某一子模块里活一处，父仓只追踪引用、不存副本
