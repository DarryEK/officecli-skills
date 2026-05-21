---
name: officecli-chart-colors
description: Excel/WPS 专业图表配色库 + OfficeCLI 图表配色指南。当用户需要 Excel/WPS 图表配色、财务报表配色、商务图表色板、工作汇报配色、风险等级配色、多系列柱状图/饼图/折线图配色、莫兰迪低饱和度配色、暗色大屏配色、图表类型选择时使用。内置27套精选色板+20种图表类型样式指南，输出标准#十六进制色值。
---

# OfficeCLI 图表配色指南 & Excel/WPS 专业图表配色库

本技能融合两大部分：
1. **OfficeCLI xlsx chart `colors` 属性用法** — 在 officecli 命令中直接指定 chart series 颜色的方法
2. **Excel/WPS 专业图表配色库** — 27套精选色板 + 20种图表类型样式指南

## 一、OfficeCLI Chart Colors 用法

在 officecli 中，chart series 颜色通过 `colors` 属性设置，格式为逗号分隔的十六进制色值（不带 `#`）：

```bash
officecli add "$FILE" /Summary type=chart --prop chartType=columnClustered \
  --prop dataRange="Summary!A1:C6" \
  --prop anchor="A8:L24" \
  --prop colors="1F4E79,4472C4,5B9BD5"
```

### 规则

- `colors` 值**不带 `#`** 前缀，直接写十六进制：`FF0000` 而非 `#FF0000`
- 多个颜色用逗号分隔，无空格：`"1F4E79,4472C4,5B9BD5"`
- 颜色数少于 series 数时，officecli 循环使用
- 颜色数多于 series 数时，多余颜色被忽略
- 不设置 `colors` 时，officecli 使用默认 Excel 调色板

### 示例：多系列柱状图配色

```bash
officecli add "$FILE" "/Summary" type=chart --prop chartType=columnClustered \
  --prop dataRange="Summary!A1:D7" \
  --prop anchor="A10:L26" \
  --prop chartTitle="月度营收对比" \
  --prop colors="1F4E79,5B9BD5,A0AEC0"
```

## 二、Excel/WPS 专业图表配色库

> 使用方法：在 officecli 中 chart 的 `colors` 属性中使用（去掉 `#`），或 Excel/WPS 选中图表数据系列 → 填充/线条 → 自定义颜色 → 粘贴 `#` 开头色值。

### 固定规则

1. 只面向 Excel / WPS 办公图表场景，不输出前端、大屏花哨配色，优先低饱和、商务稳重、打印不偏色。
2. 支持按需输出：3/5/6/7色，适配多系列数据图表、分类对比、趋势渐变、风险分级。
3. 所有色板均已预置，不许乱编新色号，保证Excel填充、线条、数据系列上色完美兼容。
4. 主动规避高饱和刺眼色，保证黑白打印也能区分色块，适合工作汇报、财务、供应链、生产报表。

### 预置色板

#### 基础必备5套

**1. 商务极简5色**｜适配Excel通用报表、条形图

```
#2E86AB, #A23B72, #F18F01, #C73E1D, #5D737E
```

OfficeCLI: `colors="2E86AB,A23B72,F18F01,C73E1D,5D737E"`

**2. 莫兰迪经典6色**｜适合PPT联动Excel、文艺报表

```
#BFB4A8, #A8B5B7, #D1C0B0, #9CA3A6, #C8B9B0, #8F979E
```

OfficeCLI: `colors="BFB4A8,A8B5B7,D1C0B0,9CA3A6,C8B9B0,8F979E"`

**3. Tableau10 原版**｜Excel专业数据分析、多维度对比

```
#4C78A8, #F58518, #E45756, #72B059, #EECA3B, #8172B2, #CC79A7, #FF9DA6, #9C755F, #BAB0AC
```

OfficeCLI: `colors="4C78A8,F58518,E45756,72B059,EECA3B,8172B2,CC79A7,FF9DA6,9C755F,BAB0AC"`

