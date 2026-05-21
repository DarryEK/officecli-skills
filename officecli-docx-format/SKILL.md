---
name: officecli-docx-format
description: 中文Word文档排版格式标准化 — 通用企业文档格式 + GB/T 9704-2012 党政机关公文格式。当用户需要中文Word排版、公文格式、标题/正文标准化、页边距/页码设置、红头文件排版时使用。提供OfficeCLI docx命令完整用例。
---

# OfficeCLI 中文Word排版格式标准化

本技能提供两套中文Word排版标准，均映射为 OfficeCLI docx 命令：

1. **通用企业文档格式** — 微软雅黑字体，标准标题层级，适合企业内部报告、方案、总结
2. **GB/T 9704-2012 党政机关公文格式** — 国家级公文标准，适合通知、报告、请示、函

---

## 通用企业文档格式

> 适用场景：企业内部报告、工作总结、项目方案、会议纪要（非公文）

### 字体规范

| 元素 | 字体 | 字号 | 加粗 | 对齐 |
|------|------|------|------|------|
| 文档标题 | 微软雅黑 | 2号(22pt) | 是 | 居中 |
| 一级标题 | 微软雅黑 | 3号(16pt) | 是 | 左对齐 |
| 二级标题 | 微软雅黑 | 4号(14pt) | 是 | 左对齐 |
| 三级标题 | 微软雅黑 | 小4号(12pt) | 是 | 左对齐 |
| 正文 | 微软雅黑 | 5号(10.5pt) | 否 | 左对齐 |
| 图表标题 | 微软雅黑 | 5号(10.5pt) | 否 | 居中 |

### 段落规范

| 元素 | 行距 | 段前 | 段后 | 首行缩进 |
|------|------|------|------|----------|
| 标题 | 单倍 | 12pt | 6pt | 无 |
| 一级标题 | 单倍 | 12pt | 6pt | 无 |
| 二级标题 | 单倍 | 6pt | 3pt | 无 |
| 三级标题 | 单倍 | 3pt | 3pt | 无 |
| 正文 | 1.5倍 | 0pt | 0pt | 2字符 |

### 页面设置

| 参数 | 值 |
|------|----|
| 纸张 | A4 (210mm × 297mm) |
| 上边距 | 2.54cm |
| 下边距 | 2.54cm |
| 左边距 | 3.17cm |
| 右边距 | 3.17cm |

### OfficeCLI 实现

```bash
# === 页面设置 ===
officecli set "$FILE" / --prop pageWidth=11906 --prop pageHeight=16838 \
  --prop marginTop=1440 --prop marginBottom=1440 \
  --prop marginLeft=1800 --prop marginRight=1800

# === 标题（22pt 微软雅黑 加粗 居中） ===
officecli add "$FILE" /body --type paragraph \
  --prop text="2026年度工作总结" \
  --prop font=微软雅黑 --prop size=22pt --prop bold=true --prop align=center \
  --prop spaceAfter=6pt

# === 一级标题（16pt 微软雅黑 加粗 左对齐） ===
officecli add "$FILE" /body --type paragraph \
  --prop text="一、经营概况" \
  --prop font=微软雅黑 --prop size=16pt --prop bold=true \
  --prop spaceBefore=12pt --prop spaceAfter=6pt

# === 二级标题（14pt 微软雅黑 加粗 左对齐） ===
officecli add "$FILE" /body --type paragraph \
  --prop text="（一）收入分析" \
  --prop font=微软雅黑 --prop size=14pt --prop bold=true \
  --prop spaceBefore=6pt --prop spaceAfter=3pt

# === 三级标题（12pt 微软雅黑 加粗 左对齐） ===
officecli add "$FILE" /body --type paragraph \
  --prop text="1. 主营业务收入" \
  --prop font=微软雅黑 --prop size=12pt --prop bold=true \
  --prop spaceBefore=3pt --prop spaceAfter=3pt

# === 正文（10.5pt 微软雅黑 1.5倍行距 首行缩进2字符） ===
officecli add "$FILE" /body --type paragraph \
  --prop text="2026年，公司在全体员工的共同努力下，实现了营业收入同比增长18%的良好业绩。..." \
  --prop font=微软雅黑 --prop size=10.5pt \
  --prop line=1.5 --prop firstLineIndent=420 \
  --prop spaceBefore=0 --prop spaceAfter=0
```

