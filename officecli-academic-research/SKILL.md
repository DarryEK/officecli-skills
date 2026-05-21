---
name: officecli-academic-research
description: 完整学术研究技能包 — Deep Research / Academic Paper / Reviewer / Pipeline 四模块，从选题到发表的 10 阶段全流程。涵盖文献回顾、论文撰写、同行评审、学术诚信验证、反幻觉引用、AIGC 风格治理。当用户需要学术研究、论文写作、文献综述、同行评审时使用。
---

# Academic Research Skills — 学术研究全流程工作台

四模块覆盖学术研究完整生命周期：**Research → Write → Review → Revise → Finalize**。

## 四模块架构

| 模块 | Agent 数 | 核心模式 | 职责 |
|------|----------|----------|------|
| **Deep Research** | 13 | full / socratic / systematic-review / lit-review / fact-check / quick / review | 深度研究：文献检索、苏格拉底引导、PRISMA 系统性回顾、事实核查 |
| **Academic Paper** | 12 | full / plan / revision / abstract-only / lit-review / format-convert / citation-check / disclosure / revision-coach / outline-only | 论文撰写：风格校准、写作质量检查、LaTeX 输出、引用格式转换、AI 使用声明 |
| **Academic Paper Reviewer** | 7 | full / re-review / quick / methodology-focus / guided / calibration | 同行评审：EIC + 3 动态审查者 + 魔鬼代言人，0-100 质量量表 |
| **Academic Pipeline** | 5 | 10-stage orchestrator | 全流程调度：状态追踪、自适应 checkpoint、学术诚信验证 |

## 10 阶段 Pipeline

```
Stage 1:    RESEARCH          → deep-research（socratic/full/quick）
Stage 2:    WRITE             → academic-paper（plan/full）
Stage 2.5:  INTEGRITY CHECK   → integrity_verification_agent（不可跳过）
Stage 3:    REVIEW            → academic-paper-reviewer（full/guided）
Stage 4:    REVISE            → academic-paper（revision）
Stage 3':   RE-REVIEW         → academic-paper-reviewer（re-review）
Stage 4':   RE-REVISE         → academic-paper（revision，最多 1 轮）
Stage 4.5:  FINAL INTEGRITY   → integrity_verification_agent（必须 100% PASS）
Stage 5:    FINALIZE          → academic-paper（format-convert → MD/DOCX/LaTeX/PDF）
Stage 6:    PROCESS SUMMARY   → 自动生成：协作质量评估 + AI 自我反思报告
```

### 关键设计

- **Mandatory 学术诚信闸门**：Stage 2.5 和 4.5 不可跳过，100% 引用/数据验证
- **7 类 AI 研究失败模式检查**（Lu 2026 Nature）：Stage 2.5/4.5 执行阻断式检查
- **抗谄媚协议**：魔鬼代言人反驳必须评分 ≥4/5 才允许让步
- **引用验证**：Semantic Scholar API + Levenshtein ≥0.70 标题比对
- **风格校准**：提供 3+ 篇过去论文，AI 学习你的写作风格

## Academic Pipeline 状态机

| 阶段 | 完成后确认 | 失败处理 |
|------|-----------|----------|
| Stage 1→2 | FULL checkpoint | 切换模式或缩小范围 |
| Stage 2→2.5 | FULL checkpoint | — |
| Stage 2.5 | MANDATORY | 最多 3 轮修正 + 重新验证 |
| Stage 3 | MANDATORY | Accept→4.5 / Minor|Major→4 / Reject→2 或终止 |
| Stage 4→3' | FULL checkpoint | — |
| Stage 3' | MANDATORY | Accept|Minor→4.5 / Major→4' |
| Stage 4'→4.5 | FULL checkpoint | 未解决问题标记为 Acknowledged Limitations |
| Stage 4.5 | MANDATORY | 最多 3 轮修正 |
| Stage 5 | MANDATORY | — |

### Checkpoint 类型

- **FULL**：首次 checkpoint、integrity 边界后、定稿前 — 完整交付物清单 + 决策看板
- **SLIM**：连续 2+ 次"继续"后 — 一行状态 + 继续/暂停
- **MANDATORY**：Integrity FAIL、审稿决策、Stage 5 — 不可跳过，必须用户输入

## Deep Research 7 种模式

