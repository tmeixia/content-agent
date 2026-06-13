---
stage: 2-script
course: SDD + Harness 规范化的 AI 全栈项目实战开发
chapter: 第11章 基于 Agent Teams 全栈实践用户模块开发：个人中心、消息通知、站内信
source_atom: ch11-l01~ch11-l09
status: draft-pending-review
generated: 2026-06-12
case: WanderChina
repo: https://github.com/sqlyouran/first
planned_min: 95
style_ref: 学习自《第6章_讲师稿_精简版》 + content-agent/skills/imooc-script
---

# 第11章：基于 Agent Teams 全栈实践用户模块开发

- **全章时长**：约 95 分钟（15 + 15 + 5 + 10 + 10 + 10 + 15 + 5 + 10）
- **案例**：WanderChina 用户模块：个人中心、消息通知、站内信
- **技术栈**：Qoder Agent Teams + OpenSpec + Superpowers Skills + Spring Boot 3.3.x + Next.js 16 / React 19 + Playwright / Vitest
- **一句话定位**：前面我们学会了让一个 AI 干活，这一章我们升级成让一支 AI 研发团队交付端到端能力。

> 讲师备注：本章先讲概念，再演示实战。听众假设为零基础或非 AI 方向同学，所以 Agent、Skill、通信协议、并行开发都要用生活类比讲透，不要默认大家懂“多智能体”。

---

## 第1节（15min）：如何对项目进行 AI 自迭代？多 Agent 协作如何解决研发效率瓶颈？

### 【讲师稿】

这一章开始，我们要把项目推进到一个新阶段。

前面几章，我们已经用 SDD + Harness 做了首页、登录注册、帖子、景点这些模块。你会发现，我们其实已经很接近一个真实产品了。WanderChina 不再只是一个 Demo，它开始有页面、有接口、有登录、有内容、有测试。

但到这里，一个新问题出现了。

**项目越像真实项目，一个 AI 会话就越不够用了。**

我先问大家一个问题：你有没有遇到过这种情况？

你让 AI 写前端，它写得挺好；你再让它写后端，它也能写；你再让它补测试，它开始有点飘；你再让它修 bug，它突然把前面写好的东西改坏了。（互动停顿）

我给你讲个很真实的翻车经历。

我之前做一个模块，本来只是想让 AI 从 Spec 开始，顺手把前端、后端、测试都做掉。刚开始很顺，像开了加速器。结果会话聊到后半段，AI 开始“忘事”：前面说过接口返回字段叫 `displayName`，后面它自己改成了 `nickname`；前面说前端不能写业务后端逻辑，后面它又想在 Next.js 里面偷偷加 Route Handler。

你看，AI 不是突然变笨了。

**AI 不是不聪明，是一个会话装不下整个公司。**

我们可以把单 Agent 想象成什么？想象成一个特别能干的实习生。你让他写方案，他能写；让他做页面，他也能做；让他写接口，他咬咬牙也能写。问题是，你不能让一个实习生同时当产品经理、交互设计师、前端工程师、后端工程师、测试工程师，还要求他一天不犯错。

这不是实习生的问题，这是组织方式的问题。

所以这一章我们引出一个新概念：**Agent Teams**。

Agent，先别被这个词吓到。用最通俗的话说：

**Agent 就是“会使用工具、能按目标持续干活的 AI 数字员工”。**

它不是单纯问答机器人。问答机器人像你问路，它回答一句。Agent 更像你请了一个人来办事：它知道目标，会读文件，会跑命令，会检查结果，必要时还会回来问你。

那 Agent Teams 是什么？

**Agent Teams 就是一支由多个 AI 数字员工组成的研发团队。**

这里顺手做一个知识普及：Agent Team 是谁提出的？

严格说，它不是某一个人拍脑袋发明出来的专有名词。更早在人工智能领域，就有“多智能体系统”这个方向，研究的是多个智能体如何分工、协作、博弈、完成复杂任务。到了大模型时代，这件事变得更实用了。

2023 年左右，像 Microsoft AutoGen 这类框架开始把“多个 LLM Agent 通过对话协作完成任务”做成开发框架；后面 AutoAgents、CrewAI、LangGraph 这一类工具，又把角色、任务、流程、编排这些东西进一步产品化、工程化。

所以我们在课里说 Agent Teams，不是说某个固定品牌，也不是说必须用某一个框架。我们讲的是一个通用思想：

**把复杂任务拆给多个有角色、有工具、有交付物的 AI Agent 协作完成。**

如果你是零基础，可以先把它理解成“AI 版项目小组”。一个人负责想清楚需求，一个人负责设计体验，一个人负责前端，一个人负责后端，一个人负责测试。它们不是随便聊天，而是围绕同一份 Spec 接力交付。

我们不再让一个 AI 从头扛到尾，而是拆成几个角色：

- 产品 Agent：把一句话需求变成边界清楚的 PRD。
- 交互 Agent：把 PRD 变成页面流程、状态流转和异常场景。
- 前端 Agent：按 Spec 和交互稿实现 Next.js 页面。
- 后端 Agent：按 Spec 实现 Spring Boot API。
- 测试 Agent：写 E2E、跑测试、出验收报告。
- 体验 Agent：检查文案、一致性、空态、加载态和可访问性。

你会发现，这就很像真实公司。

一个产品需求进来，不是程序员直接开写。正常流程应该是：先有人讲清楚要解决谁的问题，再有人设计怎么用，再有人分别实现前端和后端，最后有人验收。AI 时代不是把这个流程扔掉，而是把这个流程数字化。

**AI 不是让团队消失，而是让一个人也能调度一支团队。**

那它主要解决什么问题？我给大家压成四个词：

第一，**上下文爆炸**。一个会话装太多需求、代码、错误日志、历史决策，AI 后面就容易乱。拆成多个 Agent，每个 Agent 只背自己那部分上下文，压力小很多。

