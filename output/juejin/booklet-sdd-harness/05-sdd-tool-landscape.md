---
booklet: "SDD + Harness：用 AI 写出能交付的代码"
part: 2
article: 7
title: "SDD 工具选型：Kiro / Spec-Kit / OpenSpec / Superpowers 一图选"
prev: "06-sdd-harness-combined"
next: "08-setup-ai-harness"
demo_project: "RedLite（简版图文社区）"
---

# SDD 工具选型：Kiro / Spec-Kit / OpenSpec / Superpowers 一图选

## 痛点开篇

Part 1（#1~#6）我们讲完了理论：Vibe Coding 的问题、Spec 是什么、Harness 是什么、两者怎么组合。现在进入 **Part 2 上手准备**，第一步就是选工具。

前四篇我们讲完了 SDD+Harness 的方法论。你现在可能已经打开浏览器搜“SDD 工具”，然后被一堆名字搞晕了：Kiro、Spec-Kit、OpenSpec、Superpowers、Tessl、BMAD……每个都号称“重新定义 AI 编程”，每个都有自己的 Spec 格式和指令集。

**别慌。** 这些工具的核心能力都围绕同一件事——让 AI 按照 Spec 生成代码——只是切入点、成熟度和适用场景不同。

这篇给你一张选型地图，5 分钟做出决策。然后从下一篇开始，我们就装环境、配 Harness、跑通第一个 Spec→代码。

【图：选型地图：SDD 工具四象限图——X轴：轻量←→重量（学习成本/配置复杂度），Y轴：IDE绑定←→Agent无关（可迁移性）。Kiro在右上（重量+IDE绑定），Spec-Kit在右下（重量+Agent无关），OpenSpec在左下（轻量+Agent无关），Superpowers在左中（中量+Agent无关），Tessl在右上（重量+IDE绑定）】

## 技术溯源：SDD 工具为什么会百花齐放

2025 年是 SDD 工具的"寒武纪大爆发"。GitHub 推出 Spec-Kit、AWS 发布 Kiro、Fission-AI 开源 OpenSpec，各路人马涌入。原因很简单：

**Vibe Coding 的问题被广泛认知后，"怎么让 AI 按规格干活"变成了一个刚需，但没人定义标准。**

这就像 2010 年前后的前端框架大战——jQuery/MooTools/Dojo/Prototype 都在解决"DOM 操作太烦"的问题，最终 React 胜出是因为它提出了"组件化+虚拟 DOM"这个更本质的抽象。SDD 工具也在经历同样的过程：当前是"百团大战"阶段，最终会有 2-3 个主流方案沉淀下来。

在选型之前，你需要理解 SDD 工具的核心能力矩阵——不是所有工具都覆盖全部能力：

| 核心能力 | 说明 | 对应我们讲的 |
|---|---|---|
| **Spec 编写辅助** | 帮你写/生成 Spec 文档 | 第二篇 SDD |
| **约束文件注入** | 自动加载 AGENTS.md/frontend.md | 第三篇 Harness |
| **自动校验** | 对照 ACCEPTANCE 检查生成结果 | 第四篇五步闭环 |
| **Agent 无关** | 可用任何 AI Agent（Cursor/Claude Code/Copilot） | 可迁移性 |
| **IDE 绑定** | 只能在特定 IDE 内使用 | 锁定风险 |

## 原理深度解析：五大工具逐一拆解

### 1. Kiro（AWS 出品）—— 最重但最完整

**定位**：内置 SDD 工作流的 Agentic IDE（基于 VS Code 分支）

**核心机制**：
- **Specs**：内置 Spec 编辑器，支持 Requirements → Design → Task 三层结构
- **Steering**：类似 AGENTS.md 的约束文件，自动注入 AI 的 System Context
- **Hooks**：事件驱动的自动化（如"文件保存时自动运行测试"、"Spec 变更时自动更新设计文档"）
- **Agent 能力**：内置 AI Agent，支持 MCP（Model Context Protocol）

```
Kiro 的执行链路：
Requirements（需求）→ Spec（规格文档）→ Design（技术设计）→ Tasks（任务分解）→ Code（代码生成）
     ↑                                                                                    |
     └───────────────────── Hooks 自动反馈 ─────────────────────────────────────────────────┘
```

**优势**：开箱即用，不需要手动配置约束文件注入——Steering 是 IDE 原生功能
**劣势**：IDE 绑定（只能用 Kiro），锁定风险高；目前仍在快速迭代，API 可能变动

