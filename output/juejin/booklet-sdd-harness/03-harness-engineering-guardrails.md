---
booklet: "SDD + Harness：用 AI 写出能交付的代码"
part: 1
article: 5
title: "Harness：给 AI 装工程护栏"
prev: "04-sdd-spec-as-contract"
next: "06-sdd-harness-combined"
demo_project: "RedLite（简版图文社区）"
---

# Harness：给 AI 装工程护栏

## 痛点开篇

上一篇我们写了 RedLite 登录模块的完整 Spec，精确到每个字段的约束和错误码。在第 2 篇全景图里，我说 Harness 是“给 AI 加载规范的工程护栏”——现在就来拆这个护栏是怎么工作的。

你以为把这份 Spec 丢给 Cursor Agent，它就能生成完美代码？

试一下你就知道：**AI 读了你的 Spec，但照样自由发挥。**

Spec 里写了"用 shadcn/ui 的 Form 组件"，AI 用了原生 `<form>` + 手写样式。Spec 里写了"错误码用大写蛇形"，AI 用了 HTTP 状态码 + 中文消息。Spec 里写了"组件放在 `components/auth/` 目录"，AI 全塞进了 `app/auth/` 里。

问题不是 AI 没读 Spec——而是 **Spec 只是一份合同，没有人监督施工队按合同干活**。Harness 就是那个"工头"。

【图：流程图：无 Harness 时的 AI 生成链路——Spec→AI自由发挥→代码（偏离Spec）；有 Harness 时的链路——Spec→Harness加载约束文件→AI在约束内生成→自动校验→不通过则重试（最多3轮）→代码（对齐Spec）】

## 技术溯源：Harness 这个词从哪来

Harness 在英文里的原意是"马具"——套在马身上的缰绳和鞍具，让骑手能控制马的方向和速度。在 AI 编程语境下，Harness 的含义完全一致：**AI 是那匹马（强大但不受控），Harness 是缰绳（约束 AI 的行为边界）。**

这个概念在 2024-2025 年的 AI 编程工具中逐步成型。以 superPower（现改名为 Superpowers）为代表的工具，第一次把"约束文件自动注入"做成了产品特性：

| 阶段 | 代表 | 约束方式 | 问题 |
|---|---|---|---|
| **手动注入** | 早期 ChatGPT | 每次对话手动粘贴规范 | 容易遗漏、每轮重复 |
| **System Prompt** | Cursor Rules | 项目级 `.cursorrules` | 粒度粗、不能按模块差异化 |
| **Harness 自动注入** | superPower / openSpec | `AGENTS.md` + `frontend.md` 按模块自动加载 | 当前最优方案 |

关键进化：**从"手动告诉 AI 遵守规范"到"AI 每次执行前自动加载规范"。** 就像从"每次开车前手动检查安全带"到"不系安全带车就不启动"。

## 原理深度解析：Harness 的三层机制

### L1 用法层：Harness 在项目里长什么样

一个典型的 Harness 配置目录结构：

```
RedLite/
├── .harness/              # Harness 配置根目录
│   └── config.yaml        # 全局配置（加载策略、重试次数）
├── AGENTS.md              # 全局约束（所有模块共享）
├── frontend.md            # 前端规范（组件/UI/样式）
├── specs/                 # Spec 文档目录
│   ├── auth-register-login.md
│   └── post-create.md
├── exec-plans/            # 开发计划
│   └── auth-sprint.md
└── lessons-learned/       # 归档经验
    └── auth-module.md
```

### L2 机制层：Harness 执行时发生了什么

当你执行 `/opsx:apply specs/auth-register-login.md` 时，Harness 的执行链路如下：

1. **解析 Spec**：读取 `specs/auth-register-login.md`，提取模块边界、核心场景、数据结构、ACCEPTANCE
2. **加载约束文件**：自动注入 `AGENTS.md`（全局约束）+ `frontend.md`（前端规范），作为 AI 生成的 System Context
3. **生成代码**：将 Spec + 约束文件一起发给 AI，生成代码
4. **自动校验**：对照 ACCEPTANCE checklist 检查生成的代码（静态分析 + 模式匹配）
5. **重试修复**：如果有 ACCEPTANCE 项未通过，自动将"未通过项 + 当前代码"再次发给 AI 修复，最多 3 轮
6. **输出报告**：生成 coverage report，标注 ✅ 已覆盖 / ❌ 未覆盖 / ⚠️ 超出 Spec

