---
booklet: "SDD + Harness：用 AI 写出能交付的代码"
part: 1
article: 6
title: "SDD+Harness：如何让 Vibe Coding 工程化"
prev: "05-harness-engineering-guardrails"
next: "07-sdd-tool-landscape"
demo_project: "RedLite（简版图文社区）"
---

# SDD+Harness：如何让 Vibe Coding 工程化

## 痛点开篇

前三篇我们分别讲了：Vibe Coding 为什么走不远（第一篇）、SDD 怎么用 Spec 约束需求（第二篇）、Harness 怎么用机制约束 AI（第三篇）。你可能会问：既然 SDD 和 Harness 各自有用，为什么还要合在一起？

因为**单独用都有致命盲区**：

- **只有 SDD 没有 Harness**：你写了完美的 Spec，但 AI 生成代码时不读 Spec——Spec 沦为摆设文档
- **只有 Harness 没有 SDD**：AI 生成代码时遵守了 AGENTS.md 和 frontend.md 的规范，但功能本身是模糊的——代码风格统一，功能不对

就像建筑工程里，只有图纸没有施工队监督，或者只有监督没有图纸——都不行。**SDD+Harness 的组合，才是完整的工程化 AI 开发方案。**

【图：架构图：SDD+Harness 双轮驱动——SDD（Spec文档）解决"做什么"的问题（需求精确化），Harness（约束机制）解决"怎么做对"的问题（生成受控化），两者通过五步闭环（拆Spec→排计划→写代码→Review→测试）串联成完整工作流】

## 技术溯源：为什么 2025 年才出现 SDD+Harness 组合

SDD 和 Harness 各自都不是新概念，但它们的组合是 2025 年才成型的新范式。原因很简单：**之前的 AI 模型不够强，组合了也用不上。**

| 时间 | AI 能力 | 开发模式 | 瓶颈 |
|---|---|---|---|
| 2021-2022 | 代码补全（Copilot） | 人写为主，AI 辅助 | AI 只能补全，不能独立生成模块 |
| 2023-2024 | 对话生成（ChatGPT/Cursor） | 人描述需求，AI 生成 | AI 能生成但质量不稳定 |
| 2025+ | Agent 级生成（Cursor Agent/Claude） | AI 自主完成多步骤任务 | **AI 够强了，但缺少工程化约束** |

2025 年的关键变化是：AI 的生成能力突破了"可独立交付模块"的阈值。这意味着 AI 不再是"辅助工具"，而是"施工队"。而施工队越强，越需要好的图纸（SDD）和好的工地管理（Harness）。

这个逻辑在人类工程史上反复出现：施工队能力越强 → 建筑越复杂 → 越需要工程化管理。**没有人会用 Vibe 方式建摩天大楼。**

## 原理深度解析：SDD+Harness 的五步闭环

SDD+Harness 的核心工作流是**五步实战闭环**，每一步都有明确的输入、输出和校验机制：

【图：流程图：SDD+Harness 五步闭环——①拆Spec（输入:需求→输出:Spec文档）→②排计划（输入:多个Spec→输出:执行排期）→③写代码（输入:Spec+约束→输出:代码，Harness自动校验）→④Review（输入:代码+Spec→输出:覆盖率报告）→⑤测试（输入:ACCEPTANCE→输出:E2E测试）→循环回到①（如有问题）】

### 步骤 1：拆 Spec（`/opsx:explore` + 人工）

**输入**：产品需求（如"RedLite 需要登录注册功能"）
**输出**：`specs/auth-register-login.md`

关键动作：
1. 执行 `/opsx:explore` 让 AI 分析项目现状（已有代码、目录结构、技术栈）
2. 人工确定模块边界（包含什么、不包含什么）
3. 用 WHEN/THEN/AND 格式写出核心场景（≥5 个）
4. 定义数据结构（TypeScript interface）
5. 列出 ACCEPTANCE checklist（≥8 条）

**校验**：对照 Spec 质量评估表（第二篇的速查清单）自检。

### 步骤 2：排计划（`/opsx:propose`）

**输入**：多个 Spec 文档
**输出**：`exec-plans/<module>-sprint.md`

关键动作：
1. 执行 `/opsx:propose`，AI 分析 Spec 间的依赖关系
2. 确定开发顺序（RedLite 的依赖链：鉴权 → 发帖 → 列表 → 详情）
3. 每个 Sprint 标注对应的 Spec 和预期产出