---

## GB/T 9704-2012 党政机关公文格式

> 适用场景：通知、报告、请示、函、纪要、通报、批复等正式公文

### 页面设置

| 参数 | 国标值 | OfficeCLI (twips) |
|------|--------|-------------------|
| 纸张 | A4 210×297mm | 11906 × 16838 |
| 上边距(天头) | 37mm ±1mm | 2098 |
| 下边距 | 35mm | 1984 |
| 左边距(订口) | 28mm ±1mm | 1588 |
| 右边距 | 26mm | 1474 |
| 版心 | 156×225mm | 8844 × 12756 |

### 字体字号

| 公文要素 | 字体 | 字号 | OfficeCLI size |
|----------|------|------|----------------|
| 发文机关标志(红头) | 小标宋体 | 二号 | 22pt |
| 标题 | 小标宋体 | 二号 | 22pt |
| 正文 | 仿宋_GB2312 | 三号 | 16pt |
| 一级标题（一、二、） | 黑体 | 三号 | 16pt |
| 二级标题（（一）（二）） | 楷体_GB2312 | 三号 | 16pt |
| 三级标题（1. 2.） | 仿宋_GB2312 | 三号 | 16pt |
| 四级标题（（1）（2）） | 仿宋_GB2312 | 三号 | 16pt |
| 密级/紧急程度 | 黑体 | 三号 | 16pt |
| 发文字号 | 仿宋_GB2312 | 三号 | 16pt |
| 签发人标签 | 仿宋_GB2312 | 三号 | 16pt |
| 签发人姓名 | 楷体_GB2312 | 三号 | 16pt |
| 抄送机关 | 仿宋_GB2312 | 四号 | 14pt |
| 印发机关/日期 | 仿宋_GB2312 | 四号 | 14pt |
| 页码 | 宋体 | 四号半角 | 14pt |

### 段落格式

| 元素 | 行距 | 缩进 | 对齐 |
|------|------|------|------|
| 标题 | 固定值32pt | 无 | 居中 |
| 正文 | 固定值28pt | 首行缩进2字符(32pt) | 两端对齐 |
| 一级标题 | 固定值28pt | 无 | 左对齐 |
| 二级标题 | 固定值28pt | 无 | 左对齐 |
| 主送机关 | 固定值28pt | 居左顶格 | 左对齐 |

### 结构层次序数

```
第一层：一、二、三、……       （黑体 三号）
第二层：（一）（二）（三）…… （楷体 三号）
第三层：1. 2. 3. ……        （仿宋 三号）
第四层：（1）（2）（3）……   （仿宋 三号）
```

### 页码

- 格式：`-1-`、`-2-`（数字左右各一条一字线）
- 单页码居右空一字，双页码居左空一字
- 距版心下边缘7mm
- 字体：四号宋体半角

### OfficeCLI 完整实现

