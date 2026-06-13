---
booklet: "SDD + Harness：用 AI 写出能交付的代码"
part: 1
article: 3
title: "为什么 Vibe Coding 走不远：三宗罪"
prev: "02-ai-coding-landscape"
next: "04-sdd-spec-as-contract"
demo_project: "RedLite（简版图文社区）"
---

# 为什么 Vibe Coding 走不远：三宗罪

## 痛点开篇

上一篇的全景图里，我们给 Vibe Coding 下了一句话定义：**"凭感觉描述需求、凭感觉验收 AI 输出"的编程方式**。你可能对照自测问卷发现自己正处在这个阶段——那这篇就来拆解它为什么走不远。

你可能经历过这个循环：打开 Cursor，输入"帮我写一个登录页面"，AI 秒出一个看起来还不错的页面。你继续"加个表单校验"、"接一下后端接口"、"加个 Token 鉴权"——10 轮对话后，代码开始变得诡异：变量命名混乱、组件嵌套了 5 层、错误处理各写各的。第 15 轮，AI 把之前写好的逻辑覆盖了。

这不是你的问题。**这是 Vibe Coding 的结构性缺陷**。

Vibe Coding 的核心模式是"对话驱动开发"——你描述意图，AI 即时生成，你肉眼验收。它在原型验证阶段极其高效，但一旦进入真实项目（超过 3 个模块、需要多人协作、需要长期维护），就会暴露三宗罪：**不稳定、不规范、难协作**。

【图：对比图：Vibe Coding 效率曲线 vs 工程化 AI 开发效率曲线——Vibe Coding 前期快后期崩（指数级返工），SDD+Harness 前期慢后期稳（线性推进）】

## 技术溯源：Vibe Coding 从哪来，为什么火

Vibe Coding 这个词来自 Andrej Karpathy 2025 年 2 月的推文，他用这个词描述一种"完全依赖 AI 生成代码、开发者凭直觉验收"的编程方式。它之所以迅速流行，是因为**精准命中了开发者的一个核心欲望**：能不能不写代码，只描述需求，就让 AI 把活干了？

答案是：**在 Demo 层面，能。在工程层面，不能。**

在 Vibe Coding 之前，AI 辅助编程已经经历了两个阶段：

| 阶段 | 代表工具 | 人机关系 | 典型场景 |
|---|---|---|---|
| **L1 补全** | GitHub Copilot (2021) | AI 是打字员，人写主体 | 补全函数体、重复代码 |
| **L2 对话** | ChatGPT / Cursor (2023) | AI 是实习生，人给指令 | 写一个组件、改一个 Bug |
| **L3 Vibe** | Cursor Agent / Windsurf (2025) | AI 是施工队，人凭感觉验收 | 整个功能对话式生成 |

L3 看似是进化，但它跳过了一个关键问题：**谁来保证"感觉"是对的？**

## 原理深度解析：三宗罪的根因

### 罪一：不稳定——同一个 Prompt，三种结果

Vibe Coding 的底层机制是：你的自然语言描述 → LLM 的概率采样 → 代码输出。问题出在**概率采样**这一步：

1. **温度参数不为 0**：即使同一个 Prompt，每次生成的代码可能不同（变量名不同、实现路径不同）
2. **上下文窗口有限**：当对话超过 ~8K tokens，AI 开始"遗忘"前面的约定，导致前后不一致
3. **无显式约束**：你的 Prompt 里没有写"用 JWT 鉴权"、"错误码用 HTTP 标准"，AI 就会自由发挥

```typescript
// ❌ Vibe Coding 第 1 轮生成的登录接口
export async function login(req: Request) {
  const { username, password } = await req.json()
  // AI 自由发挥：用了 bcrypt，但没加 rate limit
  const user = await db.user.findFirst({ where: { username } })
  if (!user || !bcrypt.compare(password, user.password)) {
    return Response.json({ error: 'failed' }, { status: 401 })
  }
  // AI 自由发挥：Token 有效期 7 天，没写 refresh 逻辑
  const token = jwt.sign({ id: user.id }, 'secret123')
  return Response.json({ token })
}

// ❌ Vibe Coding 第 8 轮（加了鉴权中间件后），AI 把上面的逻辑重写了一遍
// 这次用了不同的错误码、不同的 Token 格式、甚至不同的 ORM 方法
```

**根因**：Vibe Coding 把"需求规格"隐含在对话里，而不是显式写成文档。LLM 没有持久记忆，每轮对话都是一次新的概率采样。

### 罪二：不规范——10 轮对话后，代码变成"缝合怪"

一个真实场景：你用 Vibe Coding 写 RedLite 的发帖功能，对话轨迹如下：

| 轮次 | 你的 Prompt | AI 的行为 |
|---|---|---|
| 1 | "写一个发帖表单" | 用了 shadcn/ui 的 Form 组件 |
| 3 | "加图片上传" | 引入了 react-dropzone，但没用 shadcn 的 Dialog |
| 5 | "接后端接口" | 用了 fetch，但前面的列表页用了 axios |
| 7 | "加 loading 状态" | 用了 useState，但旁边的组件用了 react-query |
| 10 | "统一错误处理" | 写了一个 ErrorBoundary，但只覆盖了当前页 |

10 轮之后，你的项目里同时存在 fetch 和 axios、shadcn Form 和自定义 Form、useState 和 react-query。**不是 AI 不知道有统一方案，而是你没在每一轮都告诉它。**

> **反直觉**：Vibe Coding 的问题不是 AI 写不出好代码，而是**每轮对话都是一次独立决策，缺少跨轮次的全局约束**。对话越多，代码越碎片化。

