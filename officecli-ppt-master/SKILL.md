---
name: officecli-ppt-master
description: PPT Master 风格的高质量 PPTX 生成技能。基于 SVG→DrawingML 渲染管线，支持 20+ 视觉模板、自定义配色/字体/图标、PDF/DOCX/URL/Markdown 多源输入。与 OfficeCLI 配合实现"AI 排版 + 原生可编辑 PPTX"交付。当用户需要制作精美演示文稿、学术汇报、商业提案、模板化 PPT 时使用。
---

# PPT Master — 高质量 PPTX 生成

本技能整合自 [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master)（10k+ Stars），一个基于 SVG→DrawingML 渲染管线的 AI PPTX 生成工具。

## 核心理念

**AI 是你的设计师，不是你的执笔人。** PPT Master 生成的 PPTX 是设计稿（design draft），不是成品。AI 处理视觉设计、布局和内容结构，交付高质量起点，最终精修在 PowerPoint 中完成。

## 技术架构

```
用户输入 (PDF/DOCX/XLSX/URL/Markdown)
    │
    ├── [源内容转换] → Markdown
    │
    ├── [策略师] → 八项确认 + 设计规范 → design_spec.md
    │   - 画布格式、页数、受众、视觉风格、配色、字体、图标策略、图片策略
    │
    ├── [图片采集] (AI 生成 / 网络搜索)
    │
    ├── [执行器] → SVG 页面生成 → svg_output/
    │   ├── [质量检查] svg_quality_checker.py（必须 0 错误通过）
    │   └── [备注生成] notes/total.md
    │
    ├── [图表校准] (可选，含数据图表时)
    │
    └── [后处理] → SVG → DrawingML → PPTX
        ├── exports/presentation_<timestamp>.pptx      ← 主交付物（原生形状，可编辑）
        └── exports/presentation_<timestamp>_svg.pptx  ← SVG 快照（可选，像素级精确参考）
```

## 安装

```bash
# 1. 安装 PPT Master
git clone https://github.com/hugohe3/ppt-master.git
cd ppt-master
pip install -r requirements.txt  # 依赖安装

# 2. 确保 OfficeCLI 已安装
# macOS / Linux
curl -fsSL https://d.officecli.ai/install.sh | bash
# Windows PowerShell
irm https://d.officecli.ai/install.ps1 | iex
```

## ⚠️ Help-First Rule

PPT Master 参数体系和 OfficeCLI pptx 命令繁多。不确定时先查帮助：

```bash
officecli help pptx                         # PPTX 元素列表
officecli help pptx slide                   # 幻灯片 schema
officecli help pptx shape                   # 形状 schema
officecli help pptx table                   # 表格 schema
officecli help pptx chart                   # 图表 schema

# PPT Master 帮助
python project_manager.py --help
```

## 工作流程

### A. 完整流程（PPT Master + OfficeCLI 混合）

推荐将 PPT Master 用于视觉布局、OfficeCLI 用于数据表和图表：

```
Step 1: 准备源材料 → PDF/URL/Markdown 放入 projects/ 目录
Step 2: PPT Master 策略师 → 确认画布/页数/风格/配色
Step 3: PPT Master 执行器 → 生成 SVG → 输出 initial.pptx
Step 4: OfficeCLI 注入数据 → 替换/添加表格和图表
Step 5: 最终微调 → PowerPoint 中手动精修
```

#### 步骤详解

**Step 1: 准备源文件**

```bash
mkdir -p projects/my-deck
# 放入源文件: paper.pdf, data.csv, notes.md 等
```

**Step 2: 策略师确认**（交互式问答，确认以下八项）

| 决策项 | 选项 |
|--------|------|
| 画布格式 | 16:9 (默认) / 4:3 / 小红书竖版 / 朋友圈方形 / 故事竖版 / A4 |
| 页数 | 10-20 页（默认 15） |
| 受众 | 专家 / 管理层 / 学生 / 大众 |
| 视觉风格 | 见模板列表 |
| 主色 | 2-3 色方案 |
| 字体 | 标题字体 + 正文字体 |
| 图标策略 | 纯文本 / Tabler 图标 / AI 自定义图标 |
| 图片策略 | 源自源文件 / AI 生成 / 网络搜索 |

**Step 3: 执行器生成**

```bash
python executor.py --project my-deck --output exports/
# → exports/presentation_20260522_120000.pptx
```