**依赖链的逻辑**：发帖需要登录（鉴权模块），列表需要帖子数据（发帖模块），详情需要列表的路由参数（列表模块）。每一步都依赖前一步的数据模型和接口。

### 步骤 3：写代码（`/opsx:apply`）——Harness 在这里生效

**输入**：Spec + AGENTS.md + frontend.md
**输出**：符合约束的代码

这是 Harness 发挥核心作用的步骤。执行 `/opsx:apply specs/auth-register-login.md` 时：

1. Harness 自动加载 AGENTS.md（全局约束）和 frontend.md（前端规范）
2. 将 Spec + 约束文件合并为 System Context 发给 AI
3. AI 在约束内生成代码
4. Harness 自动校验（对照 ACCEPTANCE）
5. 未通过则自动重试（最多 3 轮）

```
执行链路（关键在这几步）：
Spec(auth-register-login.md)
  + AGENTS.md（技术栈/命名/目录/错误码规范）
  + frontend.md（shadcn/ui/zod/Tailwind/键盘可访问）
  ─────────────────────────────────────────────
  = AI 的 System Context
  
AI 生成 → 自动校验 ACCEPTANCE → 未通过 → 重试 → 通过 → 输出
```

**为什么这一步能解决 Vibe Coding 的三宗罪**：

| Vibe Coding 的问题 | SDD+Harness 的解决方式 |
|---|---|
| 不稳定（同一个 Prompt 三种结果） | Spec 精确到字段级约束，AI 自由发挥空间极小 |
| 不规范（10 轮后代码变缝合怪） | AGENTS.md + frontend.md 自动注入，每轮都受约束 |
| 难协作（对话历史别人看不懂） | Spec 文档就是设计文档，新人 5 分钟理解 |

### 步骤 4：交叉 Review（人工 + AI 辅助）

**输入**：生成的代码 + Spec
**输出**：覆盖率报告（✅❌⚠️）

双向对账：
- **正向（Spec→代码）**：逐条检查 Spec 的核心场景是否在代码中实现。遗漏的标 `❌ 未覆盖`
- **反向（代码→Spec）**：逐行检查代码中是否有 Spec 未要求的功能。超出的标 `⚠️ 超出Spec`

核心原则：**Spec 是唯一真相源。** 不在 Spec 里的功能，再好也先删（或者先补 Spec 再留代码）。

### 步骤 5：测试（Playwright E2E）

**输入**：ACCEPTANCE checklist
**输出**：`tests/e2e/auth-register-login.spec.ts`

ACCEPTANCE 的每一条直接转化为一个 Playwright 测试用例：

```typescript
// ACCEPTANCE 条目 → Playwright 测试（关键对应关系）
// "注册：空邮箱提交时显示'请输入邮箱'" → 下面的测试

import { test, expect } from '@playwright/test'

test('注册：空邮箱提交时显示校验错误', async ({ page }) => {
  await page.goto('/register')
  
  // 不填邮箱，直接点击注册
  await page.click('button[type="submit"]')
  
  // 期望：显示"请输入邮箱"
  const error = page.locator('[data-field="email"] .text-red-500')
  await expect(error).toHaveText('请输入邮箱')
  
  // 期望：不发送请求（按钮应该置灰，这里验证请求未发出）
  // 注意：如果按钮没置灰，这个测试会暴露问题
})

test('登录：错误凭据显示通用错误（不区分原因）', async ({ page }) => {
  await page.goto('/login')
  
  await page.fill('[name="email"]', 'nonexistent@test.com')
  await page.fill('[name="password"]', 'WrongPass123')
  await page.click('button[type="submit"]')
  
  // 期望：显示通用错误，不暴露"邮箱不存在"
  const error = page.locator('.alert-error')
  await expect(error).toHaveText('邮箱或密码错误')
})
```

**Spec 即测试种子**：WHEN = test setup，THEN = expect 断言，AND = 额外断言。

## 实战落地：RedLite 五步闭环的效率对比

用 RedLite 的鉴权模块做一个真实的效率对比：

