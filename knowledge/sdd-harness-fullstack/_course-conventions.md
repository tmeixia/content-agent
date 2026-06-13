# 课程约定与事实（SDD+Harness 全栈实战）

> 来源：学习自用户真实讲师稿《第6章_讲师稿_精简版.md》。
> imooc-script / imooc-ppt 在产出本课程内容时**必须**对齐以下事实与风格，保证与已录章节一致。

## 一、项目硬事实

- **产品名**：**WanderChina**（即"外国人来中国旅行"项目的产品名，正文统一用 WanderChina）。
- **品牌标语**：Discover China Like a Local；副标题 Your AI-powered travel companion for exploring China。
- **技术栈**：
  - 前端：**Next.js 14（App Router）+ React 18 + Tailwind CSS + shadcn/ui**；图标库 **lucide-react**；图片用 **next/image**；有交互的组件标 `'use client'`。
  - 后端：**Spring AI（Java）**。
  - 测试：**Playwright**（E2E）。语言 TypeScript。
- **目录/文件约定**：
  - `specs/<module>-<unit>.md`（Spec 文档）
  - `exec-plans/<module>-sprint.md`（开发计划）
  - `components/<module>/`（前端组件）、`app/page.tsx`（页面组装）
  - `tests/e2e/<module>-<unit>.spec.ts`（E2E 测试）
  - `lessons-learned/`、`error-log`（归档经验/踩坑）
  - `frontend.md`（前端规范）、`AGENTS.md`（全局约束）、`.harness/`（Harness 配置）

## 二、工具指令集（opsx = openSpec + superPower）

实战节演示这套指令，**按阶段自动调整产出**：
- `/opsx:explore` —— 探查项目现状（"工头进场先检查材料"）。
- `/opsx:propose` —— 排开发计划/Sprint（功能阶段输出开发排期；骨架阶段输出项目骨架）。
- `/opsx:apply <spec路径>` —— 按 Spec 生成代码（自动加载 frontend.md / AGENTS.md，规范自动套用，自带"生成→检查→修复"最多 3 轮）。
- `/opsx:archive` —— 归档：记 error-log、提炼 lessons-learned、标记完成。

## 三、Spec 标准格式

一份合格 Spec 含四块：
1. **模块边界**：包含什么 / 不包含什么。
2. **核心场景**：用 **WHEN / THEN / AND** 格式，覆盖正常路径 + 异常路径。
3. **数据结构**：组件 Props 定义，含类型与约束。
4. **验收标准 ACCEPTANCE**：可勾选 checklist，量化（如 Lighthouse Performance > 90）。

> Spec 即测试种子：WHEN=setup，THEN=断言，AND=额外断言 → 直接转 Playwright 测试。

## 四、交叉 Review 约定

双向对账，用 emoji 标记（**本课程允许 ✅❌⚠️ 作为功能标记**）：
- **Spec→代码（正向）**：找遗漏 → `❌ 未覆盖`
- **代码→Spec（反向）**：找越界 → `⚠️ 超出Spec`
- `✅ 已覆盖`，最后给 **覆盖率%** + 修复 Action Items。
- 核心原则：**Spec 是唯一真相源**，不在 Spec 里的功能再好也先删。

## 五、五步实战闭环（贯穿所有实战章）

```
拆Spec → 排计划 → 写代码 → Review → 测试
  ↑                                   |
  └────── 循环（Review/测试发现问题回来改）──┘
```
效率对比常用话术：传统 2-3 周 → SDD+Harness 半天到 1 天；"减少 90% 返工"。

## 六、招牌金句（可复用，保持口吻一致）
- "Spec 是唯一真相源。"
- "AI 是施工队，superPower 是工头，你是甲方——甲方不搬砖，甲方验收。"
- "粒度就是效率，拆对了每份 AI 转化率 90%+。"
- "写 Spec 是先慢后快，纯 vibe 是先快后崩。"
- "不是学会了才去用——是用了才叫学会。"