第二，**角色混乱**。同一个 Agent 一会儿当产品，一会儿当前端，一会儿当测试，很容易标准切换。多 Agent 可以让每个角色有稳定职责。

第三，**串行太慢**。一个 Agent 做完前端再做后端再做测试，像一个窗口排长队。Agent Team 可以在 Spec 稳定后让前端、后端、测试并行推进。

第四，**质量没人兜底**。单 Agent 自己写、自己检查，容易“自己给自己打满分”。多 Agent 可以让 reviewer、tester 独立验收，减少漏项。

带来的收益也很直接：

- 速度收益：能并行的任务并行做。
- 质量收益：不同角色互相校验。
- 可维护收益：每个产出都有交接物，不靠脑补。
- 学习收益：你能看见一个真实研发团队从需求到上线的完整链路。

但我也提前说一句：收益不是白来的。它会增加一点前期组织成本。所以本章会教你什么时候该上团队，什么时候一个 Agent 就够。

这一章我们的目标，就是用 WanderChina 的用户模块来演示这件事。

需求很具体：给外国人来中国旅行这个产品增加用户模块，包括个人中心、消息通知、站内信。

这三个子需求很适合做 Agent Team：

个人中心偏用户资料和页面展示；消息通知偏系统事件和未读状态；站内信偏用户之间的消息会话。它们有关联，但又能拆开并行做。

你可以把它想象成装修一套房子。以前你找一个师傅，让他同时做设计、水电、木工、油漆、验收。师傅再厉害，也容易乱。现在我们请一个小施工队：设计师画图，水电师傅走线，木工做柜子，监理验收。你作为业主，不需要自己搬砖，但你要看图纸、看样板间、最后签字。

在 SDD + Harness 里，这个“图纸”就是 Spec。

**团队越大，Spec 越重要。Spec 是所有 Agent 之间的合同。**

这一章的主线很简单：

第一步，理解 Agent 和 Agent Teams。

第二步，给项目加数字成员。

第三步，把数字成员编成产品、交互、前端、后端、测试这样的全栈研发团队。

第四步，建立他们之间的交接单和调度规则。

第五步，让团队真正交付用户模块。

最后我们总结：什么场景适合多 Agent，什么场景还是单 Agent 更划算。

### 【屏幕操作：展示本章地图】（约 1min）

```text
招人：创建 Agent
定岗：产品 / 交互 / 前端 / 后端 / 测试 / 体验
建制度：通信协议 + 任务调度
发装备：Skills + Rules
试用：单 Agent vs Agent Teams
接单：用户模块端到端交付
复盘：最佳实践
```

口播：

“你看，这一章其实不是在讲一个工具按钮怎么点，而是在讲一种新的研发组织方式。以前是一人一枪，现在是小队协同。但注意，主角不是 AI，主角还是你。你从写代码的人，变成了定规则、签字、验收的人。”

**本节金句：单 Agent 是一个全能实习生，Agent Teams 是一支迷你研发团队。**

下一节，我们先做第一件事：给项目增加一个 AI 数字成员。

---

## 第2节（15min）：给项目增加一个 AI 数字成员，理解 Skill 在 Agent 能力扩展中的核心作用

### 【讲师稿】

上一节我们讲了 Agent Teams 的概念。这一节，我们不空谈，先给项目招第一个“数字员工”。

我们先打开 WanderChina 的父仓。注意，这个仓库不是只放业务代码，它是一个 AIWorkSpace。业务代码在 `backend/` 和 `frontend/` 两个 submodule 里，父仓主要放治理层。

治理层有三块：

- `.qoder/rules/`：规则，像员工手册。
- `.qoder/skills/`：技能，像培训教材。
- `.qoder/agents/`：数字员工定义，像岗位 JD。

这里我要先把三个概念讲清楚，因为很多同学第一次听会混。

**Rule 是员工手册，Skill 是培训教材，Agent 是具体上岗的人。**

我们再加一个大家最熟的词：Prompt。

很多同学一开始会问：Skill 不就是 Prompt 吗？

不完全是。

Prompt 更像你临时对员工说的一句话：“帮我写个测试”“帮我改下页面”。它是一次性的指令。

Skill 更像公司沉淀下来的 SOP。比如“我们公司怎么做 TDD”“怎么做 code review”“怎么用 OpenSpec 做变更”。它不是只服务一次任务，而是以后每次遇到类似场景都能复用。

Rule 更像红线。比如“业务逻辑不能写进 Next.js Route Handler”“改代码前必须走 OpenSpec”。它不是建议，是长期约束。

Agent 更像一个岗位上的人。比如 `spec-reviewer` 这个 Agent，它可以读取 Rule，也可以调用 Skill，但它自己的身份很明确：只做 Spec 合规审查。

所以四者关系可以这样记：

```text
Prompt：这一次怎么吩咐
Skill：同类任务怎么做
Rule：永远不能破的规矩
Agent：谁来执行这件事
```

打个比方。你开一家奶茶店：

Prompt 是你对店员说“这杯少冰、三分糖”。

Skill 是店里的制作手册，珍珠怎么煮，奶盖怎么打，出杯前怎么检查。

Rule 是食品安全规定，过期原料不能用。

Agent 是具体岗位，收银员、制作员、质检员。

你看，少了 Prompt，店员不知道这单客人要什么。少了 Skill，每个人做法不一样。少了 Rule，质量和安全没底线。少了 Agent，没人对结果负责。

**Skill 的价值，不是让 AI 多背几句话，而是把一次经验变成可复用能力。**

比如公司规定“代码改动前必须先走 Spec”，这是 Rule。它对所有人都生效。

比如“怎么做 TDD”“怎么做 code review”“怎么用 git worktree”，这是 Skill。它是一套方法论，需要时拿出来用。

比如“你是一个专门检查 Spec 是否被代码覆盖的 reviewer”，这就是 Agent。它是一个具体角色，有名字、有职责、有输出格式。

我们现在先创建一个最简单也最实用的 Agent：`spec-reviewer`。

为什么先建它？

