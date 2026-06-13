---
type: juejin-小册
title: SDD + Harness：用 AI 写出能交付的代码
topic: sdd-harness-fullstack
medium: 图文（Markdown）
demo_project: 简版小红书（图文社区 Demo，代号 RedLite）
demo_scope: 仅 登录注册 / 发帖表单 / 帖子列表 / 帖子详情（MVP，不含点赞评论关注）
articles: 24
status: draft-pending-review
generated: 2026-06-11
source: 由慕课视频课大纲(18章/118节)提纯重构；方法论内核复用 knowledge/sdd-harness-fullstack/_course-conventions.md
---

# 小册：SDD + Harness —— 用 AI 写出能交付的代码

## 一、小册定位（与慕课视频课的区别）
- **媒介**：图文为主（关键 Prompt + 关键代码 + 截图 + 清单），非录屏。
- **受众**：有一定基础、想把 AI 编码规范化的一线/进阶开发者（**不做零基础手把手**）。
- **读法**：阅读 + 照着抄 + 反复查，偏工具书。
- **取舍**：砍掉环境逐步演示、零基础铺垫、视频话术；**保留方法论内核 + 可复用清单/代码 + 一个最小闭环项目**。
- 单篇读 10–15min，结论先行、干货密度高、可收藏。

## 二、概念渐进设计（为什么这样排目录）

**核心原则：读者不需要提前知道任何术语，概念逐层引入。**

```
读者起点："我用 AI 写代码，但总觉得不靠谱"
         ↓
第1层：你在哪 → Vibe Coding（#1 定位现状）
         ↓
第2层：全局长什么样 → 全景图（#2 概念地图：Vibe/Spec/SDD/Harness/MCP/Skill/Agent Team 一句话解释）
         ↓
第3层：为什么不行 → Vibe Coding 三宗罪（#3 问题诊断）
         ↓
第4层：核心解法 → Spec（#4）→ Harness（#5）→ 组合（#6）
         ↓
第5层：动手准备 → 工具选型（#7）→ 环境（#8）→ 指令（#9）→ 跑通（#10）
         ↓
第6层：实战验证 → 用 RedLite 项目跑通五步闭环（#11-#16）
         ↓
第7层：能力扩展 → MCP（#17-18）→ Skill（#19-20）→ Agent Team（#21-22）
         ↓
读者终点："我能用 SDD+Harness 工程化 AI 开发，还能扩展到 MCP/Skill/Agent Team"
```

> **设计约束**：Part 4 的 MCP / Skill / Agent Team 在 #2 全景图里只给一句话定义（"知道有这个东西"），到 Part 4 才展开讲（"知道怎么用"）。读者在任何一篇往前翻，都不会遇到未解释的术语。

## 三、实战项目：简版小红书（代号 RedLite）
- **范围（MVP，只做这 4 个，严守不扩张）**：
  1. 登录注册（JWT 鉴权）
  2. 发帖表单（标题 + 正文 + 图片/封面）
  3. 帖子列表（信息流 + 分页）
  4. 帖子详情页（路由 + 详情展示）
- **为什么合适**：完整连贯的内容产品，4 个功能正好铺满 SDD+Harness 闭环（鉴权/表单/列表/详情）；
  与视频课的「权限模块(第7章) + 社交模块(第8章)」一致，是其**简化复用**版，不另起炉灶。
- **进阶 AI 落点天然**：
  - MCP → 接入外部图片/内容源，喂给 agent（Part 4 展开）
  - Skill → 帖子自动生成标题/标签，把经验打包复用（Part 4 展开）
  - Agent Team → 搭建多 Agent 全栈开发团队（Orchestrator + Frontend Agent + Backend Agent + Reviewer Agent）（Part 4 展开）
- **数据模型**：两张表 `user` + `post`，足够简单。
- 技术栈沿用课程约定（前端 Next.js + React + Tailwind + shadcn/ui；后端轻量接口 + JWT；Playwright E2E）。
- **明确不做**（留作练习/进阶）：点赞、评论、关注、私信、搜索。

## 四、大纲（24 篇 / 6 部分）

### Part 1 · 认知升级：从 Vibe Coding 到工程化 AI（6 篇）
> **读者进入时**：只知道"我用 AI 写代码"，不知道 SDD/Harness/MCP/Skill 是什么。
> **读完 Part 1 后**：理解为什么需要工程化、Spec 是什么、Harness 是什么、三者如何组合。

1. **你可能一直在 Vibe Coding——先看清你在哪**
   - 引入 Vibe Coding 概念（Karpathy 定义）+ AI 编程的三个阶段（补全→对话→Agent）