```bash
# === 第一步：页面设置（A4 + 国标页边距） ===
officecli set "$FILE" / \
  --prop pageWidth=11906 --prop pageHeight=16838 \
  --prop marginTop=2098 --prop marginBottom=1984 \
  --prop marginLeft=1588 --prop marginRight=1474

# === 第二步：发文机关标志（红头）—— 通过段落实现 ===
# 注意：OfficeCLI 不支持直接设置文字颜色为红色以外的格式
# 红头需要红色字体，设置方法如下：
officecli add "$FILE" /body --type paragraph \
  --prop text="××省人民政府文件" \
  --prop font=方正小标宋简体 --prop size=22pt --prop bold=true \
  --prop align=center --prop color=FF0000 \
  --prop spaceBefore=0 --prop spaceAfter=360

# === 第三步：发文字号 ===
officecli add "$FILE" /body --type paragraph \
  --prop text="X政发〔2026〕12号" \
  --prop font=仿宋_GB2312 --prop size=16pt \
  --prop align=center --prop spaceAfter=0

# === 第四步：分隔线（红线） ===
# 使用带下边框的空段落实现
officecli add "$FILE" /body --type paragraph \
  --prop text="" --prop spaceAfter=480 \
  --prop pbdr.bottom="single;12;FF0000;0"

# === 第五步：标题（二号小标宋，居中） ===
officecli add "$FILE" /body --type paragraph \
  --prop text="××省人民政府关于开展2026年度安全生产检查工作的通知" \
  --prop font=方正小标宋简体 --prop size=22pt --prop bold=true \
  --prop align=center --prop line=32pt \
  --prop spaceBefore=480 --prop spaceAfter=0

# === 第六步：主送机关 ===
officecli add "$FILE" /body --type paragraph \
  --prop text="各市、县人民政府，省直各部门：" \
  --prop font=仿宋_GB2312 --prop size=16pt \
  --prop line=28pt \
  --prop spaceBefore=360 --prop spaceAfter=0

# === 第七步：正文（仿宋三号，固定值28磅，首行缩进2字符） ===
officecli add "$FILE" /body --type paragraph \
  --prop text="为切实加强2026年度安全生产工作，有效防范和遏制各类安全事故发生，保障人民群众生命财产安全，根据《中华人民共和国安全生产法》及相关法律法规，现就有关事项通知如下：" \
  --prop font=仿宋_GB2312 --prop size=16pt \
  --prop line=28pt --prop firstLineIndent=480 \
  --prop spaceBefore=0 --prop spaceAfter=0

# === 第八步：一级标题（黑体，三号） ===
officecli add "$FILE" /body --type paragraph \
  --prop text="一、总体要求" \
  --prop font=黑体 --prop size=16pt \
  --prop line=28pt \
  --prop spaceBefore=0 --prop spaceAfter=0

# === 第九步：正文段落 ===
officecli add "$FILE" /body --type paragraph \
  --prop text="深入贯彻落实习近平总书记关于加强安全生产的重要指示精神，坚持人民至上、生命至上..." \
  --prop font=仿宋_GB2312 --prop size=16pt \
  --prop line=28pt --prop firstLineIndent=480

# === 第十步：二级标题（楷体，三号） ===
officecli add "$FILE" /body --type paragraph \
  --prop text="（一）企业主体责任落实" \
  --prop font=楷体_GB2312 --prop size=16pt \
  --prop line=28pt \
  --prop spaceBefore=0 --prop spaceAfter=0

# === 第十一步：发文机关署名 + 成文日期 ===
officecli add "$FILE" /body --type paragraph \
  --prop text="××省人民政府" \
  --prop font=仿宋_GB2312 --prop size=16pt \
  --prop align=right --prop line=28pt \
  --prop spaceBefore=480

officecli add "$FILE" /body --type paragraph \
  --prop text="2026年5月22日" \
  --prop font=仿宋_GB2312 --prop size=16pt \
  --prop align=right --prop line=28pt

# === 第十二步：版记分隔线 + 抄送 + 印发信息 ===
officecli add "$FILE" /body --type paragraph \
  --prop text="" --prop pbdr.top="single;4;000000;0" \
  --prop spaceBefore=0 --prop spaceAfter=0

officecli add "$FILE" /body --type paragraph \
  --prop text="抄送：省委办公厅，省人大常委会办公厅，省政协办公厅。" \
  --prop font=仿宋_GB2312 --prop size=14pt

officecli add "$FILE" /body --type paragraph \
  --prop text="××省人民政府办公室　　　　　　　　2026年5月23日印发" \
  --prop font=仿宋_GB2312 --prop size=14pt

# 底部分隔线
officecli add "$FILE" /body --type paragraph \
  --prop text="" --prop pbdr.top="single;4;000000;0"

# === 第十三步：页码（通过页脚实现） ===
officecli add "$FILE" / --type footer --prop type=default \
  --prop text="- " --prop field=page --prop text=" -" \
  --prop align=center --prop size=14pt --prop font=宋体
```

