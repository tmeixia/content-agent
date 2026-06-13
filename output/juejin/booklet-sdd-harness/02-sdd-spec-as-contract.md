---
booklet: "SDD + Harness：用 AI 写出能交付的代码"
part: 1
article: 4
title: "SDD：用 Spec 把“感觉”变成“合同”"
prev: "03-why-vibe-coding-fails"
next: "05-harness-engineering-guardrails"
demo_project: "RedLite（简版图文社区）"
---

# SDD：用 Spec 把"感觉"变成"合同"

## 痛点开篇

上一篇我们拆解了 Vibe Coding 的三宗罪，核心问题是：**需求规格隐含在对话里，没有持久化文档**。在第 2 篇的全景图里，我说过 Spec 是“你给 AI 的施工合同”——这篇就来讲清楚这份合同怎么写、为什么它比“告诉 AI 一句话”有效得多。

但"写文档"这三个字，是软件工程里被低估了 20 年的难题。

你可能写过这样的需求文档："用户登录后可以发帖，发帖需要标题和正文。" 这不是 Spec，这是需求描述。AI 看了这段描述，至少还有 5 个自由发挥的空间：标题有长度限制吗？正文支持 Markdown 吗？发帖失败怎么提示？图片上传呢？草稿保存呢？

**SDD（Spec-Driven Development，规格驱动开发）的核心主张是：Spec 不是需求描述，Spec 是合同。** 合同的特点是——每一条都有明确的边界、可验收的标准、不可模糊的约束。

【图：对比图：需求描述 vs Spec——需求描述是"用户可以发帖"（模糊，AI有5个自由发挥空间）；Spec 是"标题1-100字/正文1-5000字/封面图≤5MB仅jpg/png/空标题显示红色校验提示/成功跳转详情页"（精确，AI无自由发挥空间）】

## 技术溯源：SDD 不是新概念，但 AI 让它重生了

Spec-Driven Development 的思想可以追溯到 2000 年代的 **Contract-First Development**（契约优先开发）——先写 API 契约（如 WSDL/OpenAPI），再实现代码。这个思路在微服务时代被验证为最佳实践：团队间先约定接口，再各自开发。

但在 AI 编程场景下，SDD 有了本质性的升级：

| 时代 | Spec 的作用 | 谁读 Spec |
|---|---|---|
| **Contract-First (2000s)** | 团队间约定 API 接口 | 人类开发者 |
| **BDD/TDD (2010s)** | 用 Given-When-Then 描述行为 | 人类 + 测试框架 |
| **SDD for AI (2025+)** | AI 的唯一输入 + 验收标准 + 测试种子 | **AI 生成代码 + 自动化验收** |

关键区别：**在传统开发中，Spec 是给人看的参考文档；在 AI 编程中，Spec 是 AI 的唯一输入源。** 你给 AI 的 Spec 有多精确，AI 的输出就有多精确。

这就像建筑工程里的图纸：图纸画到"这面墙大概在这个位置"，施工队会自由发挥；图纸画到"这面墙在 X 轴 3.14m 处，厚度 240mm，承重 200kN"，施工队只能照做。

> **反直觉**：很多人觉得写 Spec 是"浪费时间"，因为"我直接告诉 AI 更快"。实际上，一份好 Spec 的投入产出比极高——写 Spec 花 30 分钟，但能避免 3 小时的返工。这就是课程里的那句话：**写 Spec 是先慢后快，纯 Vibe 是先快后崩。**

## 原理深度解析：一份好 Spec 的四要素

SDD 的核心机制是把"需求"转化为 AI 可执行、可验收的 Spec 文档。一份合格 Spec 包含四要素：

【图：流程图：Spec 四要素的流转关系——模块边界（定义范围）→ 核心场景（WHEN/THEN/AND 描述行为）→ 数据结构（Props/接口定义）→ ACCEPTANCE（可勾选验收清单），四者形成闭环】

### 要素一：模块边界——先画圈，再说圈内做什么

模块边界回答两个问题：**包含什么** 和 **不包含什么**。"不包含"比"包含"更重要，因为它阻止了 AI 的自由发挥。

```markdown
## 模块边界（RedLite 登录注册）
### 包含
- 邮箱 + 密码注册（邮箱格式校验、密码 ≥ 8 位、含大小写+数字）
- 邮箱 + 密码登录（JWT 鉴权，Token 有效期 24h）
- 登录状态持久化（localStorage 存 Token，页面刷新后保持登录）

### 不包含（本期明确不做）
- 第三方登录（Google/GitHub）
- 邮箱验证（发送验证邮件）
- 密码找回
- 多设备登录互踢
```