因为多 Agent 团队最怕的不是写得慢，而是各写各的。产品说 A，前端做 B，后端返回 C，测试断言 D，最后合在一起全错。`spec-reviewer` 的作用就是盯住一件事：代码有没有偏离 Spec。

### 【演示——创建第一个数字成员】（约 5min）

屏幕展示仓库：

```text
.qoder/
  agents/
    README.md
  skills/
  rules/
openspec/
backend/
frontend/
```

口播：

“大家看，`.qoder/agents/README.md` 里已经告诉我们：每个 subagent 就是一个 `.md` 文件，前面放 frontmatter，后面写系统提示词。这就像填一张入职表。”

演示 Prompt：

```text
请在 .qoder/agents/ 下创建一个 spec-reviewer.md。

目标：
这个 Agent 专门检查一次代码改动是否严格符合 OpenSpec 的 proposal/design/tasks。

要求：
1. frontmatter 包含 name、description、tools。
2. 系统提示词要说明它只做 Spec 合规检查，不做代码风格泛泛点评。
3. 输出必须包含：
   - 总体结论：PASS / FAIL
   - Spec 覆盖情况：✅已覆盖 / ❌未覆盖
   - 超范围实现：⚠️超出Spec
   - 必须修复项
4. 口径遵循本仓库 AGENTS.md 和 .qoder/rules/spec-driven-workflow.md。
```

AI 可能输出：

```markdown
---
name: spec-reviewer
description: 检查代码改动是否符合 OpenSpec 变更文档，适合实现完成后、合并前调用
tools: [read_file, grep_code, search_codebase]
---

# 系统提示词

你是 WanderChina 项目的 Spec 合规审查 subagent。

你的任务不是重写代码，而是检查当前改动是否严格符合：
- openspec/changes/<change>/proposal.md
- openspec/changes/<change>/design.md
- openspec/changes/<change>/tasks.md

返回格式：
1. 总体结论：PASS / FAIL
2. Spec 覆盖情况
3. 超范围实现
4. 必须修复项
```

### 【AI 输出 + 讲师点评】（约 3min）

口播：

“我们来点评一下。第一，`description` 写得不错，说明了什么时候调用。第二，职责边界也比较清楚，它不负责泛泛地夸代码，也不负责重新实现。第三，输出格式有了，但还可以更硬一点。”

这里我会纠偏一句：

```text
请补充：如果发现代码实现了 design.md 没有定义的功能，即使功能看起来更好，也必须标记为 ⚠️超出Spec；不要用“用户可能需要”作为放行理由。
```

口播：

“这句很重要。AI 很喜欢‘顺手优化’，但在 SDD 里，不在 Spec 里的好功能，先不要。因为团队协作里最怕的就是每个人都觉得自己在做好事，最后产品失控。”

**Spec 是唯一真相源，不在 Spec 里的功能再好也先停下。**

### 【讲 Skill：数字员工怎么学本事】（约 4min）

接着我们看 Skill。

Skill 不是一个人，Skill 是一套可复用能力。

比如本仓库已经有这些：

- `brainstorming`：帮我们把模糊想法问清楚。
- `writing-plans`：把想法转成计划。
- `test-driven-development`：强制 RED → GREEN → REFACTOR。
- `subagent-driven-development`：派发子任务，做二阶段评审。
- `using-git-worktrees`：用隔离分支并行开发。
- `verification-before-completion`：完成前做验证。

你可以这样理解：

Agent 像一个员工，Skill 像员工会的 SOP。

一个前端 Agent，如果没有 Skill，就像“会写代码但不知道公司流程的人”。你让它做页面，它可能上来就写。加了 Skill，它才知道先读 Spec、先写测试、再实现、最后验证。

所以我们后面搭团队时，不是只写几个 Agent 名字就完了，而是要给不同角色配不同 Skill。

产品 Agent 要会 brainstorming 和 writing-plans。

前端 Agent 要会 test-driven-development，还要读 frontend-conventions。

测试 Agent 要会 verification-before-completion。

**角色定岗位，Skill 定本事。**

这里再补一个判断标准：什么时候应该写成 Skill？

如果只是一次性任务，比如“把这个按钮文案改成 Start Planning”，用 Prompt 就够了。

如果这件事以后会反复出现，比如“每次开发前都要先看 Spec”“每次完成后都要跑测试并总结失败原因”“每次做前端都要遵守样式规范”，那就值得沉淀成 Skill 或 Rule。

**Prompt 解决一次问题，Skill 解决一类问题。**

下一节，我们把这些数字成员编成一个真正的研发团队。

---

## 第3节（5min）：将若干数字人编成团队，如何基于项目需求设计 Agent Teams 的角色和明确分工？

### 【讲师稿】

这一节我们先不急着写文件，先想一个更底层的问题：

如果要做“用户模块”，到底需要哪些角色？

很多初学者会说：前端、后端、测试。

这三个当然要有，但还不够。

因为软件交付不等于写代码。一个功能从想法到上线，中间至少经历五件事：

第一，明确做什么、不做什么。

第二，设计用户怎么操作。

第三，前端把页面做出来。

第四，后端把数据和接口撑起来。

第五，测试验证它真的能用。

所以我们这一章的 Agent Team，用五个核心角色打底：

```text
产品 Agent → 交互 Agent → 前端 Agent
                         → 后端 Agent
                         → 测试 Agent
```

再加一个体验 Agent，做最后的文案、视觉一致性、空态、错误态检查。

这就像开餐厅。

产品 Agent 是店长，决定今天卖什么菜、卖给谁、哪些菜今天不做。

交互 Agent 是菜单设计师，决定客人怎么点餐、点错了怎么办、没菜了怎么提示。

前端 Agent 是前厅，把菜单、桌子、服务流程摆出来。

后端 Agent 是后厨，真正处理库存、订单、出餐。

测试 Agent 是试吃员，按菜单一项项验收。

体验 Agent 是巡店经理，看标识清不清楚、桌面干不干净、客人会不会迷路。