### 公文要素编排顺序

公文各部分严格按以下顺序编排：

**版头（红色分隔线以上）：**
1. 份号（涉密公文左上角第一行，如 `000001`）
2. 密级和保密期限（左上角第二行，如 `机密★20年`）
3. 紧急程度（左上角第三行，如 `特急`）
4. 发文机关标志（红头）
5. 发文字号（如 `X政发〔2026〕12号`）
6. 签发人（上行文居右）
7. 红色分隔线

**主体（红色分隔线以下至版记之前）：**
8. 标题（二号小标宋，居中）
9. 主送机关（顶格，全角冒号结尾）
10. 正文（仿宋三号，首行缩进2字符）
11. 附件说明（正文下空一行，左空二字）
12. 发文机关署名（居右）
13. 成文日期（居右）
14. 附注（成文日期下一行，加圆括号）

**版记：**
15. 版记分隔线（粗-细-粗三条）
16. 抄送机关
17. 印发机关和印发日期
18. 版记分隔线

---

## Excel 表格在 Word 中的格式

当在 docx 中插入表格时，中文格式规范如下：

| 元素 | 字体 | 字号 | 格式 |
|------|------|------|------|
| 表头 | 微软雅黑/黑体 | 小四(12pt) | 加粗，底部边框1pt |
| 表体 | 微软雅黑/仿宋 | 五号(10.5pt) | 左对齐 |
| 表格标题 | 同上表 | 五号 | 表格上方居中 |
| 表格位置 | — | — | 居中显示 |

```bash
# 在正文后插入表格
officecli add "$FILE" /body --type table --prop rows=5 --prop cols=4 --prop width=100%

# 表头行
officecli set "$FILE" "/body/tbl[1]/tr[1]" --prop header=true \
  --prop c1=季度 --prop c2=营业收入 --prop c3=同比增长 --prop c4=备注
officecli set "$FILE" "/body/tbl[1]/tr[1]/tc[1]/p[1]/r[1]" \
  --prop font=微软雅黑 --prop size=12pt --prop bold=true
# 重复对其他表头单元格设置同样的字体...

# 数据行
officecli set "$FILE" "/body/tbl[1]/tr[2]" \
  --prop c1=Q1 --prop c2="¥1,200万" --prop c3=15% --prop c4=""
officecli set "$FILE" "/body/tbl[1]/tr[2]/tc[1]/p[1]/r[1]" \
  --prop font=微软雅黑 --prop size=10.5pt
```

---

## 段落层次序数完整对照表

| 层级 | 通用企业格式 | 公文格式(GB/T 9704) |
|------|-------------|---------------------|
| 第一层 | 一、二、三、…… | 一、二、三、……（黑体 三号） |
| 第二层 | （一）（二）（三）…… | （一）（二）（三）……（楷体 三号） |
| 第三层 | 1. 2. 3. …… | 1. 2. 3. ……（仿宋 三号） |
| 第四层 | （1）（2）（3）…… | （1）（2）（3）……（仿宋 三号） |

---

## 常见公文模板速查

### 通知模板