2. **AI 编程全景图：一张图看懂所有概念**
   - **核心产出：一张概念地图**，把所有术语用一句话解释并标注关系：
     - Spec = 你给 AI 的"施工合同"（写清楚做什么、做到什么程度算完）
     - SDD（Spec-Driven Development）= 用 Spec 驱动 AI 开发的方法论
     - Harness = 自动给 AI 加载规范的"工程护栏"（让 AI 不再自由发挥）
     - MCP（Model Context Protocol）= 让 AI 连接外部工具/数据的"插头协议"
     - Skill = 把可复用的 AI 操作经验打包成的"技能包"
     - Agent Team = 多个 AI Agent 通过 Orchestrator/消息传递/文件系统协作完成复杂任务的“多智能体系统”
   - 标注学习路径：基础（Spec + SDD + Harness）→ 实战 → 进阶（MCP + Skill + Agent Team）
   - 告诉读者：前 16 篇搞定基础，后 8 篇搞定进阶，不用一口气全学

3. **为什么 Vibe Coding 走不远：三宗罪（不稳定 / 不规范 / 难协作）**
   - 三宗罪的根因分析（概率采样 → 不稳定、无全局约束 → 不规范、知识孤岛 → 难协作）
   - 用 RedLite 发帖功能做对照实验（Vibe Prompt vs SDD Spec）
   - 结论：Vibe Coding 不是不好，是缺了工程化的"缰绳"

4. **Spec：把"感觉"变成"合同"——AI 时代最重要的文档**
   - 引入 Spec 概念：不是需求描述，是可验收的合同
   - Spec 四要素：模块边界 / 核心场景（WHEN/THEN/AND）/ 数据结构 / ACCEPTANCE
   - 给出 RedLite 登录模块的完整 Spec 示例
   - 技术溯源：Contract-First → BDD → SDD for AI

5. **Harness：给 AI 装工程护栏——让 AI 不再自由发挥**
   - 引入 Harness 概念（马具→缰绳→约束 AI 的行为边界）
   - Harness 三层机制：AGENTS.md（全局约束）+ frontend.md（前端规范）+ 自动校验重试
   - 给出 RedLite 的完整 Harness 配置（AGENTS.md + frontend.md）
   - 设计取舍：为什么"自动注入"比"更强的 Prompt"更好

6. **SDD+Harness 合体：从"能用"到"好用"的工程化方案**
   - 单独用的盲区（只有 Spec 没人监督 / 只有监督功能模糊）
   - 五步闭环：拆 Spec → 排计划 → 写代码 → Review → 测试
   - 效率对比（Vibe 180min vs SDD+Harness 75min）
   - 收尾：理论讲完了，接下来动手

### Part 2 · 上手准备（4 篇）
> **读者进入时**：理解方法论，但还没装工具、跑过代码。
> **读完 Part 2 后**：环境就绪，跑通了第一个 Spec → 代码。

7. **SDD 工具选型：Kiro / Spec-Kit / OpenSpec / Superpowers 一图选**
   - 五大工具的核心能力对比（Spec辅助/约束注入/自动校验/Agent无关/开源/锁定风险）
   - 选型决策树（团队规模 → 配置时间 → IDE统一 → 自动校验需求）
   - 本小册的方案：自研 Harness + opsx 指令集（跨工具通用的方法论）

8. **装好你的 AI 开发 Harness（环境只讲关键，不逐步手把手）**
   - 必备：Cursor / Claude Code + opsx 配置
   - 可选：Spec-Kit CLI / OpenSpec
   - 给出 RedLite 项目初始化命令

9. **opsx 指令速通：explore → propose → apply → archive**
   - 四条指令的核心作用 + 一句话类比（工头进场/排工期/施工/归档）
   - 每条指令的输入、输出、关键参数

10. **5 分钟跑通你的第一个 Spec → 代码**
    - 用一个极简 Spec（一个按钮组件）跑通 `/opsx:apply`
    - 体验 Harness 自动加载 AGENTS.md + frontend.md 的效果
    - 第一次感受"约束下的 AI 生成" vs "自由发挥"的差异

### Part 3 · 实战：用「简版小红书」跑通五步闭环（6 篇 · 核心）
> **读者进入时**：环境就绪，跑通过极简 Demo。
> **读完 Part 3 后**：用 RedLite 完整跑通五步闭环，理解了交叉 Review 和测试。

