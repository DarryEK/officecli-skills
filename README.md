# OfficeCLI Skills

[OfficeCLI](https://github.com/iOfficeAI/OfficeCLI) 是命令行生成 Office 文件的工具。本项目包含 OfficeCLI 的 Claude Code / AI 助手技能文件，帮助 AI 高效、规范地使用 OfficeCLI 构建各类办公文档。

## 技能列表

| 技能 | SKILL.md 行数 | 说明 |
|------|--------------|------|
| [`officecli-xlsx`](officecli-xlsx/SKILL.md) | ~740 | Excel 电子表格 — 创建/编辑/格式化/图表/数据透视表/VBA |
| [`officecli-docx`](officecli-docx/SKILL.md) | ~662 | Word 文档 — 标题层级/表格/页眉页脚/目录/域字段 |
| [`officecli-pptx`](officecli-pptx/SKILL.md) | ~587 | PowerPoint 演示文稿 — 幻灯片/图表/动画/连线图 |
| [`officecli-data-dashboard`](officecli-data-dashboard/SKILL.md) | ~409 | 数据仪表盘 — KPI 卡片/图表/迷你图/条件格式 |
| [`officecli-financial-model`](officecli-financial-model/SKILL.md) | ~562 | 金融模型 — 三表模型/DCF/LBO/敏感度分析 |
| [`officecli-chart-colors`](officecli-chart-colors/SKILL.md) | ~210 | 图表配色指南 — 27套专业色板 + 20种图表类型样式 |
| [`officecli-docx-format`](officecli-docx-format/SKILL.md) | ~310 | 中文排版格式标准化 — 通用企业格式 + GB/T 9704-2012公文格式 |
| [`officecli-thesis-workbench`](officecli-thesis-workbench/SKILL.md) | ~130 | 毕业论文工作台 — 四种DOCX交付路径 + 证据链 + 质量门禁 |
| [`officecli-academic-research`](officecli-academic-research/SKILL.md) | ~230 | 学术研究全流程 — 四模块32+Agent，10阶段pipeline，学术诚信验证 |

## 技能内容

每个技能包含：

- **Setup** — OfficeCLI 安装指引
- **Help-First Rule** — 遇到不确定的参数先查帮助
- **Shell & Execution Discipline** — 引号、转义、增量执行规范
- **Requirements for Outputs** — 交付质量标准（字体、颜色、格式）
- **Reading & Analysis** — 查看/提取/查询现有文件
- **Creating & Editing** — 创建与编辑操作详解
- **QA (Required)** — 交付前必须执行的质检门禁
- **Known Issues & Pitfalls** — 已知问题和避坑指南

## 使用方式

在 Claude Code 或其他支持技能的 AI 助手中，这些技能文件可自动或手动加载以指导 AI 使用 OfficeCLI。

## 许可

MIT
