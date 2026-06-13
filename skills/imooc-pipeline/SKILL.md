---
name: imooc-pipeline
description: 慕课网课程生产总编排。串联七步流水线：确认主题→课程大纲→讲师稿→PPT→Agent模拟试讲报告→优化定稿→产出PPT与讲师稿定稿。当要为慕课网完整生产一门课/一节课时使用。
---

# 慕课课程生产流水线（总编排）

把慕课网的课程制作流程，固化成一条可重复、可定时的流水线。每一步调用一个阶段 skill，产物层层流转。

## 七步流程与对应 skill
| 步 | 阶段 | skill | 产物 |
|---|---|---|---|
| 1 | 确认主题 + 课程大纲 | `imooc-outline` | `00-course-outline.md` |
| 2 | 课程内容 + 讲师稿 | `imooc-script` | `<lesson>/script.md` |
| 3 | PPT 内容（套慕课模板） | `imooc-ppt` | `<lesson>/slides.md` |
| 4 | Agent 模拟试讲 + 报告 | `imooc-rehearsal` | `<lesson>/rehearsal-report.md` |
| 5 | 根据试讲优化 | `imooc-revise` | `<lesson>/script.final.md`、`slides.final.md`、`revision-notes.md` |
| 6 | 试讲反馈环（按需重跑 4→5） | rehearsal+revise | 同上（迭代） |
| 7 | 定稿产出 | 收口 | 讲师稿定稿 + PPT 定稿 |

> 模板依赖：Step 3 读取 `templates/imooc-ppt-template.md`（慕课官方《课件质量标准》提炼，2026-06-12 已确认为正式规范）。

## 输入
- 主题 `knowledge/<topic>/`
- 课程定位（目标学员/总时长，由 config 传入）
- PPT 模板 `templates/imooc-ppt-template.md`

## 输出目录结构
```
output/imooc/<topic>/
├── 00-course-outline.md
└── <lesson-id>/
    ├── script.md                # Step2 讲师稿
    ├── slides.md                # Step3 PPT 内容
    ├── rehearsal-report.md      # Step4 试讲报告
    ├── script.final.md          # Step5 讲师稿定稿
    ├── slides.final.md          # Step5 PPT 定稿
    └── revision-notes.md        # Step5 修订说明
```

## 执行步骤
1. 调 `imooc-outline` 出课程大纲 → 产物供后续每节定位。
2. 对大纲中的每一节（或 config 指定的节）：
   a. 调 `imooc-script` 出讲师稿；
   b. 调 `imooc-ppt` 出 PPT 内容（套模板，讲稿↔PPT 一一对应）；
   c. 调 `imooc-rehearsal` 模拟试讲出报告；
   d. 调 `imooc-revise` 据报告优化，产出定稿 + 修订说明；
   e. **反馈环**：若报告仍有"高"严重度必改项，重跑 c→d（最多 2 轮）。
3. 全部标 `status`（草稿/定稿）状态流转；定稿标 `final-pending-human-review`。
4. 输出流水线小结：每节走到第几步、试讲结论、是否触发了二轮迭代。

## 质量红线
- 默认人工终审：流水线产物到定稿为止，发布/录制由人决定。
- 试讲是 agent 模拟（`imooc-rehearsal`），用于把明显问题在录制前消灭，不替代真人终审。

## 与主 orchestrator 的关系
- 其他平台（极客/知乎/公众号/掘金…）是"单步适配"，由主 `orchestrator` 直接产出。
- 慕课是**多步流水线**，主 orchestrator 遇到 imooc 平台时**委托给本 `imooc-pipeline`**。