**好团队不是人多，是每个人的输出刚好是下一个人的输入。**

对用户模块来说，我们可以这样分工：

| 角色 | 输入 | 输出 |
|---|---|---|
| 产品 Agent | “做用户模块”这一句话 | PRD、用户故事、功能边界 |
| 交互 Agent | PRD | 页面流程、状态流转、异常路径 |
| 前端 Agent | Spec + 交互稿 | Next.js 页面和组件 |
| 后端 Agent | Spec | Spring Boot API、实体、服务 |
| 测试 Agent | Spec | E2E 测试和验收报告 |
| 体验 Agent | 页面和交互 | 文案、一致性、可访问性走查 |

注意，角色不是按“谁厉害”切，而是按“交付物”切。

每个角色都要有明确输出。没有输出，就没法交接。

下一节，我们讲最关键的一件事：这些数字人之间怎么交流。

---

## 第4节（10min）：数字人团队如何交流？如何对 Agent Teams 的通信协议与任务调度机制进行配置？

### 【讲师稿】

这一节我们解决一个很现实的问题：

多个 AI 怎么协作？

大家先别想得太玄。AI 和 AI 之间不是坐在会议室里聊天，它们靠的是结构化交接单。

就像真实公司里，产品不能只在群里说一句“做个用户中心吧”。这句话太虚了。前端听完不知道几个页面，后端听完不知道几个接口，测试听完不知道怎么验收。

所以我们要给 Agent Team 设计通信协议。

通信协议听起来像网络编程，其实在这里就是：**每次派活都用同一张任务单。**

本仓库的 `subagent-driven-development` Skill 里已经给了一个模板：

```text
Task: <task-id from tasks.md>
Inputs:
  - design.md 相关章节
  - tasks.md 该条目
  - 涉及的源文件路径
Output:
  - 改动文件列表
  - 测试结果摘要
  - 偏离 design 的说明（若有）
```

这张表很朴素，但非常关键。

它解决三个问题：

第一，任务是什么。

第二，依据是什么。

第三，交付什么。

你看，这就像外卖小票。厨师不用猜你要什么，小票上写了菜名、备注、桌号。小票写错，菜就容易上错。

**AI 团队的沟通不靠默契，靠交接单。**

### 【屏幕操作：展示 subagent-driven-development Skill】（约 2min）

口播：

“这里还有一个重要设计，叫二阶段评审。第一阶段检查 Spec 合规，第二阶段检查代码质量。任一不过，就退回当前 Agent 修复，主上下文不接收脏代码。”

```text
实现 Agent → spec-reviewer → code-reviewer → 主线合并
              不通过退回       不通过退回
```

这很像工地监理。

施工队说“我干完了”，不能直接交房。先看有没有按图纸施工，再看施工质量有没有问题。图纸没按，返工；质量不过，也返工。

在 SDD + Harness 里：

- `proposal.md` 说明为什么做。
- `design.md` 说明怎么做。
- `tasks.md` 说明拆成哪些步骤。
- Spec reviewer 检查有没有偏。
- Code reviewer 检查质量。

### 【任务调度机制】（约 4min）

接着讲调度。

不是所有角色都能同时开工。

产品 Agent 必须先做，因为没有边界，后面都不知道做什么。

交互 Agent 必须在产品之后，因为它要基于 PRD 设计流程。

前端和后端可以在 Spec 稳定后并行，因为它们可以围绕同一个 OpenAPI 契约协作。

测试 Agent 可以先根据 Spec 写测试，也可以在实现后补验收，但更推荐提前参与，因为测试其实就是把 Spec 翻译成可执行检查。

所以用户模块的调度可以画成这样：

```text
产品 Agent
   ↓
交互 Agent
   ↓
OpenSpec /opsx:propose
   ↓
人类签字
   ↓
前端 Agent  ─┐
后端 Agent  ─┼→ 联调 → 测试 Agent → 体验 Agent → Review → Archive
测试 Agent  ─┘
```

这里有一个词叫并行。

并行不是大家一起乱改同一个文件。真正的并行要隔离。

本仓库有 `using-git-worktrees` Skill。它的作用就是给不同任务开不同工作区。你可以把它想象成一个工地分了三个施工区：个人中心一块、通知一块、站内信一块。每个施工队在自己的区域干活，不要互相踩脚。

**并行的前提不是人多，而是边界清楚。**

下一节，我们给这些角色发装备：用 Skills 构建角色能力。

---

## 第5节（10min）：如何通过 Skills 构建项目 Agent Teams 的角色能力？

### 【讲师稿】

前面我们定了角色，也定了交接规则。现在的问题是：每个 Agent 到底会什么？

这就要靠 Skills。

我们先看一个角色能力矩阵：

| 角色 | 主要 Skills / Rules | 重点能力 |
|---|---|---|
| 产品 Agent | brainstorming、writing-plans、spec-driven-workflow | 问清需求、定义边界、拆用户故事 |
| 交互 Agent | frontend-conventions、styling-conventions | 页面流程、状态设计、可用性 |
| 前端 Agent | test-driven-development、frontend-conventions | Next.js 页面、组件、前端测试 |
| 后端 Agent | test-driven-development、backend/api/database conventions | Spring Boot API、数据模型、单测 |
| 测试 Agent | verification-before-completion、test-driven-development | E2E、回归、验收报告 |
| 体验 Agent | styling-conventions、frontend-conventions | 文案、一致性、空态、错误态 |

注意，不是 Skill 越多越好。

新同学很容易犯一个错：给每个 Agent 塞一堆能力，恨不得全员全能。结果每个人都能做一点，每个人都边界不清。

这就像公司招聘。你招一个前端，不会要求他同时负责法务、财务、市场投放。不是他不能学，而是岗位职责不应该混。

**Agent 的能力越清楚，越不容易乱发挥。**

### 【演示——创建产品 Agent】（约 5min）

我们先创建一个产品 Agent。

演示 Prompt：