```bash
officecli add "$FILE" /body --type paragraph --prop text="关于开展××工作的通知" --prop font=方正小标宋简体 --prop size=22pt --prop bold=true --prop align=center
officecli add "$FILE" /body --type paragraph --prop text="各部门：" --prop font=仿宋_GB2312 --prop size=16pt
officecli add "$FILE" /body --type paragraph --prop text="为……，现就有关事项通知如下：" --prop font=仿宋_GB2312 --prop size=16pt --prop firstLineIndent=480
officecli add "$FILE" /body --type paragraph --prop text="一、……" --prop font=黑体 --prop size=16pt
# ...正文...
officecli add "$FILE" /body --type paragraph --prop text="特此通知。" --prop font=仿宋_GB2312 --prop size=16pt --prop firstLineIndent=480
```

### 请示模板

```bash
officecli add "$FILE" /body --type paragraph --prop text="关于××的请示" --prop font=方正小标宋简体 --prop size=22pt --prop bold=true --prop align=center
officecli add "$FILE" /body --type paragraph --prop text="×××：" --prop font=仿宋_GB2312 --prop size=16pt
officecli add "$FILE" /body --type paragraph --prop text="现就……问题请示如下：" --prop font=仿宋_GB2312 --prop size=16pt --prop firstLineIndent=480
# ...正文...
officecli add "$FILE" /body --type paragraph --prop text="妥否，请批示。" --prop font=仿宋_GB2312 --prop size=16pt --prop firstLineIndent=480
```

### 报告模板

```bash
officecli add "$FILE" /body --type paragraph --prop text="关于××情况的报告" --prop font=方正小标宋简体 --prop size=22pt --prop bold=true --prop align=center
officecli add "$FILE" /body --type paragraph --prop text="×××：" --prop font=仿宋_GB2312 --prop size=16pt
officecli add "$FILE" /body --type paragraph --prop text="现将……情况报告如下：" --prop font=仿宋_GB2312 --prop size=16pt --prop firstLineIndent=480
# ...正文...
officecli add "$FILE" /body --type paragraph --prop text="特此报告。" --prop font=仿宋_GB2312 --prop size=16pt --prop firstLineIndent=480
```

### 函模板

```bash
officecli add "$FILE" /body --type paragraph --prop text="关于××的函" --prop font=方正小标宋简体 --prop size=22pt --prop bold=true --prop align=center
officecli add "$FILE" /body --type paragraph --prop text="×××：" --prop font=仿宋_GB2312 --prop size=16pt
officecli add "$FILE" /body --type paragraph --prop text="现就……事项函告如下：" --prop font=仿宋_GB2312 --prop size=16pt --prop firstLineIndent=480
# ...正文...
officecli add "$FILE" /body --type paragraph --prop text="请予研究函复。" --prop font=仿宋_GB2312 --prop size=16pt --prop firstLineIndent=480
```

---

## 字体安装说明

GB/T 9704-2012 公文标准要求的特殊字体：

| 字体 | 用途 | Windows自带 | 下载来源 |
|------|------|-------------|----------|
| 方正小标宋简体 | 标题、红头 | 否 | 方正字库官网 |
| 仿宋_GB2312 | 正文 | 是(C:\Windows\Fonts) | 系统自带 |
| 黑体 | 一级标题 | 是 | 系统自带 |
| 楷体_GB2312 | 二级标题 | 是 | 系统自带 |
| 宋体 | 页码 | 是 | 系统自带 |

如果缺少方正小标宋简体，可用宋体加粗替代。缺少仿宋_GB2312时用仿宋替代。

```bash
# 替代方案：无方正小标宋简体时
--prop font=宋体 --prop bold=true
# 替代方案：无仿宋_GB2312时
--prop font=仿宋
# 替代方案：无楷体_GB2312时
--prop font=楷体
```

---

## 速查：字号与pt对照

| 字号 | pt |
|------|----|
| 初号 | 42pt |
| 小初 | 36pt |
| 一号 | 26pt |
| 小一 | 24pt |
| **二号** | **22pt** |
| 小二 | 18pt |
| **三号** | **16pt** |
| 小三 | 15pt |
| **四号** | **14pt** |
| 小四 | 12pt |
| **五号** | **10.5pt** |
| 小五 | 9pt |