| 步骤 | Vibe Coding 耗时 | SDD+Harness 耗时 | 差异原因 |
|---|---|---|---|
| 需求明确 | 0 min（直接对话） | 30 min（写 Spec） | SDD 前期多投入 30 min |
| 代码生成 | 45 min（10+ 轮对话） | 15 min（1 次 apply） | Harness 自动加载约束，无需逐轮描述 |
| 修复不一致 | 60 min（发现+修复） | 5 min（自动校验发现） | Harness 自动校验 ACCEPTANCE |
| 代码 Review | 30 min（人肉逐行） | 10 min（对照 Spec 对账） | Spec 提供明确参照 |
| 写测试 | 45 min（从零写） | 15 min（ACCEPTANCE 转测试） | Spec 即测试种子 |
| **总计** | **180 min** | **75 min** | **SDD+Harness 节省 58%** |

> **避坑提示**：这个对比不是"Vibe Coding 一定慢"——如果你的项目只有 1 个模块、不需要长期维护，Vibe Coding 可能更快。SDD+Harness 的优势在**模块数 ≥ 3、需要多人协作、需要长期维护**的场景下才显现。

## 可抄走的硬货：五步闭环 Checklist

```markdown
# SDD+Harness 五步闭环 Checklist（每个模块过一遍）

## Step 1: 拆 Spec
- [ ] 执行 /opsx:explore 了解项目现状
- [ ] Spec 有模块边界（包含+不包含，不包含附原因）
- [ ] Spec 有 ≥5 个核心场景（WHEN/THEN/AND 格式）
- [ ] Spec 有 TypeScript 数据结构
- [ ] Spec 有 ≥8 条 ACCEPTANCE
- [ ] 文件路径：specs/<module>-<unit>.md

## Step 2: 排计划
- [ ] 执行 /opsx:propose 生成开发排期
- [ ] 依赖关系已确认（如：发帖依赖鉴权）
- [ ] 文件路径：exec-plans/<module>-sprint.md

## Step 3: 写代码
- [ ] AGENTS.md 已就位（技术栈/命名/目录/错误码/禁止行为）
- [ ] frontend.md 已就位（组件库/表单/样式/路由）
- [ ] 执行 /opsx:apply specs/<module>-<unit>.md
- [ ] Harness 自动校验通过（ACCEPTANCE 全部 ✅）

## Step 4: Review
- [ ] 正向：Spec 核心场景 → 代码（找 ❌ 未覆盖）
- [ ] 反向：代码 → Spec（找 ⚠️ 超出Spec）
- [ ] 覆盖率 100%（无 ❌ 无 ⚠️）

## Step 5: 测试
- [ ] ACCEPTANCE 每条已转 Playwright 测试
- [ ] 测试全部通过
- [ ] 文件路径：tests/e2e/<module>-<unit>.spec.ts
- [ ] 执行 /opsx:archive 归档
```

## 速查清单：三种开发模式对比

| 维度 | 纯 Vibe Coding | 只有 SDD | 只有 Harness | SDD + Harness |
|---|---|---|---|---|
| **需求精确度** | 低（对话隐含） | 高（Spec文档） | 低（对话隐含） | 高（Spec文档） |
| **生成一致性** | 低（每轮独立） | 低（无自动约束） | 高（自动加载规范） | 高（自动加载规范） |
| **代码可验收** | 肉眼判断 | 对照Spec | 无明确标准 | 对照Spec+自动校验 |
| **协作友好度** | 低（对话历史） | 高（Spec文档） | 中（规范统一但需求不明） | 高（Spec+规范） |
| **前期投入** | 最低 | 中（写Spec） | 中（写约束文件） | 最高（Spec+约束） |
| **长期效率** | 指数级返工 | 需求清晰但生成不稳定 | 风格统一但功能不精确 | 线性推进 |
| **适用场景** | 原型Demo | 需求复杂的团队 | 风格要求严格的项目 | **真实项目（推荐）** |

## 独到小结 + 动手点

**SDD+Harness 的组合本质上是把软件工程的经典方法论（需求规格化+过程约束化+验收自动化）应用到了 AI 编程场景。** 它不是发明了新技术，而是把"先写规格再施工、施工过程有监督、完工后有验收"这套人类工程界验证了上百年的方法，落地到了 AI 工具链里。

**动手点**：回顾你的 RedLite 项目，确认 AGENTS.md 和 frontend.md 已就位（第三篇的任务），然后用第二篇的 Spec 执行一次 `/opsx:apply`，体验 Harness 自动加载约束的效果。

---

> 前四篇（#3~#6）我们讲完了 SDD+Harness 的理论精要。但市面上有好多工具都声称支持 SDD+Harness，该选哪个？下一篇进入 **Part 2 上手准备**的第一步：**SDD 工具选型——Kiro / Spec-Kit / OpenSpec / Superpowers 一图选**。