```text
请为 WanderChina 创建 .qoder/agents/product-manager.md。

定位：
你是产品 Agent，负责把一句话需求转成可进入 OpenSpec 的产品 brief。

职责：
1. 追问模糊需求，但不要无限追问，能合理假设就继续。
2. 输出目标用户、核心场景、功能边界、非目标、用户故事。
3. 必须把需求拆成可独立交付的 capability。
4. 输出要服务后续 /opsx:propose，不直接写代码。

输出格式：
- 背景
- 目标用户
- 用户故事
- In Scope
- Out of Scope
- Capability 拆分建议
- 验收口径草案

约束：
遵循 AGENTS.md、openspec/project.md 和 .qoder/rules/spec-driven-workflow.md。
```

AI 可能输出：

```markdown
---
name: product-manager
description: 将模糊业务需求整理为可进入 OpenSpec 的产品 brief，适合 propose 前调用
tools: [read_file, grep_code, search_codebase]
---

# 系统提示词

你是 WanderChina 项目的产品 Agent。
你的任务是把一句话需求整理成产品 brief，不直接写代码。

每次输出必须包含：
- 背景
- 目标用户
- 用户故事
- In Scope
- Out of Scope
- Capability 拆分建议
- 验收口径草案
```

讲师点评：

“这份已经能用了。但我会再补一句：如果需求和 `openspec/project.md` 的产品范围冲突，要主动标出来。比如当前项目早期 `project.md` 写过 UGC 社区不在范围内，如果我们后面要做站内信、通知，就必须明确这是第 11 章的范围扩展，而不是悄悄写进去。”

人工纠偏 Prompt：

```text
请补充一条约束：
如果新需求和 openspec/project.md 的既有 out of scope 冲突，必须在输出中增加“范围变更提醒”，建议先走 propose 更新项目范围，不允许直接默认通过。
```

口播：

“这就是 Agent Team 的好处。我们不是让 AI 盲目听话，而是让它帮我们发现边界冲突。一个成熟的产品 Agent，不是你说什么它都说好，而是会提醒你：老板，这件事会影响项目范围。”

**好的 Agent 不是更会写代码，而是更会守边界。**

下一节，我们做一个对比实验：单 Agent 和 Agent Teams，到底差在哪。

---

## 第6节（10min）：对比单 Agent 开发与多 Agent 协作的效率和质量有哪些差异？

### 【讲师稿】

讲到这里，大家可能会问一个很实际的问题：

老师，多 Agent 听起来很高级，但会不会太麻烦？

这个问题问得特别好。

我先给结论：

**多 Agent 不是银弹，小任务不要上团队，复杂任务才值得扩编。**

我们拿用户模块里的“消息通知”做一个对比实验。

单 Agent 做法是什么？

你开一个会话，对 AI 说：

```text
给 WanderChina 增加消息通知功能，包括通知列表、未读数量、标记已读、后端接口和前端页面，顺便补测试。
```

这个写法看起来很爽，但风险也很明显：

- 它可能先写页面，后想接口，字段对不上。
- 它可能只写正常路径，忘了空态和错误态。
- 它可能为了跑通页面，临时 mock 一堆数据，最后忘了接后端。
- 它可能测试只测“页面能渲染”，不测“标记已读”。

这就像让一个人同时做导演、演员、摄影、剪辑。短视频可以，电影不行。

Agent Team 做法是：

产品 Agent 先写清楚通知功能边界：

- 系统通知，不做营销推送。
- 有列表、未读数、标记已读。
- 通知由后端 API 提供。
- 前端必须有空态、加载态、错误态。

交互 Agent 再补状态：

- 没通知显示什么？
- 加载中显示什么？
- 请求失败怎么重试？
- 点击标记已读后 UI 怎么变化？

后端 Agent 做 API。

前端 Agent 做页面。

测试 Agent 根据 Spec 写 E2E。

你看，团队方式不是为了“仪式感”，而是为了减少漏项。

### 【屏幕展示：对比表】（约 3min）

| 维度 | 单 Agent | Agent Teams |
|---|---|---|
| 启动速度 | 快 | 慢一点 |
| 上下文压力 | 高 | 分散 |
| 角色边界 | 容易混 | 清楚 |
| 漏场景风险 | 高 | 低 |
| 返工概率 | 高 | 低 |
| 适用场景 | 小修小补 | 多页面、多接口、多测试的复杂需求 |

我们再换成学员最关心的收益语言：

第一，**你少盯人了。** 单 Agent 开发时，你要不停提醒它“别忘了测试”“别乱改接口”“别超出需求”。Agent Team 里，这些提醒可以分给 spec-reviewer、test-runner、ux-reviewer。

第二，**你少返工了。** 因为产品边界、交互状态、接口契约、测试标准都提前拆出来，后面不是边写边猜。

第三，**你更像真实研发了。** 不是只看 AI 生成一段代码，而是看到需求怎么变成 Spec，Spec 怎么变成任务，任务怎么变成前后端代码，代码怎么被测试和验收。

第四，**你学到的是迁移能力。** 这一章我们做的是用户模块，但方法可以迁移到订单模块、支付模块、后台管理模块、AI 助手模块。角色和流程可以复用，业务换了也能跑。

口播：

“所以别神化多 Agent。修一个按钮文案，单 Agent 甚至你自己改最快。做用户模块这种涉及产品边界、交互、前端、后端、测试的事情，多 Agent 才划算。”

判断标准也给大家：

适合单 Agent：

- 2 分钟以内的小任务。
- 改文案、改样式、小 bug。
- 没有明显跨角色协作。

适合 Agent Teams：

- 多页面 + 多接口 + 多测试。
- 需要先拆需求再实现。
- 任务可以并行。
- 主会话上下文快满。
- 你希望每个环节都有独立审查。

**多 Agent 不是为了炫技，是为了把复杂任务拆成可控交付。**

下一节，我们进入本章大实战：用 Agent Teams 驱动用户模块开发。

---

## 第7节（15min）：Agent Teams 驱动用户模块开发