11. **立项与拆 Spec：把简版小红书拆成 4 个可交付单元（鉴权/发帖/列表/详情）**
    - 拆分三标准：独立可测试 / 数据边界清 / 可独立交付
    - 用 Prompt 让 AI 帮你拆，人工做判断
    - 给出 RedLite 的 5 个 Spec 单元（含卡片组件复用）

12. **排计划：先做哪个、依赖怎么排（鉴权 → 发帖 → 列表 → 详情）**
    - `/opsx:propose` 生成开发排期
    - 依赖链逻辑：为什么 auth 必须排在最前
    - 产出 `exec-plans/redlite-sprint.md`

13. **实战·登录注册：用 apply 驱动生成 + JWT 鉴权全流程（看三个 moment）**
    - 三个关键 moment：Spec → apply 生成 / Harness 自动校验 / 交叉 Review
    - 完整代码 + Spec 对账

14. **实战·发帖表单与后端存储：表单校验 + 接口 + post 表**
    - zod 校验 + shadcn/ui Form + API Route
    - 对比：有 Harness 约束 vs 无约束的生成差异

15. **实战·帖子列表与详情：信息流分页 + 详情页路由 + 复用卡片组件**
    - 分页组件 + 路由参数 + 卡片复用
    - Spec 粒度对 AI 输出质量的影响（实战验证）

16. **交叉 Review + 自动化测试 + 归档：Spec 是唯一真相源**
    - 双向对账：Spec→代码（❌ 未覆盖）+ 代码→Spec（⚠️ 超出Spec）
    - ACCEPTANCE → Playwright 测试
    - `/opsx:archive` 归档 + lessons-learned

### Part 4 · 进阶：让 AI 更强——MCP / Skill / Agent Team（6 篇）
> **读者进入时**：已掌握 SDD+Harness 基础闭环。
> **读完 Part 4 后**：理解 MCP/Skill/Agent Team 的技术原理、怎么自己搭建、怎么扩展 AI 能力。
> **术语引入策略**：每个概念先用"一句话回顾"（#2 全景图里的定义），再展开讲。

17. **MCP 是什么：让 AI 连接外部世界的"插头协议"**
    - 概念引入：AI 只能看你给它的文件 → MCP 让 AI 调外部 API/数据库/工具
    - 类比：MCP 之于 AI，就像 USB 之于电脑——标准化接口，即插即用
    - 实战：给 RedLite 接入一个图片源 MCP（生成帖子封面图）

18. **MCP 进阶：让 Agent 用真实外部数据**
    - 多 MCP 串联（图片源 + 内容审核 API）
    - MCP 的安全边界和权限控制

19. **Skill 是什么：把 AI 操作经验打包成可复用"技能包"**
    - 概念引入：你每次都要在 Prompt 里写"生成标题时注意 SEO"→ 打包成 Skill，AI 自动加载
    - 类比：Skill 之于 AI，就像宏/模板之于编辑器——一次定义，反复使用
    - 实战：写一个"帖子自动生成标题/标签"Skill

20. **Skill 进阶：内容审核 Skill + 打包复用 / 上传技能市场**
    - Skill 的组合和发布
    - 从个人 Skill 到团队共享

21. **Agent Team 技术原理：多 Agent 系统是怎么工作的**
    - **技术溯源**：从单 Agent 的局限性讲起（上下文窗口有限、角色混乱、串行执行瓶颈）→ 为什么需要多 Agent
    - **核心原理拆解**（三层递进）：
      - L1 用法层：Agent Team 的三种架构模式（Orchestrator 中心编排 / Pipeline 流水线 / Peer-to-Peer 对等协作）
      - L2 机制层：Agent 间通信的技术实现（消息传递 / 共享上下文 / 文件系统协作 / MCP 工具调用）
      - L3 设计层：为什么 Orchestrator 模式最适合全栈开发（中心化协调 > 去中心化通信，代价和取舍）
    - **配图**：三种架构模式的对比图（信息流向、瓶颈点、适合场景）
    - **独到见解**：为什么大多数 Agent Team 产品不好用——不是 Agent 不够多，而是缺少共享 Spec 作为"合同"