**设计取舍**：为什么不做邮箱验证？因为 RedLite 是 Demo 项目，邮箱验证引入 SMTP 依赖，增加了环境复杂度但不增加 SDD 方法论的展示价值。在真实项目中，邮箱验证应该是"包含"。

### 要素二：核心场景——用 WHEN/THEN/AND 格式，不给 AI 模糊空间

核心场景是 Spec 的灵魂。它的格式借鉴了 BDD（Behavior-Driven Development）的 Given-When-Then，但针对 AI 编程做了简化：

```markdown
## 核心场景

### 场景 1：注册成功
- WHEN 用户填写有效邮箱（格式 xxx@xxx.xxx）和密码（≥8位，含大小写+数字）并点击注册
  THEN 调用 POST /api/auth/register 创建用户
  AND 返回 JWT Token
  AND 前端存储 Token 并跳转到帖子列表页

### 场景 2：邮箱已存在
- WHEN 用户填写的邮箱已被注册
  THEN 不调用后端接口（前端校验拦截）或后端返回 409 Conflict
  AND 邮箱输入框下方显示红色提示"该邮箱已被注册"
  AND 不清除已填写的表单内容

### 场景 3：密码强度不足
- WHEN 用户填写的密码少于 8 位或不含大写字母或不含数字
  THEN 密码输入框下方实时显示具体不满足的条件（如"需要至少一个大写字母"）
  AND 注册按钮置灰不可点击

### 场景 4：登录成功
- WHEN 用户填写正确的邮箱和密码并点击登录
  THEN 调用 POST /api/auth/login
  AND 返回 JWT Token（有效期 24h）
  AND 前端存储 Token 并跳转到帖子列表页

### 场景 5：登录失败
- WHEN 邮箱或密码错误
  THEN 显示通用错误"邮箱或密码错误"（不区分是邮箱不存在还是密码错误，防止枚举攻击）
```

**关键细节**：场景 5 的错误提示故意用"邮箱或密码错误"而不是"邮箱不存在"或"密码错误"——这是一个安全考量，防止攻击者通过错误信息枚举有效邮箱。**这种设计取舍必须在 Spec 里写明，否则 AI 会按"用户友好"的逻辑分开提示。**

### 要素三：数据结构——给 AI 精确的类型约束

```typescript
// RedLite 登录注册的数据结构

// 注册请求
interface RegisterInput {
  email: string    // 格式：xxx@xxx.xxx
  password: string // 长度：8-64，含大小写字母+数字
}

// 登录请求
interface LoginInput {
  email: string
  password: string
}

// 鉴权响应
interface AuthResponse {
  token: string    // JWT，有效期 24h
  user: {
    id: string
    email: string
    createdAt: string // ISO 8601
  }
}

// 错误响应
interface ApiError {
  code: 'EMAIL_EXISTS' | 'INVALID_CREDENTIALS' | 'VALIDATION_ERROR'
  message: string
  details?: Record<string, string> // 字段级错误，如 { password: "需要至少一个大写字母" }
}
```

### 要素四：ACCEPTANCE——可勾选的验收标准

```markdown
## ACCEPTANCE
- [ ] 注册：空邮箱提交时显示"请输入邮箱"
- [ ] 注册：无效邮箱格式显示"邮箱格式不正确"
- [ ] 注册：密码少于 8 位时实时提示，注册按钮置灰
- [ ] 注册：重复邮箱显示"该邮箱已被注册"
- [ ] 登录：正确凭据返回 Token 并跳转
- [ ] 登录：错误凭据显示"邮箱或密码错误"（不区分原因）
- [ ] Token 存储在 localStorage，页面刷新后保持登录
- [ ] 未登录访问帖子列表时重定向到登录页
- [ ] Lighthouse Performance > 90
- [ ] 所有表单交互支持键盘 Tab 切换和 Enter 提交
```

**ACCEPTANCE 直接对应测试用例**：每一条 `- [ ]` 都可以转化为一个 Playwright E2E 测试。这就是 SDD 的精髓——**Spec 即测试种子**。

## 实战落地：RedLite 登录模块完整 Spec

把上面四要素拼在一起，就是 RedLite 登录注册模块的完整 Spec：

