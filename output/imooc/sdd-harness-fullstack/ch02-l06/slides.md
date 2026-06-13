---
stage: 3-ppt
course: SDD + Harness 规范化的 AI 全栈项目实战开发
chapter: 第二章 从认知澄清到工程落地
lesson: 第6节 SDD+Harness 如何让 Vibe Coding 走向工程化
status: draft-pending-review
generated: 2026-06-10
template: templates/imooc-ppt-template.md
source_script: ch02-l06/script.md
---

> ⚠ 使用**占位模板**（templates/imooc-ppt-template.md 仍为 PLACEHOLDER）。
> 待替换慕课网真实 PPT 模板后，按真版式（母版/配色/字号/单页上限）重生成。
> 本节为概念课：PPT 放结论金句 + 类比图示 + 一份真实 Spec，不堆文字。

# 第6节 PPT 内容（逐页）

## Slide 1 | 页型：cover
- 标题：SDD+Harness 如何让 Vibe Coding 走向工程化
- 副标题：第二章 · 第6节　|　案例：WanderChina
- 讲师备注：对应开场"把它们拧成一股绳"
- 视觉提示：课程主视觉 + 章节号

## Slide 2 | 页型：concept（钩子页）
- 标题：前十分钟封神，后两小时崩溃
- 要点：纯 Vibe 做 WanderChina 首页 → 改到第6次崩了，解 bug 比重写还久
- 金句：**纯凭感觉指挥 AI，能用，但走不到"能交付"**
- 讲师备注：对应"我给你讲个我的真事"翻车故事 +（互动停顿）
- 视觉提示：一条"爽→崩"的情绪曲线，第6次断崖下跌

## Slide 3 | 页型：concept
- 标题：Vibe Coding 的三宗罪
- 要点（三卡片）：① 输出质量不稳定（感觉会飘）② 缺乏规范约束（文件乱放）③ 难以团队协作（感觉只在你脑里）
- 金句：**不稳定、不规范、难协作 —— SDD 治第一个，Harness 治后两个**
- 讲师备注：对应"三宗罪"分镜
- 视觉提示：三张红色问题卡片并排

## Slide 4 | 页型：concept
- 标题：SDD = 把"感觉"变成"合同"
- 要点：Spec-Driven Development —— 先把要做什么写清楚，再让 AI 做
- 金句：**Spec 就是你和 AI 之间的一份合同**
- 讲师备注：对应【SDD 怎么治"不稳定"】，引出下一页 Spec 实例
- 视觉提示：左"一句话(飘)" → 右"一份Spec(稳)" 对比

## Slide 5 | 页型：code（Spec 实例）
- 标题：一份最小 Spec 长什么样（WanderChina 行程页）
- 代码（只放要讲的）：
  ```
  功能：行程展示页
  - 展示：标题/城市/天数/封面图
  - 交互：可按"城市"筛选
  - 边界：无行程显示"暂无行程"
  - 验收：筛选"北京"只显示北京行程
  ```
- 金句：**做什么、有什么、怎么算做对——白纸黑字，AI 不用猜**
- 讲师备注：对应 Spec 示例演示
- 视觉提示：高亮"验收"那行（可量化）

## Slide 6 | 页型：concept
- 标题：Harness = 给 AI 装"护栏"
- 要点：解决"怎么做"——AI 自由发挥还是会乱
- 金句：**像保龄球道两边的挡板，球再歪也滚不进沟**
- 讲师备注：对应【Harness 怎么治】开头类比
- 视觉提示：保龄球道 + 两侧护栏挡住歪球

## Slide 7 | 页型：concept
- 标题：护栏怎么挡？两件事
- 要点：① 结构化目录/文档规范（specs/、components/…谁看都一样）② 自动化约束（生成完自动检查，不合规打回重做）
- 金句：**规范让协作变可能，自动约束让质量有底线**
- 讲师备注：对应 Harness 两件事
- 视觉提示：左"统一目录树" + 右"自动检查拦截✅/❌"

## Slide 8 | 页型：concept（核心页）
- 标题：从"能用"到"好用"
- 要点（进化链）：Vibe Coding（快但飘）→ +SDD（合同/质量稳）→ +Harness（护栏/规范+协作）
- 金句：**SDD+Harness = Vibe Coding 的工程化升级**
- 讲师备注：对应【合起来】，本节最该记住的一页
- 视觉提示：三段递进箭头，逐段点亮

## Slide 9 | 页型：compare（误区页）
- 标题：两个最容易踩的误区
- 要点：
  - ❌ 以为要"取代" Vibe Coding —— 其实是升级，vibe 的爽不丢
  - ❌ 以为写 Spec"太慢" —— 纯 vibe 先快后崩，写 Spec 先慢后快
- 金句：**写 Spec 是先慢后快，纯 vibe 是先快后崩**
- 讲师备注：对应【容易错的点】，故意点出两个误区
- 视觉提示：两个红叉误区 + 时间轴对比（先慢后快 vs 先快后崩）

## Slide 10 | 页型：summary
- 标题：本节小结
- 要点：三个坑（不稳定/不规范/难协作）→ SDD 用合同治第一个 → Harness 用护栏治后两个
- 讲师备注：对应【本节小结】
- 视觉提示：3 坑 → 2 治 的映射图

## Slide 11 | 页型：practice
- 标题：动手练习 + 下一节预告
- 任务：把你最近让 AI 做的一件事，写成三行 Spec（做什么/有什么/怎么算做对）
- 预告：SDD 工具一大把——Harness / Superpowers / Spec-kit / Kiro / openSpec，下节挨个对比怎么选
- 讲师备注：对应练习 + 下一节钩子
- 视觉提示：练习模板留白 + 下节工具 logo 墙

---
共 11 页。讲稿分镜 ↔ PPT 页映射见各页"讲师备注"。
> 流水线下一步：Step 4 `imooc-rehearsal`（模拟试讲，校验讲稿↔PPT 一致性、时长、难点）→ Step 5 优化定稿。