**4. ggplot学术5色**｜适合论文、课程作业、科研 Excel图表

```
#F8766D, #7CAE00, #00BFC4, #C77CFF, #619CFF
```

OfficeCLI: `colors="F8766D,7CAE00,00BFC4,C77CFF,619CFF"`

**5. 简易风险3色**｜Excel合格率、库存、绩效等级

```
#E53E3E, #D69E2E, #38A169
```

OfficeCLI: `colors="E53E3E,D69E2E,38A169"`

---

#### 商务职场5套

**6. 高级蓝灰商务6色**｜财务报表、月度汇总图

```
#1F4E79, #4472C4, #5B9BD5, #8FAADC, #B4C7E7, #D9E2F3
```

OfficeCLI: `colors="1F4E79,4472C4,5B9BD5,8FAADC,B4C7E7,D9E2F3"`

**7. 低饱和轻奢6色**｜年报、述职、极简WPS报表

```
#4A5568, #718096, #A0AEC0, #CBD5E0, #E2E8F0, #EDF2F7
```

OfficeCLI: `colors="4A5568,718096,A0AEC0,CBD5E0,E2E8F0,EDF2F7"`

**8. 藏青经典商务5色**｜政企、机关单位Excel图表

```
#0F3057, #1E5F99, #3A7CA5, #6195C3, #8BB3D9
```

OfficeCLI: `colors="0F3057,1E5F99,3A7CA5,6195C3,8BB3D9"`

**9. 深绿稳重5色**｜制造业、生产、供应链数据

```
#1E3F38, #2D5F55, #3F7F72, #5A998C, #7CB3A8
```

OfficeCLI: `colors="1E3F38,2D5F55,3F7F72,5A998C,7CB3A8"`

**10. 雾霾蓝灰6色**｜多系列折线图、对比柱状图

```
#2C3E50, #34495E, #5D6D7E, #85929E, #AEB6BF, #D0D3D4
```

OfficeCLI: `colors="2C3E50,34495E,5D6D7E,85929E,AEB6BF,D0D3D4"`

---

#### 莫兰迪进阶5套

**11. 莫兰迪大地6色**｜人事、人文、市场分析报表

```
#A89F91, #BFA89E, #C9B9AB, #D4C8BE, #DFD6CC, #E8E0D5
```

OfficeCLI: `colors="A89F91,BFA89E,C9B9AB,D4C8BE,DFD6CC,E8E0D5"`

**12. 莫兰迪冷调6色**｜冷淡风、极简办公报表

```
#8B9DA5, #9CACB4, #B0BFC7, #C1CDD5, #D1DBE2, #E1E9EF
```

OfficeCLI: `colors="8B9DA5,9CACB4,B0BFC7,C1CDD5,D1DBE2,E1E9EF"`

**13. 莫兰迪暖棕5色**｜零售、餐饮、消费数据

```
#967C6A, #A98C78, #BC9E87, #CFAF96, #E2C1A5
```

OfficeCLI: `colors="967C6A,A98C78,BC9E87,CFAF96,E2C1A5"`

**14. 莫兰迪青绿6色**｜医疗、环保、农业Excel图表

```
#8A9B91, #9CAFA3, #AFC2B5, #C1D5C7, #D3E8D9, #E5FBEB
```

OfficeCLI: `colors="8A9B91,9CAFA3,AFC2B5,C1D5C7,D3E8D9,E5FBEB"`

**15. 莫兰迪紫灰5色**｜教育、文创、心理统计图表

```
#8C8399, #9E94AB, #B0A6BD, #C2B8CF, #D4CAE1
```

OfficeCLI: `colors="8C8399,9E94AB,B0A6BD,C2B8CF,D4CAE1"`

---

#### 工业/数据统计5套

**16. 科技蓝渐变7色**｜设备监控、运维趋势图

```
#0A192F, #112240, #233554, #345077, #496F9E, #648CBE, #88A8D8
```

OfficeCLI: `colors="0A192F,112240,233554,345077,496F9E,648CBE,88A8D8"`