```typescript
// Harness 自动校验的核心逻辑（伪代码，展示机制）
// 关键在这几行：Spec ACCEPTANCE → 校验函数 → 覆盖率报告

async function harnessApply(specPath: string) {
  // Step 1: 解析 Spec
  const spec = parseSpec(specPath)
  
  // Step 2: 加载约束（关键！这是 Harness 的核心价值）
  const agentsRules = await loadFile('AGENTS.md')     // 全局约束
  const frontendRules = await loadFile('frontend.md')  // 前端规范
  const systemContext = `${agentsRules}\n${frontendRules}`
  
  // Step 3: 生成代码（Spec + 约束一起给 AI）
  let code = await aiGenerate({
    prompt: spec.toPrompt(),
    systemContext  // ← 这就是"缰绳"
  })
  
  // Step 4-5: 自动校验 + 重试（最多 3 轮）
  for (let attempt = 0; attempt < 3; attempt++) {
    const report = await validate(code, spec.acceptance)
    if (report.allPassed) break
    
    // 把未通过项反馈给 AI 修复
    code = await aiFix({
      code,
      failures: report.failures,
      systemContext  // 修复时也加载约束
    })
  }
  
  // Step 6: 输出覆盖率报告
  return generateCoverageReport(code, spec.acceptance)
}
```

**关键在这几行**：Step 2 的 `systemContext` 注入——这是 Harness 区别于 Vibe Coding 的核心机制。Vibe Coding 把规范留在人脑里，Harness 把规范注入到 AI 的 System Context 里，确保每次生成都受约束。

### L3 设计层：为什么是"自动注入"而不是"更强的 Prompt"

一个直觉性的替代方案是：把规范写进 Prompt 不就行了？为什么要搞一套 Harness 机制？

**因为 Prompt 有三个根本性限制**：

1. **Prompt 不会自动加载**：你每次对话都得手动粘贴 AGENTS.md 和 frontend.md，遗漏一次就前功尽弃
2. **Prompt 不会自动校验**：AI 生成完代码，你得肉眼逐行检查是否符合 ACCEPTANCE
3. **Prompt 不会自动重试**：发现问题后，你得手动描述"这里不对"再让 AI 修，而 AI 修复时可能引入新问题

Harness 的设计取舍是：**把"人监督 AI"变成"机制监督 AI"**。人的注意力是有限的、会疲劳的、会遗漏的；机制是确定性的、不会疲劳的、不会遗漏的。代价是前期配置成本更高，但在项目规模超过 3 个模块时，收益远超成本。

> **反直觉**：很多人觉得"我的项目很小，不需要 Harness"。但恰恰是小项目最容易翻车——因为小项目你会放松警惕，更容易全程 Vibe Coding，等到发现问题时已经积累了 10+ 轮对话的混乱代码。

【图：架构图：Harness 的约束注入机制——AGENTS.md（全局约束：技术栈/命名规范/错误码规范/目录结构）+ frontend.md（前端约束：组件库/样式方案/响应式/可访问性）+ Spec（本模块需求）→ 三者合并为 System Context → AI 在约束内生成代码】

## 实战落地：RedLite 的 Harness 配置

### AGENTS.md（全局约束）

```markdown
# AGENTS.md —— RedLite 全局约束

## 技术栈（严格遵守，不得引入未列出的库）
- 前端：Next.js 14 (App Router) + React 18 + Tailwind CSS + shadcn/ui
- 图标：lucide-react
- 图片：next/image
- 后端：轻量 API Route + JWT (jsonwebtoken)
- 测试：Playwright (E2E)
- 语言：TypeScript (strict mode)

## 命名规范
- 组件：PascalCase（如 LoginForm.tsx）
- 文件：kebab-case（如 login-form.tsx）
- API 路由：kebab-case（如 /api/auth/login）
- 错误码：大写蛇形（如 EMAIL_EXISTS, INVALID_CREDENTIALS）
- 变量/函数：camelCase

## 目录结构
- specs/<module>-<unit>.md（Spec 文档）
- exec-plans/<module>-sprint.md（开发计划）
- components/<module>/（前端组件）
- app/page.tsx（页面组装）
- tests/e2e/<module>-<unit>.spec.ts（E2E 测试）

## 错误处理
- API 统一返回 { code, message, details? } 格式
- 前端统一用 try-catch，不用 .catch()
- 错误提示用中文，错误码用英文大写蛇形

## 禁止行为
- ❌ 引入未列出的 npm 包
- ❌ 使用 any 类型
- ❌ console.log 残留（生产代码）
- ❌ 硬编码 URL（用环境变量）
```