> **我的判断**：如果你的团队已经决定"All-in AWS 生态"且接受 IDE 锁定，Kiro 是当前最完整的 SDD 方案。如果你需要跨 IDE 灵活性（比如团队有人用 Cursor 有人用 VS Code），Kiro 不是最佳选择。

### 2. Spec-Kit（GitHub 出品）—— Agent 无关的标准化工具

**定位**：Agent-agnostic 的 SDD 工具包，CLI 驱动

**核心机制**：
- **Constitution**：项目级原则文件（类似 AGENTS.md），通过 `/speckit.constitution` 生成
- **Specify**：Spec 编写辅助，通过 `/speckit.specify` 从需求描述生成 Spec
- **Plan**：任务分解，通过 `/speckit.plan` 将 Spec 拆为可执行任务
- **Implement**：代码生成，通过 `/speckit.implement` 按 Spec 生成代码
- 支持 30+ AI Coding Agent（Cursor/Claude Code/Copilot/Cline 等）

**优势**：Agent 无关（换 AI Agent 不影响工作流）、开源（GitHub 官方维护）、社区活跃
**劣势**：学习曲线略陡（需要理解 Constitution/Specify/Plan/Implement 四层）、CLI 依赖

> **我的判断**：Spec-Kit 是当前"Agent 无关"方向最成熟的方案。如果你不想被任何 IDE 或 AI Agent 绑定，选 Spec-Kit。代价是前期需要花 1-2 天学会 CLI 和配置。

### 3. OpenSpec（Fission-AI 开源）—— 最轻量的迭代式方案

**定位**：轻量级 SDD 框架，"agree before you build"

**核心机制**：
- **Proposals**：提案文档（类似需求描述），记录"为什么做"
- **Specs**：规格文档（类似我们的 Spec），记录"做什么"
- **Design**：设计文档，记录"怎么做"
- **Tasks**：任务分解，记录"分几步做"
- 四层文档逐步细化，每层需要人工确认后才进入下一层

**优势**：极轻量（零配置，放几个 Markdown 文件即可）、迭代式（每层确认后再推进）、Agent 无关
**劣势**：没有自动校验机制（ACCEPTANCE 需要人工检查）、没有约束文件自动注入

> **我的判断**：OpenSpec 适合"想快速试用 SDD 但不想重配置"的场景。如果你的项目只有 2-3 个模块，OpenSpec 够用；如果模块多且需要自动化校验，需要搭配 Harness 机制。

### 4. Superpowers —— 把 SDD 做成"技能包"

**定位**：AI Agent 的增强技能集（不是独立工具，而是 Agent 的插件）

**核心机制**：
- 以"Skill"为单位组织 SDD 能力（如 spec-writing skill、code-review skill、test-gen skill）
- 每个 Skill 是一个 Markdown 文件，描述 AI 在执行该技能时应遵循的流程和约束
- 可以被 Agent 按需调用（如"我现在需要写 Spec" → 激活 spec-writing skill）

**优势**：灵活（可以只激活需要的 Skill）、可组合（多个 Skill 可串联）、可共享（Skill 可上传社区）
**劣势**：不是完整的 SDD 框架（需要自己组合 Skill）、缺少自动校验和约束注入

> **我的判断**：Superpowers 适合"已经有 AI Agent 工作流，想加入 SDD 能力"的场景。它不是从零搭建 SDD 环境的工具，而是给现有工作流"加buff"的插件。我们本小册使用的 Harness 方案就借鉴了 Superpowers 的 Skill 理念。

### 5. Tessl —— 企业级 SDD 平台

**定位**：面向企业团队的 SDD 平台（商业产品）

**核心机制**：
- Spec → Design → Implementation 三层结构
- 内置代码审查和合规检查
- 支持团队协作（多人共享 Spec、分配 Task）

**优势**：企业级功能完善（权限管理、审计、合规）
**劣势**：商业产品（非开源）、学习成本高、适合大团队

> **我的判断**：Tessl 适合 20+ 人的工程团队、对合规有要求的企业场景。个人开发者或小团队没必要引入这个复杂度。

【图：对比表格图：五大工具核心能力矩阵——行是工具名（Kiro/Spec-Kit/OpenSpec/Superpowers/Tessl），列是核心能力（Spec辅助/约束注入/自动校验/Agent无关/IDE绑定/开源/适合规模），用✅❌⚠️标记】

## 实战落地：RedLite 的选型决策

对于 RedLite 这个项目，我们的选型是：

**核心方案：自研 Harness 机制 + Spec 格式（本小册方案）**