22. **实战：搭建一个 AI 全栈开发 Agent Team**
    - **架构设计**：为 RedLite 搭建一个 4-Agent 全栈开发团队
      - Orchestrator Agent（工头）：接收 Spec，分配任务，汇总结果
      - Frontend Agent：负责前端代码生成（加载 frontend.md + shadcn/ui 约束）
      - Backend Agent：负责 API Route + 数据库操作（加载 JWT/Prisma 约束）
      - Reviewer Agent：对照 Spec ACCEPTANCE 做交叉 Review（✅❌⚠️）
    - **技术实现**：
      - Agent 配置文件（每个 Agent 的 system prompt + 约束文件 + 可用工具）
      - 编排逻辑：Orchestrator 怎么解析 Spec → 怎么分配 → 怎么等所有 Agent 完成 → 怎么让 Reviewer 对账
      - 通信机制：通过文件系统协作（Spec 文件 + 代码目录 + Review 报告）
    - **端到端流水线**：“拆 Spec → 前端 Agent 生成组件 → 后端 Agent 生成 API → Reviewer Agent 对账”
    - **可抄走的硬货**：完整的 Agent Team 配置文件 + 编排脚本
    - **踩坑复盘**：Agent 间上下文丢失、重复生成、Review 漏检的技术原因和解决方案

### Part 5 · 工程化收尾（2 篇）
> 用 Spec 驱动迭代 + 部署上线，补完从开发到交付的最后一公里。

23. **用 Spec 驱动改需求：给帖子加封面图 —— 改而不乱 + 迭代护栏**
    - 需求变更时怎么改 Spec、怎么只改该改的代码
    - 验证 SDD 的"改而不乱"优势

24. **一键部署上线：让简版小红书能被访问**
    - Vercel 部署 + 环境变量配置
    - 从代码到可访问 URL 的最后一公里

### Part 6 · 转型与面试（2 篇，作为番外）
> 把 SDD+Harness 的经验转化为职业价值。

- 转型路线 + 把项目讲成面试亮点 / 简历
- 高频面试题精讲（SDD / MCP / Skill / Agent）

> ⚠️ Part 6 暂不计入 24 篇正编，作为番外内容后续补充。

## 五、每篇统一形态（掘金小册体例 · 7 段式）
1. **痛点开篇**（~200字）：一句话戳中读者真实困惑，给一个真实场景的反例
2. **技术溯源**（~300字）：这个技术/概念为什么存在？之前怎么做？配 `【图】`
3. **原理深度解析**（~600字）：三层递进（用法→机制→设计取舍），配 `【图】`
4. **实战落地**（~500字）：完整可运行代码，反例 vs 正例对照
5. **可抄走的硬货**（~300字）：完整 Prompt / 配置 / 模板
6. **速查清单 / 表格**（~200字）：checklist 或对照表
7. **独到小结 + 动手点**（~150字）：加粗结论 + 5 分钟小任务
> 允许 ✅❌⚠️ 作为功能标记（沿用课程既有风格）。

## 六、与视频课的复用关系
- **复用内核**：SDD / Harness / opsx 五步闭环 / 招牌金句（见 `_course-conventions.md`）。
- **复用模块**：视频课「权限(第7章)+社交(第8章)」→ 小册简版小红书 4 功能。
- **变的部分**：媒介(图文)、深度(精炼)、项目(简版小红书替代 WanderChina)、节奏(快)。
- **砍掉**：环境逐步演示、第13–16章 RAG/爬虫/迭代/DevOps 重内容（仅 24 篇点到）。

## 七、已产出文章与新编号映射

| 新编号 | 标题 | 旧文件 | 状态 |
|---|---|---|---|
| 01 | 你可能一直在 Vibe Coding | — | 待写 |
| 02 | AI 编程全景图 | — | 待写 |
| 03 | 为什么 Vibe Coding 走不远 | 01-why-vibe-coding-fails.md | ✅ 已写（需调整钩子） |
| 04 | Spec：把"感觉"变成"合同" | 02-sdd-spec-as-contract.md | ✅ 已写（需调整钩子） |
| 05 | Harness：给 AI 装工程护栏 | 03-harness-engineering-guardrails.md | ✅ 已写（需调整钩子） |
| 06 | SDD+Harness 合体 | 04-sdd-harness-combined.md | ✅ 已写（需调整钩子） |
| 07 | SDD 工具选型 | 05-sdd-tool-landscape.md | ✅ 已写（需移动到 Part 2） |
| 08–10 | 上手准备 | — | 待写 |
| 11 | 立项与拆 Spec | 09-split-spec.md | ✅ 已写（需调整编号） |
| 12–24 | 其余 | — | 待写 |

---
> 状态：小册大纲 v3（概念渐进式重构），待你审阅。
> 核心变化：新增 #1（定位现状）+ #2（概念全景图），Part 1 从 5→6 篇；Part 4 每个概念（MCP/Skill/Agent Team）先讲"是什么"再讲"怎么用"；工具选型移入 Part 2 作为上手准备第一步。
