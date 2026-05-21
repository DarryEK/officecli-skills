---
name: officecli-nature
description: Nature 期刊级学术文档产出技能包 — 论文 PPT 汇报/手稿排版/图表规范/引用管理/润色标准。将 Nature Portfolio 出版标准映射为 OfficeCLI 可执行的 docx/pptx/xlsx 操作。当用户需要学术汇报 PPT、论文手稿排版、Nature 级图表、引用格式化、数据可用性声明、审稿回复时使用。
---

# Nature-Style Academic Publishing with OfficeCLI

本技能整合自 [Yuan1z0825/nature-skills](https://github.com/Yuan1z0825/nature-skills) 的 9 个模块，将 Nature 期刊级别的学术标准转化为 OfficeCLI 可交付的文档操作。

## 九模块总览

| 模块 | 来源技能 | OfficeCLI 交付路径 | 成熟度 |
|------|----------|-------------------|--------|
| **Paper2PPT** | nature-paper2ppt | `officecli pptx` — 论文→汇报幻灯片 | Beta |
| **Figure** | nature-figure | matplotlib/R → `officecli xlsx` 嵌入 | Stable |
| **Polishing** | nature-polishing | `officecli docx` 润色标准 | Stable |
| **Writing** | nature-writing | `officecli docx` 手稿结构 | Draft |
| **Citation** | nature-citation | `officecli docx` 引用管理 | Beta |
| **Data** | nature-data | `officecli docx` 数据可用性声明 | Draft |
| **Reader** | nature-reader | 中英对照阅读器（Markdown） | Beta |
| **Response** | nature-response | `officecli docx` 审稿回复信 | Beta |
| **Academic Search** | nature-academic-search | MCP 文献检索工作流 | Beta |

## Setup

OfficeCLI 安装：

- **macOS / Linux**: `curl -fsSL https://d.officecli.ai/install.sh | bash`
- **Windows (PowerShell)**: `irm https://d.officecli.ai/install.ps1 | iex`

Python 依赖（批量 / 图表脚本用，非 OfficeCLI 必需）：

```bash
pip install matplotlib pillow python-pptx  # figure + paper2ppt
```

## ⚠️ Help-First Rule

本技能涉及大量 OfficeCLI 参数（图表类型、页码格式、引用样式）。不确定时先查帮助：

```bash
officecli help pptx                         # PPTX 元素列表
officecli help docx                         # DOCX 元素列表
officecli help xlsx                         # XLSX 元素列表
officecli help pptx slide                   # 幻灯片 schema
officecli help docx paragraph               # 段落 schema
officecli help xlsx chart                   # 图表 schema
officecli help pptx chart --json            # 机器可读 schema
```

当本技能与帮助冲突时，**help 是权威来源**。

## Shell & Execution Discipline

- **增量执行**：每个命令后检查 exit code。OfficeCLI 每次调用都会写文件，一条失败会级联损坏。
- **路径引用**：含 `[]` `!` `$` 的路径必须引用：`"/Slide1/shape[1]"`。
- **公式小心**：跨 sheet 引用含 `!`，用 heredoc batch 传入。
- **Python 脚本**：涉及 figure 生成时，先写脚本再预览，预览确认后再插入 Office 文件。

## 核心工作流程

### A. Paper → PPTX（nature-paper2ppt 映射）

将学术论文转化为 12-16 页 Nature 风格中文汇报幻灯片。这是本技能最常用的路径。

#### 原则

- **论证驱动，而非按章节搬运**：幻灯片逻辑应回答"问题→缺口→方法→证据→信任→意义→边界"七问
- **证据优先**：结果页以图表为主，文字为辅。避免纯文字结果页
- **反模板设计**：避免千篇一律的"三卡片+结论"布局。根据证据形状选择排版
- **自审循环**：PPTX 初稿后必须运行至少一轮自审+修正

#### 默认结构（15 分钟汇报）

| 页码 | 内容 | 布局 |
|------|------|------|
| 1 | 标题页（论文信息） | 大视觉/大字型 |
| 2 | 研究背景 | 短文字+示意 |
| 3 | 知识缺口/技术瓶颈 | 文字主导 |
| 4 | 核心问题与主张 | 单句+支撑 |
| 5 | 研究设计/技术路线 | 全宽流程图 |
| 6-8 | 关键证据 1-3 | 主图 60-75% + 窄注释 |
| 9 | 验证/对照/稳健性 | 图表+短标注 |
| 10 | 机制模型/综合框架 | 大图+底部条 |
| 11 | 创新点与可复用价值 | 2-4 要点 |
| 12 | 局限性与未解决问题 | 开放布局 |
| 13 | 总结与讨论 | 结论+边界 |

#### OfficeCLI 实现

```bash
FILE="output/presentation.pptx"

officecli create "$FILE"

# 标题页
officecli set "$FILE" "/Slide1/text[1]" --prop value="基于深度学习的医学影像分析"
officecli set "$FILE" "/Slide1/text[2]" --prop value="张三, 李四, 王五"
officecli set "$FILE" "/Slide1/text[3]" --prop value="Nature Medicine, 2025"

# 证据页：主图+注释
officecli add "$FILE" /Slide2 --type image --prop file="output/assets/fig1_result.png"
officecli set "$FILE" "/Slide2/text[1]" --prop value="模型在多中心数据上表现优于基线"
officecli set "$FILE" "/Slide2/text[2]" --prop value="AUC: 0.943 vs 0.871 (p<0.001)"

# 设置 16:9
officecli set "$FILE" /Slide1 --prop width=13.333 --prop height=7.5

officecli close "$FILE"
```

#### 图面裁剪自审清单

插入 PPTX 前检查每张图：
- 标题、坐标轴标签、图例、面板字母是否被裁剪
- 是否包含周围无关的正文文字
- 缩放后文字是否仍然可读
- 密集多面板图是否应拆分或裁剪为关键面板
- 分辨率是否足够

**裁剪缺陷 = 阻断性缺陷**，必须修复后才能交付。

#### 幻灯片文字预算

| 元素 | 字数限制 |
|------|---------|
| 标题 | 1 行（≤30 字） |
| 正文要点 | 每点 ≤18 字，每页 2-3 条 |
| 结果页 | 1 句解释 + ≤2 个标注 |
| 来源标注 | 小字，不抢视觉 |

超出限制 → 拆分幻灯片或将解释移入 Speaker Notes。

### B. 图表规范（nature-figure 映射）

#### 科学图表四原型

1. **定量网格** (quantitative grid)：多面板定量比较
2. **示意图主导组合** (schematic-led composite)：流程图+数据图
3. **图像板+定量** (image plate + quant)：显微图+量化数据
4. **非对称混合模态**：多种图表类型混合

#### 配色原则

- 每张图使用 **1 个中性色系 + 1 个信号色系 + 1 个强调色系**
- 保留红色/绿色用于方向性指示（增加/减少）
- 用直接标签优于图例（当类别空间固定时）

#### OfficeCLI 图表实现

```bash
# 柱状图（Nature 风格）
officecli add "$FILE" /Sheet1 --type chart \
  --prop type=column \
  --prop dataRange="Sheet1!A1:C10" \
  --prop anchor="D2:L20" \
  --prop colors="4D4D4D,5DA5DA,F15854" \
  --prop font.size=8

# 设置轴格式
officecli set "$FILE" "/Sheet1/chart[1]/categoryAxis" --prop tickLabelFont.size=7
officecli set "$FILE" "/Sheet1/chart[1]/valueAxis" --prop tickLabelFont.size=7

# 散点图+趋势线（Nature 风格 6.5pt 字体）
officecli add "$FILE" /Sheet1 --type chart \
  --prop type=scatter \
  --prop dataRange="Sheet1!E1:F8" \
  --prop anchor="D22:L36" \
  --prop font.size=6.5
```

**尺寸对照**（Nature 期刊标准）：
- 单栏图：宽 85mm
- 双栏图：宽 183mm
- 大字报式图：≦240mm
- 正文字体：5.5-7pt
- 坐标轴字体：6-7pt

#### 导出标准

三种格式必须同时输出：
```python
fig.savefig("figure.svg", bbox_inches="tight")   # 可编辑矢量
fig.savefig("figure.pdf", bbox_inches="tight")    # 印刷级 PDF
fig.savefig("figure.tiff", dpi=600, bbox_inches="tight")  # 高分辨率 TIFF
```

matplotlib rcParams（Nature 兼容）：

```python
import matplotlib as mpl
mpl.rcParams.update({
    "font.family": "sans-serif",
    "font.sans-serif": ["Arial", "DejaVu Sans", "sans-serif"],
    "svg.fonttype": "none",      # SVG 文字可编辑
    "pdf.fonttype": 42,          # PDF TrueType
    "font.size": 7,
    "axes.spines.right": False,
    "axes.spines.top": False,
    "axes.linewidth": 0.8,
    "legend.frameon": False,
})
```

### C. 学术写作润色标准（nature-polishing 映射）

应用到 OfficeCLI docx 产出中，作为内容质量门禁。

#### 段落层规则

| 章节 | 工作 | 禁忌 |
|------|------|------|
| Introduction | 开宽→收窄到缺口 | 不要总结 Results |
| Results | 报告观察到什么 | 不要混入 Discussion 解释 |
| Discussion | 与领域的关系、意义、边界 | 不要逐图重复 Results |
| Conclusion | 贡献→证据→含义→边界 | 不要引入新数据 |
| Abstract | 背景/问题→缺口→方法→结果→意义 | 不要超字数 |

#### 句子层规则

- 句子长度 **10-30 词**，不超过 30 词
- 首句避免废话（"In recent years" 等）
- 避免 AI 高频词汇（25 词黑名单：robust, leverage, delve, pivotal 等）
- em dash 每篇 ≤3 个
- 每段一个控制思想，新思想起新段

#### 动词校准

| 语境 | 推荐动词 |
|------|---------|
| Results | was detected, increased, showed, enabled, achieved |
| Discussion | may reflect, suggests that, could indicate, is likely due to |

#### Overclaim 检查

每次 Conclusion 后必须检查：
- "首次" → 有证据吗？
- "显著优于" → 有统计支持吗？
- "填补空白" → 真填补了吗？

### D. 引用管理（nature-citation 映射）

#### OfficeCLI 引用实现

```bash
# 在 docx 中插入引用
officecli set "$FILE" "/chapter[1]/paragraph[5]" --prop text="...previous work demonstrated this mechanism [1]. As shown in Zhang et al. [2], ..."

# 参考文献列表（手动排版，需保持悬挂缩进）
officecli set "$FILE" "/chapter[1]/paragraph[10]" --prop text="[1] Smith, J. et al. Nature 612, 45-52 (2023)."
officecli set "$FILE" "/chapter[1]/paragraph[10]" --prop hanging=true
officecli set "$FILE" "/chapter[1]/paragraph[11]" --prop text="[2] Zhang, L. et al. Science 376, 89-94 (2022)."
```

#### 引用验证三级体系

| 级别 | 验证方式 | 工具 |
|------|---------|------|
| T0 | DOI + Semantic Scholar API | `python scripts/nature_citation.py` |
| T1 | 标题 Levenshtein ≥0.70 比对 | 手动或脚本 |
| T2 | 全文验证 | 人工检查 |

#### 五类引用幻觉

| 类型 | 说明 | 检出方式 |
|------|------|---------|
| TF | 完全捏造 | T0 检查 + DOI 验证 |
| PAC | 作者/会议正确但无此论文 | T1 标题比对 |
| IH | 引用歪曲原意 | 人工检查 |
| PH | 部分幻觉（如页码错误） | T0 元数据检查 |
| SH | 幸存幻觉（已撤稿） | 撤稿标记检查 |

**严禁**在 docx 中插入未经验证的引用。每条引用必须经过至少 T0 验证。

#### 引用格式支持

| 格式 | OfficeCLI 实现 |
|------|---------------|
| APA 7.0 | 手动排版 author-date 引用 |
| Chicago | 脚注或 author-date |
| IEEE | `[1], [2]` 方括号编号 |
| Vancouver | 右上角编号 |

### E. 数据可用性声明（nature-data 映射）

用于 OfficeCLI docx 中的数据可用性章节。

#### 声明模板

```bash
# 插入 Data Availability 声明段
officecli set "$FILE" "/chapter[6]/paragraph[1]" --prop text="Data Availability" --prop heading=2
officecli set "$FILE" "/chapter[6]/paragraph[2]" --prop text="The raw sequencing data generated in this study have been deposited in the NCBI Sequence Read Archive under accession code PRJNAXXXXXX. Processed data and source data for figures are available in the Supplementary Information. The publicly available datasets used in this study include TCGA-LIHC (https://portal.gdc.cancer.gov) and GTEx (https://gtexportal.org). All other data supporting the findings are available from the corresponding author upon reasonable request, subject to a data transfer agreement."
```

#### 三条硬规则

1. **不虚构** DOI、登录号、存储库名
2. **首选学科专用存储库**（NCBI、EMBL、Figshare），非泛型
3. **"available upon request" 是弱声明**，必须有具体理由和访问路径

### F. 审稿回复信（nature-response 映射）

用于 OfficeCLI docx 生成逐点回复信。

```bash
# 审稿回复信结构
officecli set "$FILE" "/chapter[1]/paragraph[1]" --prop text="Response to Reviewers" --prop heading=1
officecli set "$FILE" "/chapter[1]/paragraph[2]" --prop text="We thank the Editor and Reviewers for their constructive comments. Below we address each point point-by-point." --prop italic=true

officecli set "$FILE" "/chapter[1]/paragraph[3]" --prop text="Reviewer 1, Comment 1" --prop bold=true
officecli set "$FILE" "/chapter[1]/paragraph[4]" --prop text="The reviewer raised a concern about..." --prop italic=true
officecli set "$FILE" "/chapter[1]/paragraph[5]" --prop text="Our response: We have added a new control experiment (Supplementary Fig. 3) to address this. The results confirm..." --prop indentLeft=1
```

#### 回复信红线

- **不得忽略任何 reviewer comment**
- **不得改变 reviewer 原意**（保留原文）
- **不虚构实验、行号、图面板、引用**
- 不敌对语言
- 不以时间/经费为由拒绝合理实验要求

### G. 论文阅读器（nature-reader 映射）

生成中英对照 Markdown 阅读器，非 OfficeCLI 直接输出，但可作为 PPTX 制作的前置步骤。

```bash
# 输出结构
output/paper.md              # 中英对照全文
output/source_map.json        # 来源锚点
output/translation_notes.md   # 翻译注释
output/assets/                # 图表裁剪
```

每段格式：
```markdown
<a id="S001"></a>
**Source:** p.3 S001

**Original:** [原文段落]

**中文:** [忠实翻译]
```

## QA — Required

交付前必须执行以下门禁检查。

### PPTX 质量门禁

- 幻灯片顺序遵循论文论证逻辑，而非按章节搬运
- 每页有且只有一个核心主张
- 结论式标题（陈述句，非标签式）
- 无虚构数据、方法、引用
- 结果页有来源标注，不删除原始图标签
- Speaker Notes 存在且有用
- 无裁剪缺陷：标题、坐标轴、图例、面板字母完整
- 主图在缩放后仍然可读
- 文字不溢出文本框
- 布局节奏有变化，不重复同一模板
- 无 AI 感"三卡片"泛型布局
- 使用 Office 安全字体（避免环境不可用字体）
- 运行 officecli validate（如支持）

### DOCX 质量门禁

- 无 `#REF!` 或公式错误
- 引用经过至少 T0（DOI）验证
- 无 AI 高频词汇（25 词黑名单检查）
- 句子长度均 ≤30 词
- em dash 使用 ≤3 处
- Overclaim 检查通过（"首次""显著优于"需有证据）
- Data Availability 声明无虚构字段
- 中文术语与英文缩写一致

### XLSX 质量门禁

- 无 `###` 截断（列宽足够）
- 图表配色不冲突（非默认彩虹配色）
- 数据范围引用前缀 sheet 名
- 数字格式符合 Nature 标准（小数位 1-2 位）
- 字体统一（Arial / Helvetica，无混合字型）

### 自审循环

交付前运行：

1. `officecli view "$FILE" html` — Read 输出路径，检查渲染效果
2. 记录缺陷（high / medium / low + 页码）
3. 修正所有 high 和 medium 缺陷
4. 重新生成文件
5. 重跑验证
6. 更新 QA 报告

## Known Issues & Pitfalls

1. **PPTX 图表不可变**：`set chart[N] --prop anchor=` 被拒绝（UNSUPPORTED props: anchor）。要调整大小/移动 → `remove` 后 `add`。
2. **字体缺失**：Nature 要求 Arial/Helvetica。中文环境没有 Arial → 先用内置字体，或安装后指定。
3. **图片路径含空格**：引号包裹 `--prop file="output/assets/fig 1.png"`。
4. **引用验证不可跳过**：即使时间紧，也必须运行至少 T0（DOI 级别）验证。
5. **batch 批量的限制**：纯值写入 ≤80 ops/block，跨 sheet 公式建议单条执行。
6. **libreoffice 渲染可选**：预览渲染用 `officecli view` 而非 LibreOffice。仅当需要 PPTX→PDF 时才用 LibreOffice。
7. **MCP 工具不可用时**：nature-academic-search 的 MCP 工具不可用时，回退到 WebSearch + 手动查找。

## 版本信息

本技能整理自 [Yuan1z0825/nature-skills](https://github.com/Yuan1z0825/nature-skills) v5.0.2（nature-polishing）。

9 个原始模块版本：
| 模块 | 版本 |
|------|------|
| nature-figure | v3.x |
| nature-polishing | v5.0.2 |
| nature-writing | v0.2.0 |
| nature-citation | v3.x |
| nature-data | v2.x |
| nature-reader | v2.x |
| nature-response | v0.1.0 (Beta) |
| nature-paper2ppt | v1.x |
| nature-academic-search | v2.x |

遵循 CC-BY-NC 4.0 许可。
