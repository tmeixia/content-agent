---
stage: 章节框架（待用户确认后写详细讲师稿+PPT）
chapter: 第11章 基于Agent Teams全栈实践用户模块开发：个人中心、消息通知、站内信
lessons: 9
planned_min: 95
audience: 零基础应届生 / 单技术栈转全栈（概念必须普及到位）
case: WanderChina 用户模块（个人中心、消息通知、站内信）
repo: https://github.com/sqlyouran/first（父仓 AIWorkSpace + backend/frontend submodule）
created: 2026-06-12
---

# 第11章讲师框架（v1 待确认）

## 章定位（章头四件套草案）
- **全章时长**：95min（15+15+5+10+10+10+15+5+10）
- **案例**：WanderChina 用户模块——个人中心、消息通知、站内信（三个子需求，天然适合并行）
- **技术栈**：Qoder Agent Teams（`.qoder/agents/`）+ Superpowers Skills + opsx 指令 + Spring Boot 3.3.5 + Next.js 16/React 19 + Playwright E2E
- **一句话定位**：从"一个 AI 单兵"升级为"一支 AI 公司"——产品/交互/体验/前端/后端/测试六角色协同，端到端完整交付一个模块

## 章主线（贯穿叙事）
公司类比：前10章你带的是**一个全能实习生**（单 Agent）；本章你创办**一家麻雀公司**（Agent Teams）——
招人（L2）→ 定岗（L3）→ 建沟通制度（L4）→ 发装备（L5）→ 试用对比（L6）→ 正式接单（L7）→ 沉淀公司章程（L8）。
你的角色从"盯着一个人干活的师傅"升级为"只做三次决策的 CEO"：签字 Spec、验收 Demo、批准合并。
SDD 内核不变：**Spec 仍是唯一真相源，且团队越大越重要——它是所有角色之间的合同。**

## 项目仓库锚点（实战全部基于真实仓库现状）
| 仓库现状 | 第11章怎么用 |
|---|---|
| `.qoder/agents/` 为空，README 留了模板与命名建议 | L2 现场添加第一个数字成员，L5 补齐六角色 |
| `.qoder/skills/` 已有 superpowers 8 件套 + opsx 4 命令 | L5 给角色"发装备"，不重造轮子 |
| `subagent-driven-development` skill（派发模板+二阶段评审） | L4 通信协议与质检流程的现成依据 |
| `using-git-worktrees` skill | L7 三个子需求并行开发的隔离机制 |
| 后端已有 UserEntity/JWT（ch7）、Post/Comment/Vote/Bookmark（ch8） | 用户模块在其上新增 profile/notification/message |
| 前端已有 (auth)/posts/regions 路由组 | 新增 app/profile、app/notifications、app/messages |

---

## 逐节框架

### L1（15min）如何对项目进行AI自迭代？多Agent协作如何解决研发效率瓶颈？【概念普及节】
- **开场钩子**：回顾前几章实战的三个憋屈瞬间——①会话干久了上下文爆满开始"失忆"；②前端干完切后端像"人格分裂"；③你全程盯着，走不开身。（互动停顿："这三个坑你踩过几个？"）
- **踩坑故事**：一个会话连干 4 小时，上下文满了之后把 ch7 写好的 JWT 刷新逻辑改坏——**"AI 不是不聪明，是脑容量被你塞满了。"**
- **零基础普及块**：Agent 到底是什么（LLM+工具+记忆+自主循环，类比"会用电脑的实习生"）；上下文窗口=脑容量；单 Agent 三大瓶颈：脑容量有限、一双手串行、既当爹又当妈角色混杂
- **引出 Agent Teams**：解法不是换一个更强的实习生，而是开一家公司——多个专才 > 一个通才
- **本章地图**（PPT 思维导图）：招人→定岗→沟通→装备→试用→接单→章程
- **金句**：**"单 Agent 是一个全能实习生，Agent Teams 是一家麻雀公司。"**
- **下一节钩子**："公司第一步是招人。AI 公司怎么招人？写一个 md 文件就行。"
- PPT 估 9 页（含章封面）

