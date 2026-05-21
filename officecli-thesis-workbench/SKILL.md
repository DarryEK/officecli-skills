---
name: officecli-thesis-workbench
description: 本科毕业论文标准化交付工作台 — 以标准和证据为骨架，以四种 DOCX 交付路径为输出，把项目源码、学校模板、往届样文、文献和截图整合成可追溯的中文毕业论文。当用户需要写论文、生成Word、排版论文、整理参考文献、处理导师批注时使用。
---

# Chinese Thesis Workbench — 中文毕业论文工作台

将毕业论文拆分为可审查、可回退的步骤：收集材料 → 选择交付路径 → 构建证据 → 确认大纲 → 分章写作 → 生成/填充/编辑 DOCX → 质量检查。

## 四种 DOCX 交付路径

| 路径 | 输入 | 输出 | 格式策略 |
|------|------|------|----------|
| **M1** 默认样式生成 | Markdown | 全新 `.docx` | 内置默认格式（宋体小四正文、黑体标题） |
| **M2** 样文版式贴近 | Markdown + 样文分析 JSON | 全新 `.docx` | 从样文提取高置信度字号/字体/缩进，合并进默认值 |
| **M3** 模板副本填充 | Markdown + 学校模板 `.docx` + spec YAML | 模板副本被填充 | 复制模板 OOXML 底座，仅替换占位文本 + 插入章节正文 |
| **M4** 已有稿编辑 | 初稿 `.docx` + 编辑指令 | 同一份 `.docx` | 只改段落文本，不动 pPr/rPr/sectPr/批注 |

**M3 是格式要求严格的学校的推荐路径**——复制模板后只做文本替换和插入，模板的 styles.xml、页眉页脚、分节符全部原封不动。

## 工作台架构

```
治理侧 paper-context/
  workflow/         用户仪表盘、材料清单、内容取舍、阻断报告、用户决策、进度日志
  evidence/         项目证据、技术栈、数据库 Schema、API 列表
  literature/       参考文献抽取、交叉引用、联网检索建议
  standards/        标准解析、样式提取
  aigc/             AIGC 风格报告
  word-comments/    导师批注待办和修订记录

交付侧 paper-output/
  <论文标题>.md                       Markdown 源稿
  <论文标题>.docx                     主论文（默认/样文贴近/模板填充）
  <论文标题>-附件.docx               附件（图源码等）
  <论文标题>-image-map.json          图片映射
  <论文标题>-文献核验清单.json       参考文献核验
  figures/                            图表
  screenshots/                        截图
```

## 工作流（14步）

1. **intake_materials** — 收集材料，按必需/建议/可选分级，记录缺失影响
2. **init_workspace** — 初始化工作区 `thesis-ai-standard/`、`paper-context/`
3. **resolve_standards** — 填写 `standard-profile.yaml`（学校模板规范）
4. **analyze_sample_and_template** — 分析学校模板和往届样文，提取样式和结构
5. **build_evidence** — 从项目源码、数据库、API、测试中提取事实证据
6. **stop_and_report** — 证据/标准/图表不足时阻断或有限继续
7. **build_thesis_spec** — 填写 `thesis-ai-spec.yaml`（论文事实规格）
8. **build_figure_registry** — 填写 `figure-registry.yaml`（图表注册表）
9. **confirm_outline** — 确认章节结构、字数和内容取舍决策
10. **draft_chapters** — 仅从已确认的结构化事实和证据开始写作
11. **produce_assets** — 生成图表、截图、附录材料
12. **produce_docx** — 生成/编辑主论文 DOCX 和附录 DOCX（M1~M4）
13. **quality_gates** — 标准/证据/文献/图表/DOCX/AIGC 全面检查
14. **delivery_report** — 输出交付物清单、验证结果、未决事项

## 硬规则