| 模式 | 触发语 | 输出 |
|------|--------|------|
| full | "研究 X 的影响" | 完整研究报告 |
| socratic | "引导我研究 X" | 苏格拉底对话 + 研究计划 |
| quick | "给我 X 的快速摘要" | 快速简报 |
| systematic-review | "做系统性文献回顾" | PRISMA 流程图 + 质量评估 |
| lit-review | "做文献回顾" | 文献综述 |
| fact-check | "核查这些说法" | 事实核验报告 |
| review | "审查这篇论文的研究质量" | 研究方法评估 |

## Academic Paper 10 种模式

| 模式 | 触发语 | 输出 |
|------|--------|------|
| full | "帮我写论文" | 完整论文草稿 |
| plan | "引导我写论文" | 逐章苏格拉底规划 |
| outline-only | "搭论文大纲" | 大纲 |
| revision | "这是审稿意见" | 修订稿 + Response to Reviewers |
| revision-coach | "整理审稿意见" | 修订路线图 |
| abstract-only | "写摘要" | 双语摘要 |
| lit-review | "写成文献回顾论文" | 文献综述论文 |
| format-convert | "转 LaTeX/格式转换" | 目标格式文件 |
| citation-check | "检查引用格式" | 引用格式报告 |
| disclosure | "生成 AI 使用声明" | 特定期刊/会议 AI 声明 |

## Reviewer 6 种模式 + 质量量表

| 模式 | 用途 | 审查者构成 |
|------|------|-----------|
| full | 完整审查 | EIC + R1/R2/R3 + 魔鬼代言人 |
| quick | 快速评估 | 精简审查 |
| guided | 引导改进 | 对话式 |
| methodology-focus | 方法论聚焦 | 方法论专家 |
| re-review | 再审验收 | 验证修订 |
| calibration | 校准模式 | 用户提供 gold set 测量 FNR/FPR |

**0-100 质量量表**：≥80 接受、65-79 小修、50-64 大修、<50 退稿。

## 学术诚信验证

### 引用验证（v3.3）

- Semantic Scholar API Tier 0 验证
- Levenshtein ≥0.70 标题比对
- DOI 不符检测
- API 不可用时优雅降级

### 已知引用幻觉 5 类分类

| 类型 | 说明 |
|------|------|
| TF | 完全捏造（Title Fabrication） |
| PAC | 作者/会议正确（Plausible Authors & Conference） |
| IH | 引用歪曲（Interpretive Hallucination） |
| PH | 部分幻觉（Partial Hallucination） |
| SH | 幸存幻觉（Survivorship Hallucination） |

### 7 类 AI 研究失败模式（Lu 2026 Nature Stage 2.5/4.5 阻断）

1. 实现错误 — Implementation Bug
2. 幻觉实验结果 — Hallucinated Results
3. 取巧特征依赖 — Shortcut Reliance
4. 错误包装为发现 — Bug-as-Insight
5. 方法伪造 — Methodology Fabrication
6. 框架锁定 — Frame Lock
7. 引用幻觉 — Citation Hallucination

### 三层引用 Locator（v3.7.3）

每条引用携带三层 anchor：`<!--ref:slug--><!--anchor:<kind>:<value>-->`，`<kind>` ∈ `{quote, page, section, paragraph, none}`。Formatter hard gate 拒绝 `[UNVERIFIED CITATION — NO QUOTE OR PAGE LOCATOR]`。

### 跨索引三角测量（v3.9.0）

三索引引用污染检测：Semantic Scholar + OpenAlex + Crossref。4-tier advisory matrix（k=0/1/2/3 advisory 级别）。

## AIGC 风格治理

写作质量检查清单（5 大类）：
- AI 高频词汇警告（25 个词）
- 标点模式控制（em dash ≤3）
- 开头废话检测
- 结构模式警告（三项枚举强迫症、均匀段落、同义词循环）
- 句子长度变化检查

## 引用格式支持

| 格式 | 说明 |
|------|------|
| APA 7.0 | 默认，含中文引用规则 |
| Chicago | Notes & Author-Date |
| MLA | 现代语言协会格式 |
| IEEE | 工程领域格式 |
| Vancouver | 医学领域格式 |

## 论文结构支持

| 结构 | 适用 |
|------|------|
| IMRaD | 实证研究 |
| 主题式 | 文献回顾 |
| 理论分析 | 理论论文 |
| 个案研究 | 案例论文 |
| 政策简报 | 政策论文 |
| 研讨会论文 | 会议论文 |

## 版本信息

本技能整理自 [Imbad0202/academic-research-skills](https://github.com/Imbad0202/academic-research-skills) v3.9.4.2。

遵循 CC-BY-NC 4.0 许可：
```
Based on Academic Research Skills by Cheng-I Wu
https://github.com/Imbad0202/academic-research-skills
```