### L2（15min）给项目增加一个AI数字成员：Skill 在 Agent 能力扩展中的核心作用【实战节】
- **普及块**：数字成员=`.qoder/agents/<name>.md`（frontmatter：name/description/tools + 系统提示词）；三件套区分——**Agent 定义=岗位 JD，Skill=培训教材，Rule=员工手册**（招聘类比）
- **演示（完整 Prompt）**：现场创建第一个数字成员 `spec-reviewer.md`（仓库 README 留的命名建议正好用上）→ AI 输出 + 讲师逐条点评（系统提示词哪句写得好/哪句要改）→ 人工纠偏一轮
- **高质量 Skill 编写要点**：单一职责、明确触发时机（when-to-use）、输出格式约定
- **必装 Skills 巡礼**：项目已有 superpowers 8 件套一句话点名（brainstorming/writing-plans/executing-plans/TDD/subagent-driven-development/worktrees/code-review/verification）
- **金句**：**"Skill 是教材，Agent 是按教材上岗的人。"**
- **钩子**："一个人招进来了。但公司不是人堆出来的，是岗位结构搭出来的——下一节：定岗。"
- PPT 估 9 页

### L3（5min）将若干数字人编成团队：基于项目需求设计角色分工【设计思想节·短】
- **核心观点（零基础最缺的认知）**：软件交付≠写代码。一个需求从进到出要经过：产品定边界→交互定流程→体验定质感→前端/后端实现→测试验收。**所以团队要有非技术角色。**
- **六角色流水线**（PPT 横向流程图，每角色标输入/输出）：
  | 角色 | 输入 | 输出 |
  |---|---|---|
  | 产品 Agent | 一句话需求 | PRD/用户故事（含边界：做什么/不做什么） |
  | 交互 Agent | PRD | 页面流程图、状态流转（空态/加载/错误） |
  | 体验 Agent | 交互稿+实现页面 | 走查清单（文案/一致性/a11y） |
  | 前端 Agent | Spec+交互稿 | Next.js 组件+单测 |
  | 后端 Agent | Spec | Spring Boot API+单测 |
  | 测试 Agent | Spec | Playwright E2E+验收报告 |
- **设计原则**：角色按"交付物"切，不按技术名词切；**Spec 是角色之间的合同**
- **金句**：**"好团队不是人多，是每个人的输出刚好是下一个人的输入。"**
- **钩子**："岗位有了，可他们互相不认识。AI 和 AI 怎么说话？"
- PPT 估 4 页

### L4（10min）数字人团队如何交流：通信协议与任务调度机制
- **普及块**：AI 之间不"聊天"，靠**交接单**——结构化的派发模板（直接展示仓库 `subagent-driven-development` 的 Task/Inputs/Output 模板，这就是通信协议）
- **调度机制**：谁先谁后（产品→交互必须串行；前端/后端拿到 Spec 后可并行）；主 Agent（你+主会话）当工头排班
- **并行的隔离**：git worktree（仓库已有 skill）——类比"一个工地拆三个施工区，互不踩脚"
- **质检流程**：二阶段评审（spec 合规→代码质量；任一不过退回重做，**"主上下文不接收脏代码"**）
- **金句**：**"AI 团队的沟通不靠开会，靠交接单。"**
- **钩子**："制度建好了，但员工还没培训。下一节给六个角色发装备。"
- PPT 估 7 页（含派发模板页+调度泳道图）

### L5（10min）如何通过 Skills 构建 Agent Teams 的角色能力【实战节】
- **实操**：逐角色"发装备"——角色 × Skills/Rules 配置矩阵（PPT 表格，可抄走）：
  产品←brainstorming+writing-plans；交互/体验←frontend/styling-conventions rules；前端←TDD+frontend-conventions；后端←TDD+backend/api/database-conventions；测试←TDD+verification-before-completion；全员←spec-driven-workflow
- **演示（完整 Prompt）**：现场写一个完整角色文件 `product-manager.md`（frontmatter+系统提示词+输出格式），AI 输出+点评+纠偏
- **深讲 vs 快讲**：深讲产品/前端/测试三个角色文件，其余三个展示成品快速过（时长考虑）
- **金句**：**"角色定岗位，Skill 定本事。"**
- **钩子**："公司开张前，先试用——单兵和团队拉出来遛遛，谁强？"
- PPT 估 7 页