**17. 暗夜青森6色**｜深色风格WPS报表

```
#0F2724, #183B36, #22504A, #2C655E, #367A72, #408F86
```

OfficeCLI: `colors="0F2724,183B36,22504A,2C655E,367A72,408F86"`

**18. 工业硬核6色**｜机械、设备、专业图表

```
#2D3436, #636E72, #B2BEC3, #DFE6E9, #FDCB6E, #E17055
```

OfficeCLI: `colors="2D3436,636E72,B2BEC3,DFE6E9,FDCB6E,E17055"`

**19. 暖橙渐变7色**｜销售额、营收、增长趋势图

```
#E64A19, #F57C00, #FF9800, #FFB74D, #FFCC80, #FFE0B2, #FFF3E0
```

OfficeCLI: `colors="E64A19,F57C00,FF9800,FFB74D,FFCC80,FFE0B2,FFF3E0"`

**20. 森林治愈渐变7色**｜健康、公益、环保统计

```
#1B5E20, #2E7D32, #388E3C, #43A047, #4CAF50, #66BB6A, #81C784
```

OfficeCLI: `colors="1B5E20,2E7D32,388E3C,43A047,4CAF50,66BB6A,81C784"`

---

#### 学术/马卡龙/预警5套

**21. 学术期刊淡雅6色**｜论文Excel插图、课程设计

```
#5470C6, #91CC75, #FAC858, #EE6666, #73C0DE, #37A2DA
```

OfficeCLI: `colors="5470C6,91CC75,FAC858,EE6666,73C0DE,37A2DA"`

**22. 红黄绿风险5色**｜绩效、评级、不合格率图表

```
#E53E3E, #DD6B20, #D69E2E, #38A169, #3182CE
```

OfficeCLI: `colors="E53E3E,DD6B20,D69E2E,38A169,3182CE"`

**23. 马卡龙柔和6色**｜教育、少儿、新媒体数据

```
#FFB6C1, #FFDAB9, #E6E6FA, #98FB98, #87CEFA, #DDA0DD
```

OfficeCLI: `colors="FFB6C1,FFDAB9,E6E6FA,98FB98,87CEFA,DDA0DD"`

**24. 极光科技6色**｜互联网、IT行业报表

```
#0077BE, #00A3FF, #00C8FF, #00E5FF, #64FFDA, #A4FFE8
```

OfficeCLI: `colors="0077BE,00A3FF,00C8FF,00E5FF,64FFDA,A4FFE8"`

**25. 深空紫蓝5色**｜AI、科技公司内部报表

```
#20123A, #2E1D52, #3C286A, #4A3382, #583E9A
```

OfficeCLI: `colors="20123A,2E1D52,3C286A,4A3382,583E9A"`

---

#### 暗色大屏2套

**26. AntV 大屏7色**｜暗色背景数据大屏、监控面板、WPS演示

```
#36CFC9, #FA8C16, #F759AB, #4096FF, #73D13D, #9254DE, #FFC53D
```

OfficeCLI: `colors="36CFC9,FA8C16,F759AB,4096FF,73D13D,9254DE,FFC53D"`

**27. 暗色大屏渐变7色**｜深色主题仪表盘、数据看板、实时监控

```
#597EF7, #36CFC9, #FA8C16, #9254DE, #73D13D, #F759AB, #FFC53D
```

OfficeCLI: `colors="597EF7,36CFC9,FA8C16,9254DE,73D13D,F759AB,FFC53D"`

---

### 图表类型样式指南

#### 基础图表类型（20种）