为什么不直接选现成工具？因为本小册的目标是**教你 SDD+Harness 的方法论**，而不是某个特定工具的使用教程。我们用的 Spec 格式（模块边界/核心场景/数据结构/ACCEPTANCE 四要素）和 Harness 机制（AGENTS.md + frontend.md 自动注入）是跨工具通用的——你学会后，可以无缝迁移到 Spec-Kit、Kiro 或 OpenSpec。

**opsx 指令集**（`/opsx:explore`、`/opsx:propose`、`/opsx:apply`、`/opsx:archive`）本质上就是本小册自定义的 Harness 实现。

如果你想在本小册基础上使用现成工具做增强，我的推荐：

| 场景 | 推荐工具 | 理由 |
|---|---|---|
| 个人项目、想快速上手 | OpenSpec | 零配置，放几个 Markdown 就行 |
| 想系统学习 SDD、Agent 灵活切换 | Spec-Kit | Agent 无关、社区活跃、GitHub 官方 |
| 团队 All-in AWS、接受 IDE 锁定 | Kiro | 最完整的开箱即用方案 |
| 已有 Agent 工作流，想加 SDD 能力 | Superpowers | 插件式，不改变现有工作流 |
| 20+ 人团队、合规要求 | Tessl | 企业级功能 |

## 可抄走的硬货：SDD 工具选型决策树

```markdown
# SDD 工具选型决策树（直接抄走）

Q1: 你的团队规模？
├── 1-3 人 → Q2
├── 4-10 人 → Q3
└── 10+ 人 → Tessl 或 Spec-Kit（团队协作）

Q2: 你想投入多少配置时间？
├── < 30 分钟 → OpenSpec（零配置）
├── 1-2 天 → Spec-Kit（CLI 学习曲线）
└── 愿意锁定 IDE → Kiro（开箱即用）

Q3: 团队是否统一 IDE？
├── 全部用 VS Code/Cursor → 可以用 Kiro
├── IDE 混用（Cursor + WebStorm + Vim） → Spec-Kit（Agent 无关）
└── 全部用 AWS 生态 → Kiro

Q4: 你需要自动校验（ACCEPTANCE 自动检查）吗？
├── 需要 → Kiro 或 Spec-Kit + 自定义 Harness
└── 不需要（人工 Review 即可） → OpenSpec 或 Superpowers
```

## 速查清单：五大工具能力对照表

| 能力 | Kiro | Spec-Kit | OpenSpec | Superpowers | Tessl |
|---|---|---|---|---|---|
| **Spec 编写辅助** | ✅ 内置编辑器 | ✅ CLI 生成 | ✅ Markdown | ⚠️ 按 Skill | ✅ 内置 |
| **约束文件注入** | ✅ Steering 原生 | ✅ Constitution | ❌ 手动 | ⚠️ Skill 注入 | ✅ 内置 |
| **自动校验** | ✅ Hooks 驱动 | ✅ Implement 验证 | ❌ 无 | ❌ 无 | ✅ 内置 |
| **Agent 无关** | ❌ IDE 绑定 | ✅ 30+ Agent | ✅ 任意 Agent | ✅ 任意 Agent | ❌ 平台绑定 |
| **开源** | ❌ | ✅ GitHub 官方 | ✅ Fission-AI | ✅ 社区 | ❌ |
| **学习成本** | 中（IDE 上手） | 高（CLI + 概念） | 低（放文件） | 低（装 Skill） | 高（平台学习） |
| **适合规模** | 小-中团队 | 中-大团队 | 个人/小团队 | 个人/小团队 | 大团队 |
| **锁定风险** | 高（IDE绑定） | 低 | 低 | 低 | 高（平台绑定） |

## 独到小结 + 动手点

**选型的核心判断框架不是"哪个工具最好"，而是"我愿意用什么换什么"。** 愿意牺牲灵活性换开箱即用 → Kiro；愿意牺牲开箱即用换灵活性 → Spec-Kit；愿意牺牲自动化换零配置 → OpenSpec。没有银弹，只有取舍。

**动手点**：用上面的决策树，为你当前的项目（不一定是 RedLite）做出选型决策。如果选不出来，先用 OpenSpec（最低成本试错），体验一轮 Spec → Code 的流程，再决定是否升级到 Spec-Kit 或 Kiro。

---

> Part 2 第一步完成：工具选型已定。下一篇：**装好你的 AI 开发 Harness 环境**——我们安装必备工具、初始化 RedLite 项目、配好 AGENTS.md + frontend.md。