### 【讲师稿】

现在正式接单。

需求是：给 WanderChina 增加用户模块，包括个人中心、消息通知、站内信。

先别急着写代码。我们仍然遵守 SDD + Harness 的五步闭环：

```text
拆 Spec → 排计划 → 写代码 → Review → 测试
```

只不过这次，不再是一个 AI 从头干到尾，而是一支 Agent Team 分工完成。

在进入演示前，我们先把“理论”和“实战”对上号。否则大家容易听懂概念，但不知道落到项目里怎么用。

| 前面讲的理论 | 在本节实战里对应什么 |
|---|---|
| Agent Team | 产品、交互、前端、后端、测试、体验六个数字成员 |
| Skill | TDD、worktree、code review、verification 这些可复用方法 |
| Rule | AGENTS.md 和 `.qoder/rules/` 里的工程红线 |
| 通信协议 | Task / Inputs / Output 交接单 |
| Spec 是合同 | `openspec/changes/<name>/proposal.md/design.md/tasks.md` |
| 人类负责人 | 签字需求、验收 Demo、批准合并 |

所以这节你不要只看“AI 生成了什么代码”。更重要的是看这个链路：

```text
一句话需求
  ↓ 产品 Agent
产品 brief
  ↓ 交互 Agent
页面流程
  ↓ OpenSpec
proposal / design / tasks
  ↓ 前端 Agent + 后端 Agent
代码实现
  ↓ 测试 Agent + 体验 Agent
验收报告
  ↓ 人类决策
合并归档
```

**这一章学会的不是某个按钮，而是把 AI 研发团队从理论开到项目里的完整方法。**

### 【演示 1：产品 Agent 生成产品 brief】（约 4min）

演示 Prompt：

```text
调用 product-manager Agent，为 WanderChina 设计用户模块产品 brief。

背景：
WanderChina 是面向境外用户的入境中国旅行目的地探索平台。
当前已经有首页、登录认证、内容/景点相关能力。

本次需求：
新增用户模块，包括：
1. 个人中心：展示用户资料、旅行偏好、收藏入口。
2. 消息通知：展示系统通知、未读数量、标记已读。
3. 站内信：用户之间可以查看会话和消息详情。

要求：
1. 先指出这是否涉及项目范围扩展。
2. 拆成可独立交付的 capability。
3. 每个 capability 给出 In Scope / Out of Scope。
4. 输出后续可喂给 /opsx:propose 的 brief。
5. 不写代码。
```

AI 输出关键片段：

```markdown
## 范围变更提醒
当前 project.md 曾将 UGC 社区列为 out of scope。
站内信和通知属于用户互动能力扩展，建议通过 OpenSpec propose 显式更新范围。

## Capability 拆分建议
1. user-profile
2. user-notifications
3. user-messages

## user-notifications
In Scope:
- 通知列表
- 未读数量
- 单条标记已读
- 全部标记已读

Out of Scope:
- 邮件 / 短信 / Push 推送
- 营销通知策略
- 后台通知运营系统
```

讲师点评：

“这里有两个点非常好。第一，它主动发现了范围冲突。第二，它把三个能力拆开了。个人中心、通知、站内信不要混成一个大 Spec，否则后面并行开发会互相打架。”

人工纠偏 Prompt：

```text
请把 user-messages 的范围收窄：
本章只做站内信 MVP，包括会话列表、消息详情、发送文本消息。
不做已读回执、不做图片附件、不做实时 WebSocket。
```

口播：

“这一步就是人类签字前的关键动作。AI 给草案，你做裁剪。记住：需求不是越多越好，MVP 是先把闭环跑通。”

### 【演示 2：交互 Agent 生成页面流程】（约 3min）

演示 Prompt：

```text
调用 interaction-designer Agent，基于上面的产品 brief，输出用户模块的页面流程。

要求：
1. 页面包含 /profile、/notifications、/messages、/messages/[id]。
2. 每个页面说明正常态、加载态、空态、错误态。
3. 给前端 Agent 输出组件拆分建议。
4. 不写代码，只写交互说明。
```

AI 输出关键片段：

```markdown
## /notifications
- 正常态：通知按时间倒序展示，未读通知有视觉标识。
- 加载态：显示 skeleton。
- 空态：显示 “No notifications yet.”
- 错误态：显示重试按钮。
- 操作：点击通知进入相关目标；点击 Mark as read 更新未读状态。
```

讲师点评：

“这里大家注意，交互 Agent 的价值不是画多漂亮，而是把状态补全。零基础同学做项目最容易漏的就是空态、加载态、错误态。真实产品里，这些不是边角料，它们决定用户会不会觉得你这个网站靠谱。”

### 【演示 3：OpenSpec 生成变更三件套】（约 3min）

屏幕操作：

```text
/opsx:propose user-profile
/opsx:propose user-notifications
/opsx:propose user-messages
```

口播：

“这里我们可以拆成三个 change。每个 change 都会生成 proposal、design、tasks。你作为人类负责人，要做第一次决策：签字。”

检查点：

```text
openspec/changes/user-profile/
  proposal.md
  design.md
  tasks.md

openspec/changes/user-notifications/
  proposal.md
  design.md
  tasks.md

openspec/changes/user-messages/
  proposal.md
  design.md
  tasks.md
```

口播：

“如果你发现 design 写大了，比如站内信要上 WebSocket，这时就要打回去。因为本章目标是端到端闭环，不是做一个企业 IM 系统。”

### 【演示 4：前后端并行实现】（约 3min）

口播：

“签字后，才进入实现。这里可以用 git worktree 做隔离。每个 capability 一条线。”

派发模板：

```text
Task: user-notifications backend implementation
Inputs:
  - openspec/changes/user-notifications/design.md
  - openspec/changes/user-notifications/tasks.md
  - backend/ 当前 API 结构
Output:
  - Notification entity/service/controller
  - 后端测试结果摘要
  - 与 design 偏离说明
```

