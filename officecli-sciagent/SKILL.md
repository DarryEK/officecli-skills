---
name: officecli-sciagent
description: 科研智能体技能全家桶 — 集成 SciAgent-Skills（197 生物信息学技能）、scientific-agent-skills（138+ 科学计算技能）、luwill/research-skills（8 项研究协作技能）。覆盖文献分析、科学计算、生物信息学、统计建模、科研写作全流程，并与 OfficeCLI 文档产出打通。当用户需要做生信分析、科学计算、文献综述、科研绘图、论文协作时使用。
---

# Scientific Agent Skills — 科研智能体技能全家桶

本技能整合三个开源科研技能仓库：

| 来源 | 技能数 | 覆盖领域 |
|------|--------|---------|
| [jaechang-hits/SciAgent-Skills](https://github.com/jaechang-hits/SciAgent-Skills) | 197 | 生物信息学、分子生物学、基因组学、蛋白质工程、细胞生物学 |
| [K-Dense-AI/scientific-agent-skills](https://github.com/K-Dense-AI/scientific-agent-skills) | 138+ | 科学 Python 库（RDKit/Scanpy/BioPython/astropy 等）+ 数据库 + 引用管理 |
| [luwill/research-skills](https://github.com/luwill/research-skills) | 8 | 文献侦查/论文分析/综述撰写/质量编辑/幻灯片生成 |

## 目录

1. [三大仓库总览](#三大仓库总览)
2. [文献综述工作流](#文献综述工作流)
3. [生物信息学分析](#生物信息学分析)
4. [科学计算与绘图](#科学计算与绘图)
5. [OfficeCLI 交付](#officecli-交付)
6. [QA](#qa)

## 三大仓库总览

### 1. SciAgent-Skills 领域分布（197 技能）

| 领域 | 技能数 | 示例 |
|------|--------|------|
| molecular-biology | ~25 | CRISPR guide design, primer design, qPCR analysis |
| genomics-bioinformatics | ~35 | RNA-seq, WGS, DESeq2, STAR aligner, GATK |
| proteomics-protein-engineering | ~20 | AutoDock Vina, AlphaFold, RDKit, PyMOL |
| structural-biology-drug-discovery | ~20 | Molecular dynamics, docking, pharmacophore |
| systems-biology-multiomics | ~15 | WGCNA, pathway enrichment, network analysis |
| cell-biology | ~15 | Cellpose, napari, flow cytometry, H&E analysis |
| biostatistics | ~12 | Bayesian modeling, survival analysis, hypothesis testing |
| data-visualization | ~10 | matplotlib, seaborn, Plotly, bioinformatics plots |
| scientific-computing | ~8 | PyTorch, scikit-learn, Dask, NumPy |
| scientific-writing | ~8 | Paper revision, figure legends, methods writing |
| lab-automation | ~5 | Liquid handler, LIMS integration |

### 2. scientific-agent-skills 类型分布（138+ 技能）

| 类型 | 数量 | 示例 |
|------|------|------|
| Python 包技能 | ~70 | RDKit, Scanpy, BioPython, PyTorch Lightning |
| 数据库技能 | ~53 | PubChem, ChEMBL, UniProt, PDB, COSMIC |
| 科学集成技能 | ~9 | Benchling, DNAnexus, LatchBio |
| 分析与沟通 | ~6 | 文献综述、科学写作、同行评审 |

### 3. research-skills 研究协作流水线（8 技能）

| 技能 | 角色 | 职责 |
|------|------|------|
| literature-scout | 文献侦查员 | 自动检索、筛选 Top K 文献 |
| paper-analyst | 论文分析师 | 精读论文、提取方法细节、构建对比表 |
| survey-director | 综述主管 | 管理多 Agent 协作，质量把控 |
| survey-writer | 综述写手 | 生成综述初稿，对比表驱动 |
| quality-editor | 质量编辑 | 检查逻辑一致性、引用准确性、学术诚信 |
| research-proposal | 研究计划撰写 | 研究背景→问题→方法→计划 |
| paper-slide-deck | PPT 生成 | 论文→幻灯片图像 |
| medical-imaging-review | 医学影像综述 | 领域特定综述流水线 |

## 文献综述工作流（luwill/research-skills）

完整的综述写作流水线遵循「文献侦查 → 论文分析 → 综述撰写 → 质量编辑」四阶段：

```
用户需求
    │
    ▼
┌─────────────────────────────────────────────────────────┐
│  Stage 1: 文献侦查 (literature-scout)                    │
│  ● 自动检索 (Google Scholar / PubMed / arXiv)            │
│  ● 筛选 Top 20+ 核心论文                                 │
│  ● 输出 literature_matrix.md                             │
└─────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────┐
│  Stage 2: 论文分析 (paper-analyst)                       │
│  ● 精读 Top 20 论文                                      │
│  ● 提取方法细节、实验设置、核心发现                         │
│  ● 构建跨论文对比表                                       │
│  ● 输出 paper_cards/ + comparison_table.md               │
└─────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────┐
│  Stage 3: 综述撰写 (survey-writer)                       │
│  ● 对比表驱动的写作                                       │
│  ● 按大纲分章节生成                                       │
│  ● 输出 survey_draft.md                                  │
└─────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────┐
│  Stage 4: 质量控制 (quality-editor)                      │
│  ● 检查逻辑一致性、引用准确性                               │
│  ● AIGC 风格检测                                         │
│  ● Overclaim 检查                                        │
│  ● 输出 quality_report.md                                │
└─────────────────────────────────────────────────────────┘
```

### 使用示例

```bash
# 1. literature-scout: 检索文献
/literature-scout "deep learning medical image segmentation 2024"

# 2. paper-analyst: 分析重点论文
/paper-analyst --paper "Attention U-Net" --focus method,results

# 3. survey-director: 启动综述流程
/survey-director --topic "AI in Radiology" --target journal "Nature Reviews"
```

## 生物信息学分析（SciAgent-Skills）

### RNA-seq 分析流水线

```bash
# 1. 质量评估 (FastQC)
fastqc sample_R1.fastq.gz sample_R2.fastq.gz -o qc/

# 2. 比对 (STAR)
STAR --genomeDir /ref/star/ --readFilesIn sample_R1.fastq.gz sample_R2.fastq.gz \
     --outFileNamePrefix sample_ --outSAMtype BAM SortedByCoordinate

# 3. 定量 (featureCounts)
featureCounts -a annotation.gtf -o counts.txt sample_Aligned.sortedByCoord.out.bam

# 4. 差异表达分析 (DESeq2 in R)
#   dds <- DESeqDataSetFromMatrix(countData, colData, design = ~condition)
#   dds <- DESeq(dds); results(dds)
```

### 单细胞分析（Scanpy）

```python
import scanpy as sc

adata = sc.read_10x_h5("filtered_feature_bc_matrix.h5")
sc.pp.filter_cells(adata, min_genes=200)
sc.pp.filter_genes(adata, min_cells=3)
sc.pp.normalize_total(adata, target_sum=1e4)
sc.pp.log1p(adata)
sc.pp.highly_variable_genes(adata, n_top_genes=2000)
sc.pp.pca(adata)
sc.pp.neighbors(adata)
sc.tl.umap(adata)
sc.tl.leiden(adata)

# 输出到 Excel（通过 OfficeCLI）
# sc.tl.rank_genes_groups(adata, 'leiden', method='wilcoxon')
# result = adata.uns['rank_genes_groups']
# → officecli xlsx 写入差异基因表
```

### 分子对接（AutoDock Vina）

```bash
# 准备受体和配体
prepare_receptor -r protein.pdb -o protein.pdbqt
prepare_ligand -l ligand.sdf -o ligand.pdbqt

# 运行对接
vina --receptor protein.pdbqt --ligand ligand.pdbqt \
     --center_x 0 --center_y 0 --center_z 0 \
     --size_x 20 --size_y 20 --size_z 20 \
     --exhaustiveness 8 --out output.pdbqt
```

## 科学计算与绘图（scientific-agent-skills）

### 引用管理（citation-management）

```bash
# BibTeX 生成
python -c "
from citation_management import doi_to_bibtex
bib = doi_to_bibtex('10.1038/s41586-023-06436-1')
print(bib)
"
```

### 科学绘图（matplotlib scientific plotting）

```python
import matplotlib.pyplot as plt
import matplotlib as mpl

mpl.rcParams.update({
    "font.family": "sans-serif",
    "font.sans-serif": ["Arial", "DejaVu Sans"],
    "font.size": 7,
    "axes.spines.right": False,
    "axes.spines.top": False,
    "svg.fonttype": "none"
})

fig, ax = plt.subplots(figsize=(3.5, 2.5))  # 单栏宽度
ax.errorbar(x, y, yerr=sd, fmt='o', color='steelblue', capsize=2)
ax.set_xlabel("Concentration (μM)")
ax.set_ylabel("Response (AU)")
fig.savefig("figure.svg", bbox_inches="tight")
fig.savefig("figure.pdf", bbox_inches="tight")
```

## OfficeCLI 交付

### 分析结果 → Excel

```bash
# 差异表达基因表
officecli create "$FILE"
officecli set "$FILE" /Sheet1/A1 --prop value=Gene --prop bold=true
officecli set "$FILE" /Sheet1/B1 --prop value=log2FC --prop bold=true
officecli set "$FILE" /Sheet1/C1 --prop value=p_adjust --prop bold=true
officecli set "$FILE" /Sheet1/D1 --prop value=Significant --prop bold=true

officecli set "$FILE" /Sheet1/A2 --prop value=BRCA1
officecli set "$FILE" /Sheet1/B2 --prop value=2.34 --prop numFmt="0.00"
officecli set "$FILE" /Sheet1/C2 --prop value=0.001 --prop numFmt="0.000"
officecli set "$FILE" /Sheet1/D2 --prop value=YES

# 格式化
officecli set "$FILE" "/Sheet1/col[A]" --prop width=15
officecli set "$FILE" "/Sheet1/col[B]" --prop width=10
officecli set "$FILE" "/Sheet1/col[C]" --prop width=10
officecli set "$FILE" "/Sheet1/col[D]" --prop width=12

officecli close "$FILE"
```

### 研究计划 → DOCX

```bash
# research-proposal 输出为结构化 docx
officecli create "$FILE" --type docx

officecli set "$FILE" "/chapter[1]/paragraph[1]" --prop text="研究背景与意义" --prop heading=1
officecli set "$FILE" "/chapter[1]/paragraph[2]" --prop text="...（研究背景内容）..."

officecli set "$FILE" "/chapter[2]/paragraph[1]" --prop text="研究目标与科学问题" --prop heading=1
officecli set "$FILE" "/chapter[2]/paragraph[2]" --prop text="...（研究目标）..."

officecli set "$FILE" "/chapter[3]/paragraph[1]" --prop text="研究方法与技术路线" --prop heading=1
officecli set "$FILE" "/chapter[3]/paragraph[2]" --prop text="...（方法描述）..."

officecli set "$FILE" "/chapter[4]/paragraph[1]" --prop text="预期成果与时间节点" --prop heading=1
officecli set "$FILE" "/chapter[4]/paragraph[2]" --prop text="...（计划）..."

officecli close "$FILE"
```

### 论文幻灯片 → PPTX

```bash
# paper-slide-deck + OfficeCLI
# 先用 paper-slide-deck 生成幻灯片图像
# 再用 OfficeCLI 组装为 editable pptx

officecli create "$FILE"

# 标题页
officecli set "$FILE" "/Slide1/text[1]" --prop value="论文标题" --prop font.size=28
officecli set "$FILE" "/Slide1/text[2]" --prop value="作者, 期刊, 年份" --prop font.size=16

# 插入分析结果图
officecli add "$FILE" /Slide2 --type image --prop file="outputs/figure1.png"

# 结果数据表格
officecli add "$FILE" /Slide3 --type table --prop rows=5 --prop cols=4
officecli set "$FILE" "/Slide3/table[1]/cell[1,1]" --prop value="Method"
officecli set "$FILE" "/Slide3/table[1]/cell[1,2]" --prop value="Accuracy"
officecli set "$FILE" "/Slide3/table[1]/cell[2,1]" --prop value="Ours"
officecli set "$FILE" "/Slide3/table[1]/cell[2,2]" --prop value=0.943

officecli close "$FILE"
```

## 技能安装

### SciAgent-Skills（197 生物信息学技能）

```bash
# Claude Code
/plugin marketplace add jaechang-hits/SciAgent-Skills
/plugin install sciagent-skills

# 或手动
git clone https://github.com/jaechang-hits/SciAgent-Skills.git .claude/skills/sciagent-skills
```

### scientific-agent-skills（138+ 科学计算技能）

```bash
npx skills add K-Dense-AI/scientific-agent-skills
```

### research-skills（8 研究协作技能）

```bash
git clone https://github.com/luwill/research-skills.git .claude/skills/research-skills
```

## QA

- 文献检索是否覆盖全了目标范围
- 基因/蛋白名称是否正确（大小写、格式）
- 统计方法是否与数据特性匹配
- 对比表是否覆盖所有核心论文
- 引用是否有 DOI 验证（防引用幻觉）
- 图表字体是否符合期刊要求
- OfficeCLI 交付物格式是否一致
- 生物信息学 pipeline 参数是否可复现

## 已知问题

1. **SciAgent-Skills 安装量大**：197 个技能文件，首次加载需一定时间
2. **scientific-agent-skills 依赖多**：涉及 70+ Python 包，需按需安装
3. **research-skills Agent 协作**：多角色流水线需 CLAUDE.md 配置，非开箱即用
4. **论文检索依赖 API**：Google Scholar/PubMed 可能有访问限制
5. **OfficeCLI 科学图表**：复杂统计图（火山图、热图、箱线图）建议用 matplotlib 生成后嵌入

## 版本信息

| 来源 | 版本 |
|------|------|
| SciAgent-Skills | v1.x (BixBench 92.0%) |
| scientific-agent-skills | v2.x |
| research-skills (luwill) | v1.x |