| # | 类型 | 适用场景 | 样式要点 | 推荐色板 |
|---|------|----------|----------|----------|
| 1 | 折线图 | 趋势变化、时间序列 | 线宽2.5pt，圆点标记，平滑曲线 | 科技蓝渐变、AntV大屏 |
| 2 | 柱状图 | 分类对比、排名 | 柱间距50%，圆角顶部 | 商务蓝灰、莫兰迪经典 |
| 3 | 堆叠柱状图 | 构成分析、占比 | 无缝堆叠，白色1pt分隔线 | 低饱和轻奢、莫兰迪大地 |
| 4 | 条形图 | 类别多、名称长 | 水平排列，标签右对齐 | 雾霾蓝灰、深绿稳重 |
| 5 | 堆叠条形图 | 水平构成分析 | 百分比堆叠显示100% | 红黄绿风险、Tableau10 |
| 6 | 面积图 | 总量趋势、累积 | 填充透明度30-40%，无边框 | 科技蓝渐变、森林治愈 |
| 7 | 堆叠面积图 | 多系列累积趋势 | 透明度递减 | 马卡龙柔和、极光科技 |
| 8 | 散点图 | 相关性分析 | 点大小统一，轻微透明度 | AntV大屏、ggplot学术 |
| 9 | 气泡图 | 三维数据展示 | 大小映射第三维度，带边框 | 暗色大屏渐变、极光科技 |
| 10 | 饼图 | 简单占比(≤6项) | 扇区间距2-3pt，带标签 | 莫兰迪经典、Tableau10 |
| 11 | 环形图 | 占比+中心总计 | 内径40-50%，中心显示总计 | AntV大屏、红黄绿风险 |
| 12 | 雷达图 | 多维度评估 | 填充透明度20%，轴线浅色 | 莫兰迪紫灰、深空紫蓝 |
| 13 | 漏斗图 | 流程转化 | 上宽下窄，颜色渐变 | 暖橙渐变、科技蓝渐变 |
| 14 | 仪表盘图 | KPI完成度 | 红/黄/绿刻度分段 | 红黄绿风险、简易风险 |
| 15 | 热力图 | 时间矩阵 | 颜色深浅映射数值 | 科技蓝渐变（单色系） |
| 16 | 树形图 | 层级占比 | 面积映射数值，带标签 | Tableau10、学术期刊 |
| 17 | 瀑布图 | 增减变动 | 上升绿、下降红、总计蓝 | 红黄绿风险、简易风险 |
| 18 | 箱线图 | 分布统计 | 箱体透明度30% | 莫兰迪冷调、雾霾蓝灰 |
| 19 | 旭日图 | 多级层级占比 | 内到外层级展开 | 莫兰迪大地、Tableau10 |
| 20 | 关联图/弦图 | 节点关系、流量 | 节点颜色分类，连线带透明度 | 暗色大屏渐变、极光科技 |

#### 暗色大屏专用样式

- 背景色：`#141414` / `#1a1a2e` / `#0f172a`
- 文字色：`#e0e0e0` / `#d1d5db`
- 轴线/网格：`#333333` / `#374151`
- 数据标签：白色带轻微阴影
- 图例：底部或右侧，横向排列
- 标题：左对齐，14-16pt，白色

### OfficeCLI 完整示例

```bash
# 商务蓝灰配色柱状图
officecli add "$FILE" "/Revenue" type=chart --prop chartType=columnClustered \
  --prop dataRange="Revenue!A1:D7" \
  --prop anchor="A10:L26" \
  --prop chartTitle="月度营收（万元）" \
  --prop colors="1F4E79,4472C4,5B9BD5"

# 莫兰迪经典配色饼图
officecli add "$FILE" "/Summary" type=chart --prop chartType=pie \
  --prop dataRange="Summary!A1:B6" \
  --prop anchor="A1:H16" \
  --prop chartTitle="市场份额分布" \
  --prop colors="BFB4A8,A8B5B7,D1C0B0,9CA3A6,C8B9B0,8F979E"

# 红黄绿风险配色瀑布图
officecli add "$FILE" "/P&L" type=chart --prop chartType=waterfall \
  --prop dataRange="P&L!A1:B10" \
  --prop anchor="A1:I18" \
  --prop chartTitle="利润变动分析" \
  --prop colors="E53E3E,DD6B20,D69E2E,38A169"
```