### L6（10min）单 Agent vs Agent Teams：效率和质量差异对比【对比实验节】
- **实验设计**：拿"消息通知"一个子需求做 AB——A 组单会话一条龙 vs B 组团队流水线
- **对比维度**（PPT 对比表）：总耗时、返工次数、Spec 覆盖率、你的介入次数、上下文崩溃风险
- **诚实讲 trade-off**（仓库 skill 里现成的"何时不用"清单）：任务<2分钟直接做；强依赖上一步运行时输出别派；全局重构 subagent 看不全；token 成本翻倍
- **决策清单**：什么时候单兵、什么时候团队（可抄走 checklist）
- **金句**：**"多 Agent 不是为了快，是为了稳——快只是并行的副产品。"**
- **钩子**："试用合格，正式接单：用户模块，三个子需求，一次性端到端交付。"
- PPT 估 6 页

### L7（15min）Agent Teams 驱动用户模块开发【本章大实战】
- **需求**："给 WanderChina 加用户模块：个人中心、消息通知、站内信"
- **端到端全流程演示**（实战骨架严格走：抛问题→演示 Prompt→AI输出点评→人工纠偏→金句）：
  1. 产品 Agent：一句话需求 → PRD+用户故事+边界（演示完整 Prompt）
  2. 交互 Agent：PRD → 三个页面的流程与状态设计
  3. `/opsx:propose`：生成三份 Spec（个人中心/消息通知/站内信）——**你做第 1 次决策：签字**
  4. 并行开发：三条 worktree 流水线，每条线内前端 Agent+后端 Agent 按 Spec 实现（TDD 强制）
  5. 测试 Agent：Playwright E2E + 验收报告；体验 Agent：走查清单
  6. 二阶段评审 → **你做第 2、3 次决策：验收 Demo、批准合并**
- **架构落点**（PPT 架构图）：后端新增 ProfileController/NotificationEntity/MessageEntity…；前端新增 app/profile、app/notifications、app/messages；OpenAPI 契约同步
- **演示节奏控制**：产品 Agent 和并行开发深演示，其余环节快进展示结果（15min 装不下全程实录，录制时配剪辑）
- **金句**：呼应课程招牌——**"以前你是甲方，现在你是一家 AI 公司的 CEO——CEO 只做三次决策：签字、验收、合并。"**
- PPT 估 11 页

### L8（5min）Agent Teams 在 SDD+Harness 流程中的最佳实践【方法论收口·短】
- 四条铁律（可抄走 checklist）：①Spec 仍是唯一真相源，团队越大越要；②Harness 护栏对所有 Agent 一视同仁；③二阶段评审不可省；④先单兵后团队，复杂度配得上才扩编
- **金句**：**"SDD+Harness 是公司章程，Agent Teams 只是扩编——流程不变，人手变多。"**
- PPT 估 4 页

### L9（10min）本章小结
- 按课程惯例：本章地图回顾（思维导图）→ 效率对比数字（L6 实验数据回收）→ 产出物表（6 个角色文件+3 份 Spec+用户模块前后端代码+E2E+验收报告）→ 下一章预告（ch12 MCP/CLI："团队建好了，但他们只会用公司内网——下一章给他们接上外部世界"）→ 收尾金句
- PPT 估 5 页

---

## 全章 PPT 体量估算
约 62 页（9+9+4+7+7+6+11+4+5），按官方标准 16:9、标题 #C9394A、正文 20 号、关键词短句、流程图/对比表为主。

## 待用户确认的问题
1. 九节框架、叙事主线（麻雀公司）、各节目标与演示设计是否 OK？
2. 六角色全保留；演示深讲产品/前端/测试三个，交互/体验/后端展示成品快讲——可以吗？
3. 技术栈口径：仓库实际是 **Next.js 16 + React 19**，与 `_course-conventions.md`（第6章时期）记的 Next.js 14 + React 18 不一致——第11章材料按仓库实际写？
4. 演示工具以 **Qoder**（`.qoder/agents/`）为主，是否需要带一句 Claude Code 同理？