```text
Task: user-notifications frontend implementation
Inputs:
  - openspec/changes/user-notifications/design.md
  - frontend/ 当前 App Router 结构
  - 交互 Agent 输出
Output:
  - /notifications 页面
  - API client 调用
  - 前端测试结果摘要
  - 与 design 偏离说明
```

口播：

“前端和后端并行不是各写各的，它们共同遵守 OpenAPI 契约。后端负责真实业务，前端是薄 BFF，不把业务逻辑塞进 Next.js Route Handler。这一点是 AGENTS.md 里的硬约束。”

### 【演示 5：测试 Agent 和体验 Agent 验收】（约 2min）

测试 Agent Prompt：

```text
调用 test-runner Agent，根据 user-notifications 的 Spec 编写并执行验收。

重点验证：
1. 已登录用户可以看到通知列表。
2. 没有通知时出现空态。
3. 点击 Mark as read 后未读数量减少。
4. 后端错误时前端出现错误态和重试入口。

输出：
- 测试文件路径
- 运行命令
- 通过 / 失败摘要
- 失败时定位到具体 Spec 条款
```

体验 Agent Prompt：

```text
调用 ux-reviewer Agent，对 /profile、/notifications、/messages 做体验走查。

重点检查：
1. 英文主界面是否符合 WanderChina 的境外用户定位。
2. 空态、错误态文案是否自然。
3. 按钮、链接、未读状态是否清楚。
4. 移动端布局是否可用。
5. 是否有明显 a11y 问题。
```

口播：

“最后你作为负责人做第二次和第三次决策：验收 Demo，批准合并。你不是不管细节，而是把细节交给角色和流程，把关键决策收回来。”

**以前你是开发者，现在你是一支 AI 研发团队的负责人：签字、验收、合并，是你的三次关键决策。**

下一节，我们收口方法论：Agent Teams 在 SDD + Harness 里有哪些铁律。

---

## 第8节（5min）：Agent Teams 在 SDD + Harness 流程中的最佳实践

### 【讲师稿】

这一节我们把经验压缩成几条铁律，大家可以直接抄到自己的项目里。

第一条：**Spec 仍然是唯一真相源。**

Agent 越多，越不能靠口头感觉。产品、交互、前端、后端、测试都要围绕 Spec 对齐。没有 Spec，多 Agent 不是团队，是多人同时自由发挥。

第二条：**角色按交付物切，不按幻想切。**

不要创建“超级全栈大神 Agent”。这种名字听起来厉害，实际最容易失控。你要问：这个 Agent 的输入是什么？输出是什么？交给谁？

第三条：**二阶段评审不能省。**

先检查有没有按 Spec 做，再检查代码质量。不要反过来。一个超出 Spec 的功能，代码写得再漂亮，也先不能进主线。

第四条：**先单兵，后团队。**

小任务直接做。复杂任务再拆 Agent Team。不要为了用多 Agent 而用多 Agent。

第五条：**人类只放权，不放责。**

AI 可以写 brief、写代码、写测试、跑验证，但范围裁剪、上线标准、合并决策，还是你负责。

**SDD + Harness 是公司章程，Agent Teams 只是扩编。流程不变，人手变多。**

下一节，我们做本章小结。

---

## 第9节（10min）：本章小结

### 【讲师稿】

这一章我们做了一件很重要的升级：

从“让一个 AI 帮我写代码”，升级到“让一支 AI 研发团队交付模块”。

我们回顾一下全章路径。

第一，我们讲了 Agent 的概念。

Agent 不是普通聊天机器人，而是能读上下文、用工具、按目标持续做事的数字员工。

第二，我们讲了 Agent Teams。

单 Agent 像一个全能实习生，Agent Teams 像一支迷你研发团队。它解决的是上下文压力、角色混乱、复杂任务串行这些问题。

第三，我们给项目增加了第一个数字成员 `spec-reviewer`。

它的任务很明确：检查代码有没有偏离 OpenSpec。它不负责泛泛点评，也不负责替你重写代码。

第四，我们区分了 Rule、Skill、Agent。

Rule 是员工手册，Skill 是培训教材，Agent 是具体上岗的人。

第五，我们设计了用户模块团队：

```text
产品 Agent → 交互 Agent → 前端 Agent / 后端 Agent → 测试 Agent → 体验 Agent
```

第六，我们讲了通信协议。

AI 团队不靠闲聊，靠交接单。每个任务必须写清楚 Task、Inputs、Output。

第七，我们用用户模块做了实战。

个人中心、消息通知、站内信，分别拆成 capability，先产品 brief，再交互设计，再 OpenSpec propose，再前后端并行实现，最后测试和体验验收。

这一章的产出物应该包括：

| 产出物 | 用途 |
|---|---|
| `spec-reviewer.md` | Spec 合规审查 |
| `product-manager.md` | 产品需求拆解 |
| 用户模块产品 brief | 进入 OpenSpec 的输入 |
| `user-profile` change | 个人中心能力 |
| `user-notifications` change | 消息通知能力 |
| `user-messages` change | 站内信能力 |
| 前后端实现 | 用户模块端到端能力 |
| E2E / 验收报告 | 证明功能可用 |

最后再强调三句话。

**第一，Agent Teams 不是为了热闹，是为了让复杂交付可控。**

**第二，团队越大，Spec 越重要。**

**第三，你不再是搬砖的人，你是定标准、签字、验收的人。**

如果用“学完这一章你应该能做到什么”来验收，我希望大家至少能做到五件事：

第一，能用自己的话讲清楚什么是 Agent、什么是 Agent Team、什么是 Skill。

第二，能判断一个任务到底该用单 Agent，还是该拆成 Agent Team。

第三，能为一个真实功能设计角色分工，比如产品、交互、前端、后端、测试分别输出什么。

第四，能写出一个基础 Agent 定义文件，至少包含职责、触发场景和输出格式。

第五，能把一句话需求推进到 OpenSpec，再让多个 Agent 围绕 Spec 做端到端交付。