### frontend.md（前端规范）

```markdown
# frontend.md —— RedLite 前端规范

## 组件规范
- UI 组件统一用 shadcn/ui，不引入其他 UI 库
- 有交互的组件必须标 'use client'
- 图标统一用 lucide-react
- 图片统一用 next/image（含 width/height/alt）

## 表单规范
- 表单校验用 zod + react-hook-form
- 错误提示放在字段下方，红色文字，字号 text-sm
- 必填字段标星号
- 支持键盘 Tab 切换和 Enter 提交

## 样式规范
- 统一用 Tailwind CSS，不写自定义 CSS
- 响应式断点：sm(640) / md(768) / lg(1024)
- 暗色模式：暂不支持，只做浅色主题

## 路由规范
- App Router 约定式路由
- 页面文件：app/<route>/page.tsx
- 布局文件：app/<route>/layout.tsx
```

这两份文件放在项目根目录，Harness 每次执行 `/opsx:apply` 时自动加载——你不需要在每次对话里重复粘贴这些规范。

## 可抄走的硬货：Harness 最小配置模板

```markdown
# 你的项目的 AGENTS.md 模板（直接抄走改）

# AGENTS.md —— [你的项目名]

## 技术栈
- 前端：[框架] + [UI库] + [样式方案]
- 后端：[框架] + [鉴权方案]
- 测试：[测试框架]
- 语言：TypeScript

## 命名规范
- 组件：PascalCase
- 文件：kebab-case
- API：kebab-case
- 错误码：UPPER_SNAKE_CASE

## 目录结构
- components/<module>/
- app/<route>/page.tsx
- tests/e2e/<module>.spec.ts

## 错误处理
- API 返回格式：{ code: string, message: string, details?: Record<string, string> }
- 前端统一 try-catch

## 禁止行为
- ❌ [列出你不想要的技术/模式/做法]
```

## 速查清单：Harness 配置评估表

| 检查项 | 说明 | 状态 |
|---|---|---|
| AGENTS.md 存在且有技术栈清单 | 列出所有允许的库和版本 | - [ ] |
| AGENTS.md 有命名规范 | 组件/文件/API/变量命名统一 | - [ ] |
| AGENTS.md 有目录结构约定 | 文件放哪、怎么命名 | - [ ] |
| AGENTS.md 有"禁止行为"列表 | 明确告诉 AI 不能做什么 | - [ ] |
| frontend.md 存在且有组件规范 | UI 库统一、交互组件标 'use client' | - [ ] |
| frontend.md 有表单规范 | 校验方案、错误提示位置、键盘可访问 | - [ ] |
| Harness 自动加载已配置 | 执行 apply 时自动注入 AGENTS.md + frontend.md | - [ ] |
| 自动校验 + 重试已开启 | 对照 ACCEPTANCE 检查，最多 3 轮 | - [ ] |

## 独到小结 + 动手点

**Harness 的本质是"把人的注意力外包给机制"。** 你不可能在每轮对话里都记得告诉 AI"用 shadcn/ui"、"错误码大写蛇形"、"组件放 components/ 目录"——但 Harness 可以。前期花 30 分钟写好 AGENTS.md 和 frontend.md，换来整个项目的一致性保证。

**动手点**：用上面的模板，为你的 RedLite 项目创建 AGENTS.md。重点填好"技术栈"和"禁止行为"两个部分——前者约束 AI 不引入奇怪的库，后者约束 AI 不做你不想让它做的事。

---

> 上一篇讲了 SDD（Spec 约束需求），这一篇讲了 Harness（机制约束 AI）。但两者单独用都有局限——下一篇：**SDD+Harness 合体，如何让 Vibe Coding 从"能用"变成"好用"**。