```markdown
# spec: auth-register-login

## 模块边界
包含：邮箱+密码注册、邮箱+密码登录（JWT）、登录状态持久化
不包含：第三方登录、邮箱验证、密码找回、多设备互踢

## 核心场景
### 注册成功
- WHEN 有效邮箱+合规密码 → THEN POST /api/auth/register → 返回 Token → 跳转列表页
### 邮箱已存在
- WHEN 已注册邮箱 → THEN 409 → 显示"该邮箱已被注册" → 不清空表单
### 密码强度不足
- WHEN 不合规密码 → THEN 实时提示具体不满足条件 → 按钮置灰
### 登录成功
- WHEN 正确凭据 → THEN POST /api/auth/login → 返回 Token(24h) → 跳转列表页
### 登录失败
- WHEN 错误凭据 → THEN 显示"邮箱或密码错误"（不区分原因）

## 数据结构
RegisterInput { email: string, password: string(8-64) }
LoginInput { email: string, password: string }
AuthResponse { token: string, user: { id, email, createdAt } }
ApiError { code: 'EMAIL_EXISTS'|'INVALID_CREDENTIALS'|'VALIDATION_ERROR', message, details? }

## ACCEPTANCE
- [ ] 空邮箱提示
- [ ] 无效邮箱格式提示
- [ ] 密码不足实时提示+按钮置灰
- [ ] 重复邮箱提示
- [ ] 登录成功跳转
- [ ] 登录失败通用错误
- [ ] Token 持久化
- [ ] 未登录重定向
- [ ] Lighthouse > 90
- [ ] 键盘可访问
```

**文件路径**：`specs/auth-register-login.md`

> **避坑提示**：Spec 不是写一次就完事。实际开发中，Spec 会经历 2-3 轮迭代（写完后发现遗漏的边界条件、和 AI 生成结果对账后补充）。但迭代是**在文档上迭代**，不是在对话里重新描述。

## 可抄走的硬货：Spec 写作 Prompt

当你不想手写 Spec 时，可以用这个 Prompt 让 AI 帮你生成初稿，然后人工校准：

```markdown
# Spec 生成 Prompt（直接抄走使用）

你是一位资深前端架构师。请根据以下需求，生成一份 SDD 格式的 Spec 文档。

## 输入
- 模块名称：[填写]
- 功能描述：[一句话描述]
- 技术栈：Next.js 14 (App Router) + React 18 + Tailwind + shadcn/ui + TypeScript
- 后端：轻量 API + JWT

## 输出格式（严格遵循）

### 模块边界
- 包含：[功能列表，每项标注具体约束（如长度、格式、大小限制）]
- 不包含：[本期明确不做的功能，附原因]

### 核心场景（至少 5 个，覆盖正常+异常路径）
- WHEN [用户操作]
  THEN [系统响应]
  AND [附加行为]

### 数据结构（TypeScript interface，含注释说明约束）

### ACCEPTANCE（可勾选 checklist，每条对应一个测试用例）
- [ ] [具体可验证的行为]

## 技术约束
- 错误提示用中文
- 组件用 shadcn/ui，不引入其他 UI 库
- 表单校验用 zod
- 错误码用大写蛇形（如 EMAIL_EXISTS）
```

## 速查清单：Spec 质量评估表

| 检查项 | 合格标准 | 你的 Spec |
|---|---|---|
| 模块边界有"不包含"列表 | ✅ 有且附原因 | - [ ] |
| 核心场景 ≥ 5 个 | ✅ 覆盖正常+异常+边界 | - [ ] |
| 每个场景用 WHEN/THEN/AND 格式 | ✅ 无模糊描述 | - [ ] |
| 数据结构有 TypeScript 类型 | ✅ 含注释和约束说明 | - [ ] |
| ACCEPTANCE ≥ 8 条 | ✅ 每条可直接转测试 | - [ ] |
| 设计取舍有说明 | ✅ "不包含"项附原因 | - [ ] |
| 安全考量已覆盖 | ✅ 如防枚举、XSS、CSRF | - [ ] |

## 独到小结 + 动手点

**Spec 的本质是一种"认知压缩"——把产品需求、技术选型、安全考量、边界条件压缩成一份 AI 可执行的合同。** 写 Spec 的过程，就是逼自己把"大概是这样"的模糊想法，变成"必须是这样"的精确约束。这个过程不舒服，但这就是工程化的本质。

**动手点**：用上面的 Spec 生成 Prompt，为 RedLite 的"发帖功能"生成一份 Spec 初稿。然后对照质量评估表，看哪些地方需要人工校准。

---

> 上一篇我们讲了 Vibe Coding 为什么走不远，这一篇讲了 SDD 如何用 Spec 约束 AI。但 Spec 只是文档，**谁来保证 AI 生成代码时真的读了 Spec？** 下一篇：**Harness——给 AI 装工程护栏**。