这五件事连起来，就是从理论到项目实战的闭环。

你以后遇到一个新需求，比如“做订单中心”“做后台审核”“做 AI 行程助手”，不要一上来就问 AI：帮我写代码。

你先问自己：

```text
这个需求需要哪些角色？
每个角色交付什么？
他们之间靠什么交接？
哪份 Spec 是合同？
我在哪几个节点签字验收？
```

能回答这五个问题，你就不是在“使用 AI 写代码”，你是在“组织 AI 完成研发”。

下一章，我们会继续升级项目能力。团队已经有了，但它现在主要还在项目内部工作。下一章我们会讲 MCP 和 CLI：怎么让 AI 接入外部服务、调用外部工具，把 WanderChina 的能力继续扩出去。

**不是学会了才去用，是用了才叫学会。**

---

## 附录：讲师演示 Prompt 完整清单

### Prompt 1：创建 spec-reviewer

```text
请在 .qoder/agents/ 下创建一个 spec-reviewer.md。

目标：
这个 Agent 专门检查一次代码改动是否严格符合 OpenSpec 的 proposal/design/tasks。

要求：
1. frontmatter 包含 name、description、tools。
2. 系统提示词要说明它只做 Spec 合规检查，不做代码风格泛泛点评。
3. 输出必须包含：
   - 总体结论：PASS / FAIL
   - Spec 覆盖情况：✅已覆盖 / ❌未覆盖
   - 超范围实现：⚠️超出Spec
   - 必须修复项
4. 口径遵循本仓库 AGENTS.md 和 .qoder/rules/spec-driven-workflow.md。
```

### Prompt 2：纠偏 spec-reviewer

```text
请补充：如果发现代码实现了 design.md 没有定义的功能，即使功能看起来更好，也必须标记为 ⚠️超出Spec；不要用“用户可能需要”作为放行理由。
```

### Prompt 3：创建 product-manager

```text
请为 WanderChina 创建 .qoder/agents/product-manager.md。

定位：
你是产品 Agent，负责把一句话需求转成可进入 OpenSpec 的产品 brief。

职责：
1. 追问模糊需求，但不要无限追问，能合理假设就继续。
2. 输出目标用户、核心场景、功能边界、非目标、用户故事。
3. 必须把需求拆成可独立交付的 capability。
4. 输出要服务后续 /opsx:propose，不直接写代码。

输出格式：
- 背景
- 目标用户
- 用户故事
- In Scope
- Out of Scope
- Capability 拆分建议
- 验收口径草案

约束：
遵循 AGENTS.md、openspec/project.md 和 .qoder/rules/spec-driven-workflow.md。
```

### Prompt 4：纠偏 product-manager

```text
请补充一条约束：
如果新需求和 openspec/project.md 的既有 out of scope 冲突，必须在输出中增加“范围变更提醒”，建议先走 propose 更新项目范围，不允许直接默认通过。
```

### Prompt 5：用户模块产品 brief

```text
调用 product-manager Agent，为 WanderChina 设计用户模块产品 brief。

背景：
WanderChina 是面向境外用户的入境中国旅行目的地探索平台。
当前已经有首页、登录认证、内容/景点相关能力。

本次需求：
新增用户模块，包括：
1. 个人中心：展示用户资料、旅行偏好、收藏入口。
2. 消息通知：展示系统通知、未读数量、标记已读。
3. 站内信：用户之间可以查看会话和消息详情。

要求：
1. 先指出这是否涉及项目范围扩展。
2. 拆成可独立交付的 capability。
3. 每个 capability 给出 In Scope / Out of Scope。
4. 输出后续可喂给 /opsx:propose 的 brief。
5. 不写代码。
```

### Prompt 6：收窄站内信范围

```text
请把 user-messages 的范围收窄：
本章只做站内信 MVP，包括会话列表、消息详情、发送文本消息。
不做已读回执、不做图片附件、不做实时 WebSocket。
```

### Prompt 7：交互设计

```text
调用 interaction-designer Agent，基于上面的产品 brief，输出用户模块的页面流程。

要求：
1. 页面包含 /profile、/notifications、/messages、/messages/[id]。
2. 每个页面说明正常态、加载态、空态、错误态。
3. 给前端 Agent 输出组件拆分建议。
4. 不写代码，只写交互说明。
```

### Prompt 8：后端派发

```text
Task: user-notifications backend implementation
Inputs:
  - openspec/changes/user-notifications/design.md
  - openspec/changes/user-notifications/tasks.md
  - backend/ 当前 API 结构
Output:
  - Notification entity/service/controller
  - 后端测试结果摘要
  - 与 design 偏离说明
```

### Prompt 9：前端派发

```text
Task: user-notifications frontend implementation
Inputs:
  - openspec/changes/user-notifications/design.md
  - frontend/ 当前 App Router 结构
  - 交互 Agent 输出
Output:
  - /notifications 页面
  - API client 调用
  - 前端测试结果摘要
  - 与 design 偏离说明
```

### Prompt 10：测试验收

```text
调用 test-runner Agent，根据 user-notifications 的 Spec 编写并执行验收。

重点验证：
1. 已登录用户可以看到通知列表。
2. 没有通知时出现空态。
3. 点击 Mark as read 后未读数量减少。
4. 后端错误时前端出现错误态和重试入口。

输出：
- 测试文件路径
- 运行命令
- 通过 / 失败摘要
- 失败时定位到具体 Spec 条款
```

### Prompt 11：体验走查

```text
调用 ux-reviewer Agent，对 /profile、/notifications、/messages 做体验走查。

重点检查：
1. 英文主界面是否符合 WanderChina 的境外用户定位。
2. 空态、错误态文案是否自然。
3. 按钮、链接、未读状态是否清楚。
4. 移动端布局是否可用。
5. 是否有明显 a11y 问题。
```

---

> 约 11200 字 | 正常语速讲解约 90 分钟（实际含演示约 100 分钟，可通过压缩第1/2节案例控制回 95 分钟）