### 罪三：难协作——"我的 Vibe 你不懂"

Vibe Coding 的全部上下文存在于**你和 AI 的对话历史**里。这意味着：

- **新成员无法理解**：你的同事接手你的代码，看到的是一堆"看起来能做但不知道为什么这样写"的代码，没有 Spec 文档、没有设计决策记录
- **Code Review 无法进行**：Reviewer 不知道哪些是 Spec 要求、哪些是 AI 自由发挥，只能逐行人肉审查
- **Bug 修复变成猜谜**：出了 Bug，你不知道是 Prompt 写得不对、还是 AI 理解有误、还是上下文被截断了

**根因**：Vibe Coding 的知识全在人脑和对话历史里，**没有持久化、没有版本化、没有共享化**。

【图：架构图：Vibe Coding 的知识孤岛——人脑意图→对话历史→AI输出→代码，四个环节各自独立，无共享文档层；对比 SDD 模式——Spec 文档居中，连接人脑意图、AI 生成、代码实现、测试用例】

## 实战落地：用 RedLite 验证三宗罪

让我们用 RedLite 的"发帖功能"做一个对照实验：

**实验组（Vibe Coding）**：直接用 Cursor Agent 对话生成
**对照组（SDD+Harness）**：先写 Spec，再用 Harness 约束 AI 生成

```markdown
# Vibe Coding 的典型 Prompt（模糊、无约束）
"帮我写一个发帖功能，要能输入标题和正文，上传图片，提交到后端"

# SDD 的 Spec（精确、有边界、可验收）
## 模块边界
- 包含：标题输入（必填，≤100字）、正文输入（必填，≤5000字）、封面图上传（可选，≤5MB，jpg/png）
- 不包含：图片裁剪、草稿保存、@提及

## 核心场景
- WHEN 用户填写标题和正文并点击发布
  THEN 调用 POST /api/posts 创建帖子
  AND 成功后跳转到帖子详情页
- WHEN 用户未填标题就点击发布
  THEN 标题输入框下方显示红色提示"标题不能为空"
  AND 不发送请求

## 数据结构
- CreatePostInput { title: string(1-100), content: string(1-5000), coverUrl?: string }

## ACCEPTANCE
- [ ] 空标题提交时显示校验错误
- [ ] 成功发布后跳转到详情页
- [ ] 图片上传限制 5MB 且仅 jpg/png
- [ ] Lighthouse Performance > 90
```

**差异一目了然**：Vibe Coding 的 Prompt 是"我想要什么"，SDD 的 Spec 是"精确到什么程度算做完了"。

## 可抄走的硬货：Vibe Coding 问题诊断清单

当你的项目出现以下症状时，说明 Vibe Coding 已经到极限了：

```markdown
## Vibe Coding 极限诊断清单

### 触发条件（满足 2 条即应转向 SDD）
- [ ] 同一个模块对话超过 10 轮，AI 开始覆盖之前的逻辑
- [ ] 项目里同时存在 2+ 种 HTTP 客户端（fetch/axios/ky）
- [ ] 新成员接手时，问"这段代码为什么这样写"你答不上来
- [ ] 同一个 Bug 修了 2 次，每次 AI 用不同方式"修好"但引入新问题
- [ ] Code Review 时 Reviewer 说"我不知道你这段的设计意图"
- [ ] 你已经不记得哪些功能是 AI 自由发挥的

### 快速止血（来不及写完整 Spec 时）
1. 把当前对话里已经确定的技术决策，写成一个 DECISIONS.md
2. 在项目根目录放一个 CONSTRAINTS.md，写明技术栈、命名规范、错误码规范
3. 每次新对话开始前，把 DECISIONS.md 和 CONSTRAINTS.md 贴进 Context
```

## 速查清单：Vibe Coding vs SDD+Harness 对照表

| 维度 | Vibe Coding | SDD + Harness |
|---|---|---|
| **需求表达** | 自然语言对话（每轮重新描述） | Spec 文档（一次写好，持续复用） |
| **一致性保证** | 靠人脑记住之前的约定 | Spec + Harness 约束文件自动加载 |
| **代码风格** | 每轮可能不同（AI 自由发挥） | AGENTS.md + frontend.md 全局统一 |
| **Bug 修复** | 重新对话，可能引入新问题 | 对照 Spec 定位，只改该改的 |
| **协作交接** | 对话历史（别人看不懂） | Spec 文档（新人 5 分钟理解设计意图） |
| **适合阶段** | 原型验证、个人 Demo | 多人协作、需要维护的真实项目 |
| **效率曲线** | 前快后崩（指数级返工） | 前慢后稳（线性推进） |

> **避坑提示**：不是说 Vibe Coding 没用——原型阶段用它验证想法极快。问题是**从原型到生产的切换点**，很多团队没有意识到该在什么时候从 Vibe 切到 SDD。

## 独到小结 + 动手点

**Vibe Coding 的本质问题是：把工程规格隐含在人机对话里，而不是显式写成文档。** 对话是临时的、概率性的、不可共享的；而工程规格必须是持久的、确定性的、可共享的。这不是 AI 的问题，是工程方法的问题。

**动手点**：打开你当前用 Vibe Coding 做的项目，对照上面的"极限诊断清单"打分。如果满足 2 条以上，下一篇我们讲怎么用 SDD 把"感觉"变成"合同"。

---

> 下一篇：**SDD：用 Spec 把"感觉"变成"合同"**——我们将从"为什么需要 Spec"讲到"一份好 Spec 的四要素"，并给出 RedLite 登录模块的完整 Spec 示例。