**Step 4: OfficeCLI 注入数据**

```bash
# 在 PPT Master 生成的 PPTX 上叠加数据表
officecli open "$FILE"

# 替换占位文本
officecli query "$FILE" 'text:contains("{{chart1_data}}")' --replace "Q1: 120, Q2: 145, Q3: 168, Q4: 192"

# 添加数据表格页
officecli add "$FILE" --type slide
officecli set "$FILE" "/Slide[last()]/text[1]" --prop value="实验数据对比" --prop font.size=18
officecli add "$FILE" "/Slide[last()]" --type table --prop rows=4 --prop cols=3

officecli close "$FILE"
```

### B. OfficeCLI 独立流程（轻量级替代）

当不需要 PPT Master 的复杂视觉模板时，可直接用 OfficeCLI 构建：

```bash
officecli create "$FILE"

# 封面
officecli set "$FILE" "/Slide1/text[1]" --prop value="2024 年度技术总结" --prop font.size=28
officecli set "$FILE" "/Slide1/text[2]" --prop value="汇报人: 张三 | 2024.12" --prop font.size=14

# 目录页
officecli add "$FILE" --type slide
officecli set "$FILE" "/Slide2/text[1]" --prop value="目录" --prop font.size=24
officecli set "$FILE" "/Slide2/text[2]" --prop value="1. 年度概况\n2. 重点项目\n3. 技术突破\n4. 明年规划"

# 图表页
officecli add "$FILE" --type slide
officecli set "$FILE" "/Slide3/text[1]" --prop value="营收趋势" --prop font.size=20
officecli add "$FILE" /Slide3 --type chart --prop type=column \
  --prop data="Q1:120,Q2:145,Q3:168,Q4:192"

# 表格页
officecli add "$FILE" --type slide
officecli add "$FILE" "/Slide4" --type table --prop rows=4 --prop cols=3

officecli close "$FILE"
```

## 视觉模板

PPT Master 内置 20+ 模板：

| 模板 | 适用场景 |
|------|---------|
| 默认 / 自由设计 | 通用场景 |
| Magazine | 杂志风格，美观大方 |
| Academic Report | 学术汇报 |
| Tech Keynote | 技术主题演讲 |
| McKinsey-style | 管理咨询风格 |
| Google-style | 简洁明亮 |
| Anthropic | AI 产品风格 |
| 学术答辩 | 论文答辩 |
| 政府红蓝 | 党政风格 |
| Pixel Retro | 像素复古 |

## 设计原则

### 模板形态三选一

1. **跳过模板**（默认）— 自由设计，AI 根据内容自主决定布局
2. **指名模板** — 在策略师阶段指定模板名称
3. **新建全局模板** — 使用 `/create-template` 工作流创建新模板

### 输出定位

- PPT Master 输出的是**设计稿**，不是最终成品
- 在 PowerPoint 中进行最终微调：替换形状、优化图表、调整配色
- AI 消除 90% 的"空白页恐惧"，但不是一键成品

### SVG → DrawingML 转换规则

- SVG 中的 `<text>` 元素 → PPTX 原生文本框（可在 PowerPoint 中编辑文字）
- SVG 中的 `<rect>`/`<circle>`/`<path>` → PPTX 原生形状
- SVG 中的 `<image>` → PPTX 嵌入图片
- 复杂渐变和滤镜 → 可能降级为 PNG 快照
- 字体 → 转换为 PowerPoint 可识别的字体名称

## 已知问题

1. **复杂 SVG 降级**：部分 SVG 滤镜/渐变在转换为 DrawingML 时可能丢失
2. **中文字体嵌入**：PPT Master 依赖系统字体，确保目标机器有对应字体
3. **批量运行耗时**：20+ 页幻灯片生成需 3-8 分钟
4. **OfficeCLI 定位问题**：在 PPT Master 生成的 PPTX 上使用 OfficeCLI 时，shape ID 可能不连续，建议用 `query` 先确认
5. **图表校准建议**：PPT Master 生成的图表以 SVG 形式嵌入，如需可编辑图表，建议用 OfficeCLI `add chart` 替换
6. **数据表格**：复杂表格建议用 OfficeCLI `add table` 插入，而非在 SVG 中绘制

## 版本信息

本技能整理自 [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master)（10k+ Stars）。
