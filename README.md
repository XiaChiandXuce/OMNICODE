# OMNICODE
An Omniscient AI Compiler IDE built on the IntelliJ Platform. Designed for enterprise-grade development, combining advanced language analysis, intelligent refactoring, AI agents, and secure model orchestration. For developers who demand precision and power.基于 IntelliJ 平台构建的全能 AI 编译器 IDE。面向企业级开发者，融合高级语言分析、智能重构、AI 多代理系统与安全的模型编排，专为追求精准与极致控制力的开发者而生。
![ChatGPT Image 2025年6月22日 10_16_26](https://github.com/user-attachments/assets/e0134657-e861-49ff-a4db-c4b2cc93e2c2)

Below is a **JetBrains-style mono-repo layout** for **OMNICODE**, aligned with the IntelliJ Platform (intellij-community) conventions but trimmed and enriched for an AI-native IDE.
*(English first, 中文在后)*

---

## 1. Top-level tree (English)

```
OMNICODE/
├─ .github/               # Workflows, issue templates, discussions
├─ buildSrc/              # Gradle build logic, Kotlin DSL, shared plugins
├─ PLATFORM/              # Git submodule or subtree of intellij-community
│   └─ (read-only)        # Rebased snapshots, updated via script
├─ omniplatform/          # Patched modules extending PLATFORM (branding, menus)
├─ plugins/               # AI & domain-specific plugins (Gradle sub-projects)
│   ├─ omnichat/          # LLM chat toolwindow
│   ├─ omnicomplete/      # Neural autocompletion engine
│   ├─ omnirefactor/      # AI-assisted refactorings
│   ├─ omnianalytics/     # Telemetry & usage insights
│   └─ ...
├─ ai-engine/             # Model routing, embeddings, vector search (Kotlin)
├─ agents/                # Multi-step AI agents (compile-test-fix loops)
├─ server/                # Optional self-hosted LLM gateway (Ktor)
├─ docs/                  # Guides, ADRs, UML/PlantUML diagrams
│   ├─ architecture/
│   └─ developer/
├─ scripts/               # JetBrains JBA tool, patching, sync helpers
├─ sandbox/               # IntelliJ “sandbox” for dev-run configs (git-ignored)
├─ .idea/                 # Gradle composite build shared run configs
├─ gradle.properties
├─ settings.gradle.kts
└─ README.md
```

### Key design choices

| Folder          | Purpose & relation to intellij-community                                                                            |
| --------------- | ------------------------------------------------------------------------------------------------------------------- |
| `PLATFORM/`     | Clean **submodule** of JetBrains CE; never edit directly.                                                           |
| `omniplatform/` | Thin **bridge layer** – branding (logo, splash), product info, license tweaks, custom VM options, launcher patches. |
| `plugins/`      | Each AI capability shipped as a **stand-alone IntelliJ plugin**; loaded via `product.xml`.                          |
| `ai-engine/`    | Pure Kotlin lib for model calls; shared with NEXAIDE via Gradle “included build”.                                   |
| `agents/`       | Coroutines + Flow orchestrators; compile, run tests, loop on fail.                                                  |
| `buildSrc/`     | Gradle convention plugins (`idea`, `intellij-plugin`, shadow, protobuf, detekt).                                    |
| `server/`       | Ktor app exposing `/chat`, `/completion`, `/embedding`; can be Docker-ised.                                         |

---

## 2. Gradle multi-project layout

* Root `settings.gradle.kts` **includes**:
  `PLATFORM`, `omniplatform`, every sub-plugin, `ai-engine`, `agents`, `server`.
* `gradle.properties` pins **JetBrains Runtime 17** & IntelliJ SDK version.
* `buildSrc/` adds tasks:
* `patchProductInfo` – rewrite product name/icon.
* `syncPlatform` – pull latest intellij-community & rebase patchset.
* `runOmniIDE` – launches IDE with all AI plugins + sandbox.

---

## 3. Quick build & run

```bash
# clone with submodule
git clone --recurse-submodules https://github.com/XiaChiandXuce/OMNICODE.git
cd OMNICODE

# install JBR 17 & Gradle (wrapper included)
./gradlew :omnipatform:prepareSandbox runIde   # first run

# build all installers
./gradlew buildAllDistribution
```

> **Requirements**: JDK 17+, 16 GB RAM (parallel compilation), \~20 GB disk.

---

## 4. Roadmap snippet (syncs with NEXAIDE)

| Milestone             | Core tasks                                    |
| --------------------- | --------------------------------------------- |
| **P0 Bootstrap**      | Submodule intellij-community; branding pass   |
| **P1 AI MVP**         | `omnichat` + `omnicomplete` using ai-engine   |
| **P2 Agent Loop**     | `agents/autofix` integrated with JUnit runner |
| **P3 Deep Refactor**  | PSI-aware AI refactorings, rename, extract    |
| **P4 Secure Routing** | On-prem LLM gateway, token audit              |
| **P5 Shared Core**    | Publish ai-engine to Maven; reused by NEXAIDE |

---

# 2. 中文版目录与说明

```
OMNICODE/
├─ .github/            # CI/CD、ISSUE 模板
├─ buildSrc/           # Gradle 脚本与公共插件
├─ PLATFORM/           # JetBrains 社区版源码（子模块只读）
├─ omniplatform/       # 品牌补丁、启动器、产品信息
├─ plugins/            # AI 插件子项目
│   ├─ omnichat/
│   ├─ omnicomplete/
│   ├─ omnirefactor/
│   └─ ...
├─ ai-engine/          # 模型路由、检索增强、RAG
├─ agents/             # 编译-测试-修复多智能体
├─ server/             # 可选 LLM 网关（Ktor）
├─ docs/               # 文档、架构决策 ADR
├─ scripts/            # 同步社区源码、打包脚本
└─ ...
```

### 构建与运行

```bash
git clone --recurse-submodules https://github.com/XiaChiandXuce/OMNICODE.git
cd OMNICODE
./gradlew runIde   # 开发模式，自动打开沙盒 IDE
```

* **最低 JDK17**，建议 16 GB 内存。
* 打包安装包：`./gradlew buildAllDistribution` （可加 `-PtargetOS=current`）。

---

## 决策亮点

* **子模块隔离**：官方社区源码不改动，方便拉取上游。
* **AI 能力插件化**：遵循 JetBrains 市场生态，允许企业按需启用。
* **双 IDE 共享**：`ai-engine` 作为独立 Kotlin Lib，与 NEXAIDE 共用，统一模型与 Prompt 逻辑。
* **安全可控**：企业可部署 `server/` 网关，管控模型密钥与访问日志。

---


````markdown
<!--
  OMNICODE – README
  English section comes first, then 中文部分保持结构对应，方便翻译维护。
-->

# OMNICODE  
*The Omniscient AI Compiler IDE for Mission-Critical Systems*

<p align="center">
  <img src="docs/assets/omnicode_banner.svg" width="640" alt="OMNICODE banner">
</p>

[![CI](https://github.com/XiaChiandXuce/OMNICODE/actions/workflows/ci.yml/badge.svg)](https://github.com/XiaChiandXuce/OMNICODE/actions)  
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)  
[![JetBrains SDK 2025.1](https://img.shields.io/badge/IntelliJ%20Platform-2025.1+-blueviolet)](https://plugins.jetbrains.com/)  

---

## ✨ Why OMNICODE?

> **Precision, Power, Privacy.**  
> OMNICODE merges the industrial-grade IntelliJ Platform with large-language-model intelligence.

* **Deep PSI Awareness** – Leverages IntelliJ PSI trees for context-accurate generation & refactorings.  
* **Enterprise-grade Security** – On-prem model gateway, token audit, opt-in telemetry.  
* **AI Agents** – Compile-test-fix loops to keep the build green before you press *Run*.  
* **Bring-Your-Own LLM** – GPT-4o, Claude 3.5, Llama 3, Gemini… route per task & cost.  
* **Plugin-first** – All AI features are shipped as sandboxed IntelliJ plugins; extend at will.  

MIT licensed and forever open-source.  

---

## 🗂 Repository layout (monorepo)

| Path                     | Purpose                                                |
|--------------------------|--------------------------------------------------------|
| `PLATFORM/`              | Read-only git-submodule of `intellij-community`        |
| `omniplatform/`          | Branding / product patches / launchers                |
| `plugins/`               | AI plugins (`omnichat`, `omnicomplete`, …)            |
| `ai-engine/`             | Model routing, embeddings, RAG (pure Kotlin lib)      |
| `agents/`                | Multi-step compile–test–fix orchestrators             |
| `server/`                | Optional Ktor LLM gateway (Docker-ready)              |
| `buildSrc/`              | Gradle convention plugins, shared tasks               |
| `docs/`                  | Guides, ADRs, diagrams                                 |
| `sandbox/`               | IntelliJ sandbox for dev-run (git-ignored)            |

---

## 🚀 Quick Start (developer mode)

```bash
# 1 Clone with submodule
git clone --recurse-submodules https://github.com/XiaChiandXuce/OMNICODE.git
cd OMNICODE

# 2 Build & run sandbox IDE (first launch downloads JBR 17)
./gradlew :omniplatform:prepareSandbox runIde
````

> **Requirements**: JDK 17+, 16 GB RAM, \~20 GB disk.
> First start may take a few minutes while Gradle downloads the IntelliJ SDK.

---

## 🧠 Architecture at a glance

```
┌────────────────────────┐
│  Launcher & JBR 17     │
└──────────┬─────────────┘
           |
┌──────────▼───────────┐
│  Omniplatform (IDE)  │  ← patched IDEA CE
│  ─ workspace UI      │
│  ─ PSI / Indexing    │
└──────┬─────┬─────────┘
       |     |
       |     | IntelliJ Plugin API
       |     |
 ┌─────▼──┐ ┌▼──────────────┐
 │plugins │ │ai-engine (lib)│
 └─────┬──┘ └─────▲─────────┘
       | RPC / Ktor
 ┌─────▼────────────────────┐
 │   server (LLM gateway)   │  ← optional, on-prem
 └──────────────────────────┘
```

---

## 🔧 Configuration (env vars)

| Variable           | Description                           |
| ------------------ | ------------------------------------- |
| `OMNI_OPENAI_KEY`  | Secret for OpenAI GPT-4o              |
| `OMNI_MODEL_ROUTE` | JSON routing rules (task → provider)  |
| `OMNI_VECTOR_PATH` | Embedding DB location (Chroma/SQlite) |
| `OMNI_TELEMETRY`   | `off` \| `anonymous` \| `full`        |

Configure under **Settings / Omni AI** or export as env.

---

## 📅 Roadmap highlights

| Phase  | Features                                   |
| ------ | ------------------------------------------ |
| **P0** | Platform fork & branding ✔                 |
| **P1** | Chat + Completion MVP (GPT-4o/Ollama)      |
| **P2** | Auto-Fix Agent integrated with JUnit       |
| **P3** | PSI-aware AI Refactorings (rename/extract) |
| **P4** | Secure LLM Gateway, token budget panel     |
| **P5** | Shared `ai-engine` with **NEXAIDE**        |

Full milestone board lives in **Projects → 2025 Roadmap**.

---

## 🤝 Contributing

1. Pick an issue labelled **good first issue**
2. Fork → `git checkout -b feat/xyz`
3. `./gradlew test` must pass
4. Open PR – CI will verify & build installers
5. Merged? Welcome to the contributors list! 🎉

Dev-container & ADR workflow: [`docs/contributing.md`](docs/contributing.md)

---

## 🔒 License

OMNICODE is distributed under the **MIT License**.
Third-party code retains its original licenses (see `ThirdPartyNotices.md`).

---

# OMNICODE 简体中文简介

> **全能 AI 编译器 IDE**——在 IntelliJ 平台的工业级能力之上，注入大模型智能，为严苛生产场景而生。

## ✨ 主要特性

* **深度 PSI 语义**：精准上下文理解，智能重构更可信。
* **安全可控**：私有部署 LLM 网关，令牌预算审计。
* **智能体循环**：编译-测试-修复自动迭代，构建灯常绿。
* **模型自由切换**：GPT-4o、Claude、Llama、Gemini 等随心路由。
* **插件生态**：所有 AI 能力以插件形式发布，可按需启用/禁用。

## 🗂 目录结构

与英文版表格对应，不再赘述。

## 🚀 本地运行

```bash
git clone --recurse-submodules https://github.com/XiaChiandXuce/OMNICODE.git
cd OMNICODE
./gradlew runIde           # 沙盒模式启动 IDE
```

* 需 JDK 17+、16 GB 内存。
* 打包安装包：`./gradlew buildAllDistribution`.

## 📅 路线图（摘要）

| 阶段 | 目标               |
| -- | ---------------- |
| P0 | 完成平台 Fork、品牌替换   |
| P1 | 聊天 & 补全 MVP      |
| P2 | 自动修复智能体          |
| P3 | PSI-级智能重构        |
| P4 | LLM 网关 & 财务预算    |
| P5 | 与 NEXAIDE 共享核心引擎 |

## 🤝 贡献指南

参见 `docs/contributing.md`，欢迎 PR！

## 🔒 许可证

本项目采用 **MIT License**，开源共享，商业可用，需保留版权声明。

---

<p align="center"><i>Understand Everything. Compile Anything.</i></p>
```
