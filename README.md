# GitMap - GitHub 项目业务分析师

GitMap 是你的专属 AI 业务分析师，专为产品经理（PM）和非技术人员设计。它能深度阅读 GitHub 项目代码（无论是本地路径还是远程 URL），将晦涩的代码逻辑转化为清晰的业务文档和 Mermaid 图表。

## 核心价值

- **零技术门槛**：无需看懂代码，直接获取业务逻辑说明。
- **图文并茂**：自动生成大量 Mermaid 图表（流程图、时序图、类图、思维导图等）。
- **多维分析**：从项目全景、用户旅程、核心功能、数据模型等 6 大维度进行拆解。

## 功能特性

GitMap 会扫描项目结构，并在项目根目录下生成 `docs/explain/` 文件夹，包含以下 6 篇文档：

1. **00-overview.md**: 项目全景鸟瞰（产品功能全景、价值主张）
2. **01-user-journey.md**: 用户旅程地图（核心体验路径）
3. **02-core-features.md**: 核心功能详解（关键功能运作流程）
4. **03-data-model.md**: 数据关系图（实体关系、数据流向）
5. **04-module-architecture.md**: 模块架构图（模块依赖、技术栈）
6. **05-api-interactions.md**: 接口与集成图（API 调用、第三方集成）

## 如何使用

建议使用 **OpenCode** 运行此 Skill，当然 **Claude Code** 也是完全支持的。

1. **触发分析**：
   - 提供 GitHub URL，例如：`分析 https://github.com/username/repo`
   - 提供本地项目路径，例如：`帮我看看 /path/to/project 这个项目`
   - 直接询问：`用 Mermaid 梳理一下这个项目的业务逻辑`

2. **查看结果**：
   - 分析完成后，请查看生成的 `docs/explain/` 目录下的 Markdown 文件。

## 🌟 最佳实践：结合 Obsidian 使用

为了获得最佳的阅读体验，**强烈推荐结合 [Obsidian](https://obsidian.md/) 使用**。

### 为什么要用 Obsidian？
生成的文档中包含大量 **Mermaid** 代码块。Obsidian 对 Mermaid 有着极佳的原生支持，能够实时渲染出精美的流程图和架构图，让你享受到真正的“图文并茂”体验。

### 操作步骤
1. **生成文档**：让 GitMap 分析项目，生成 `docs/explain/` 文件夹。
2. **导入 Obsidian**：
   - **方法 A（推荐）**：直接将项目根目录（或 `docs/explain` 文件夹）作为 Vault（仓库）用 Obsidian 打开。
   - **方法 B**：将生成的 `docs/explain/` 文件夹复制到你现有的 Obsidian Vault 目录下。
3. **享受阅读**：在 Obsidian 中打开文档，你将看到代码逻辑被转化为直观的图表，配合详细的文字说明，让项目业务逻辑一目了然。

### 🚀 进阶玩法：使用 Claudian 插件
如果你希望获得极致的一体化体验，推荐安装 **[Claudian](https://github.com/YishenTu/claudian)** 插件。它能将 Claude Code 直接嵌入 Obsidian，让你无需离开笔记软件即可触发 GitMap 分析，生成的文档也会自动出现在你的 Vault 中，实现真正的“所见即所得”。

---
*让代码不再冰冷，让业务逻辑跃然纸上。*