- 学校要求和导师要求优先于任何默认规则
- **未完成材料收集前不写正文**
- 素材不足时先 `stop_and_report`，征得用户同意后可联网检索或头脑风暴
- 不编造功能、字段、API、测试结果、实验数据、参考文献
- AIGC 风格治理仅在证据链完成后运行，只改进学术表达，不新增事实
- 论文正文不得包含 AI 工作流语言
- 第四章（实现）必须绑定真实模块、截图、核心代码或同等证据
- 系统设计类论文缺少 E-R 图或同等数据设计证据不能标记完成
- 必须同时生成主论文 DOCX 和附录 DOCX
- 输出文件名使用论文标题，不使用 `final.docx`、`draft.docx` 等通用名
- 参考文献工作流：建池 → 核验 → 筛选 → 格式化 → 生成核验清单

## 用户控制文件

初始化后在 `paper-context/workflow/` 下生成五类控制文件：

| 文件 | 作用 |
|------|------|
| `user-dashboard.md` | 当前进度、待确认事项、下一步建议 |
| `material-inventory.md` | 材料等级、缺失影响、是否可继续 |
| `content-decisions.md` | 候选内容的取舍（正文/简写/附录/暂缓/不写） |
| `blocker-report.md` | 阻断原因、影响范围、可选路径、是否可有限继续 |
| `user-decisions.md` | 用户已确认的关键选择归档 |

## DOCX 内容格式规范（M1 默认样式）

当使用 M1 默认样式生成时，DOCX 格式如下：

| 元素 | 字体 | 字号 | 行距 | 对齐 |
|------|------|------|------|------|
| 论文标题 | 黑体 | 小二(18pt) | 固定值20pt | 居中 |
| 章标题（一、） | 黑体 | 三号(16pt) | 固定值20pt | 居中 |
| 节标题（1.1） | 黑体 | 四号(14pt) | 固定值20pt | 左对齐 |
| 条标题（1.1.1） | 黑体 | 小四(12pt) | 固定值20pt | 左对齐 |
| 正文 | 宋体 | 小四(12pt) | 固定值20pt | 首行缩进2字符 |
| 页眉 | 宋体 | 五号(10.5pt) | — | 居中 |
| 页码 | 宋体 | 五号(10.5pt) | — | 居中 |

## M3 模板填充工作流（推荐）

M3 是最推荐的交付路径，保持学校模板格式完全不变：

```bash
# 1. 分析模板结构
python scripts/docx/analyze_docx.py 学校模板.docx --json-out result.json

# 2. 从 markdown 论文 + 学校模板生成填充好的 docx
python scripts/docx/apply_textual_edits.py --from-template 学校模板.docx \
  --thesis-md paper-output/<论文标题>.md \
  --spec thesis-ai-standard/templates/thesis-ai-spec.yaml \
  --out paper-output/<论文标题>.docx
```

## M4 初稿编辑工作流

保留已有初稿的格式和批注，只做文本级修改：

```bash
python scripts/docx/apply_textual_edits.py draft.docx draft-edited.docx \
  --replace "原文锚点" "新文本" \
  --insert-after "锚点段落" "新增段落" \
  --delete "要删除的段落锚点"
```

## Word 批注修订工作流

当导师在 Word 中添加批注后：

1. 抽取导师批注：运行 `scripts/docx/extract_comments.py`
2. 逐条登记到 `paper-context/word-comments/`
3. 使用 M4 模式进行文本编辑
4. 记录变更日志（修改前 → 修改后 → 对应批注 ID）

## 质量门禁

交付前检查项：
- 所有事实性声明有证据来源或已验证文献支撑
- 每个图表有 `source_kind`、`source_path`、`evidence_source`
- 参考文献来自已验证池，有核验清单
- AIGC 风格修改未新增事实
- 主 DOCX 和附录 DOCX 存在于 `paper-output/`
- 截图和图表路径在 Windows 下可解析
- `blocker-report.md` 反映最新阻断状态
- `user-decisions.md` 记录用户已确认的选择

## 引用与许可

本技能复刻自 [ZyhSechub/chinese-thesis-workbench-skill](https://github.com/ZyhSechub/chinese-thesis-workbench-skill)，完整脚本和详细参考文档请参见原仓库。MIT License。
