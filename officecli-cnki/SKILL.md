---
name: officecli-cnki
description: 中国知网（CNKI）文献检索 + OfficeCLI 文档输出工作台。集成知网高级检索、期刊查询、批量导出、引用格式化，自动将检索结果输出为 OfficeCLI 可处理的 docx/xlsx。当用户需要知网查文献、管理中文参考文献、生成文献综述表格时使用。
---

# CNKI Academic Search — 知网学术检索与文档输出

本技能整合自 [cookjohn/cnki-skills](https://github.com/cookjohn/cnki-skills) 的 10 个知网操作技能，将知网文献检索与 OfficeCLI 文档产出打通。

## 技能概览

| 模块 | 来源技能 | 功能 |
|------|---------|------|
| 基本检索 | cnki-search | 关键词检索，返回结构化结果 |
| 高级检索 | cnki-advanced-search | 来源类别(Core/SCI/EI/CSSCI)、时间范围、作者、期刊筛选 |
| 论文详情 | cnki-paper-detail | 摘要、关键词、基金信息提取 |
| 期刊检索 | cnki-journal-search | 按名称/ISSN/CN 号查期刊 |
| 期刊收录 | cnki-journal-index | 查询收录情况和影响因子 |
| 期刊目录 | cnki-journal-toc | 浏览某期目录 |
| 翻页排序 | cnki-navigate-pages | 翻页与排序 |
| PDF 下载 | cnki-download | PDF/CAJ 下载 |
| 引用导出 | cnki-export | Zotero / GB/T 7714 / 其他引用格式 |
| 结果重解析 | cnki-parse-results | 重新解析当前结果页 |

## 前置依赖

### 1. Chrome DevTools MCP

```bash
claude mcp add chrome-devtools -- npx -y @anthropic-ai/chrome-devtools-mcp@latest
```

### 2. 安装本技能

```bash
# 知网技能需配合 Chrome 远程调试使用
# 启动 Chrome（需提前手动登录知网）
chrome --remote-debugging-port=9222
```

### 3. OfficeCLI（用于文档输出）

```bash
# macOS / Linux
curl -fsSL https://d.officecli.ai/install.sh | bash
# Windows PowerShell
irm https://d.officecli.ai/install.ps1 | iex
```

## 工作流程

### A. 文献检索 → 导出引用

```
用户提问 → cnki-search 检索 → cnki-paper-detail 取详情 → cnki-export 导出
```

```javascript
// cnki-search 核心调用（通过 Chrome DevTools MCP）
// 导航到知网搜索页，输入关键词，提取结果
// 在知网搜索页执行:
const query = "你的关键词";
const input = document.querySelector('input.search-input');
input.value = query;
input.dispatchEvent(new Event('input', { bubbles: true }));
document.querySelector('input.search-btn')?.click();
// 等待结果加载后提取:
const rows = document.querySelectorAll('.result-table-list tbody tr');
const results = Array.from(rows).map(row => ({
  title: row.querySelector('.sort-tit a')?.textContent?.trim(),
  link: row.querySelector('.sort-tit a')?.href,
  authors: row.querySelectorAll('.author')[0]?.textContent?.trim(),
  source: row.querySelector('.source-item')?.textContent?.trim(),
  date: row.querySelector('.date-item')?.textContent?.trim(),
}));
```

### B. 检索结果 → OfficeCLI 文献列表（xlsx）

```bash
# 1. 创建 Excel 文献列表
officecli create "$FILE"
officecli set "$FILE" /Sheet1/A1 --prop value=序号 --prop bold=true
officecli set "$FILE" /Sheet1/B1 --prop value=标题 --prop bold=true
officecli set "$FILE" /Sheet1/C1 --prop value=作者 --prop bold=true
officecli set "$FILE" /Sheet1/D1 --prop value=期刊 --prop bold=true
officecli set "$FILE" /Sheet1/E1 --prop value=年份 --prop bold=true
officecli set "$FILE" /Sheet1/F1 --prop value=摘要 --prop bold=true
officecli set "$FILE" /Sheet1/G1 --prop value=关键词 --prop bold=true

# 2. 填入检索结果（逐条）
officecli set "$FILE" /Sheet1/A2 --prop value=1
officecli set "$FILE" /Sheet1/B2 --prop value="基于深度学习的医学图像分割综述"
officecli set "$FILE" /Sheet1/C2 --prop value="张三, 李四, 王五"
officecli set "$FILE" /Sheet1/D2 --prop value="计算机学报"
officecli set "$FILE" /Sheet1/E2 --prop value=2024
officecli set "$FILE" /Sheet1/F2 --prop value="本文综述了深度学习在医学图像分割中的最新进展..."
officecli set "$FILE" /Sheet1/G2 --prop value="深度学习; 医学图像; 图像分割"

# 3. 格式化
officecli set "$FILE" "/Sheet1/col[A]" --prop width=6
officecli set "$FILE" "/Sheet1/col[B]" --prop width=45
officecli set "$FILE" "/Sheet1/col[C]" --prop width=20
officecli set "$FILE" "/Sheet1/col[D]" --prop width=20
officecli set "$FILE" "/Sheet1/col[E]" --prop width=8
officecli set "$FILE" "/Sheet1/col[F]" --prop width=60
officecli set "$FILE" "/Sheet1/col[G]" --prop width=30
officecli set "$FILE" /Sheet1 --prop freeze=A2

officecli close "$FILE"
```

### C. 引用导出 → OfficeCLI docx 参考文献

GB/T 7714 引用格式导出后，可通过 OfficeCLI 写入 docx：

```bash
# 在 docx 中插入参考文献章节
officecli set "$FILE" "/chapter[5]/paragraph[1]" --prop text="参考文献" --prop heading=1
officecli set "$FILE" "/chapter[5]/paragraph[2]" --prop text="[1] 张三, 李四, 王五. 基于深度学习的医学图像分割综述[J]. 计算机学报, 2024, 47(3): 567-589."
officecli set "$FILE" "/chapter[5]/paragraph[3]" --prop text="[2] Smith J, et al. Deep learning in medical imaging[J]. Nature Medicine, 2023, 29(5): 1123-1135."
officecli set "$FILE" "/chapter[5]/paragraph[2]" --prop hanging=true
officecli set "$FILE" "/chapter[5]/paragraph[3]" --prop hanging=true
```

### D. 高级检索 + 文献综述表

```javascript
// cnki-advanced-search 核心调用
// 设置来源类别、时间范围等高级条件
const query = "YOUR_KEYWORDS";
const sourceType = "CSSCI"; // CSSCI|CSCD|SCI|EI|核心
const yearFrom = "2020";
const yearTo = "2025";

// 在高级搜索页执行
document.querySelector('input#txt_1_value1').value = query;
// 设置来源类别筛选...
document.querySelector('#magazine_value1').value = sourceType;
// 提交搜索
document.querySelector('#btn_search')?.click();
```

## 注意事项

### 验证码处理

知网有时会触发腾讯滑块验证码。脚本检测到 `#tcaptcha_transform_dy` 可见时返回 `captcha` 错误，此时需要用户手动在 Chrome 中完成验证。

### 登录要求

- **基本检索**：无需登录
- **PDF 下载**：需在 Chrome 中手动登录知网个人账号（机构订阅或个人账户）
- **CAJ 下载**：需知网研学客户端

### Zotero 导出

使用 cnki-export 推送到 Zotero 时，需 Zotero 桌面端在后台运行，且已安装 Zotero Connector 浏览器扩展。

### 引用准确性

知网导出的引用信息需人工核对：
- 作者名可能缺失或顺序错误
- 英文文献的引用信息可能不全
- 部分文献 DOI 可能缺失

## OfficeCLI + 知网协作模式

```
知网检索 (Chrome DevTools MCP)
    │
    ├── Excel 文献列表 → officecli xlsx（分类、筛选、标注阅读状态）
    │
    ├── Word 参考文献 → officecli docx（GB/T 7714 格式化引用）
    │
    └── PPT 文献汇报 → officecli pptx（文献综述幻灯片）
```

## QA

- 文献标题是否完整无截断
- 引用格式是否符合 GB/T 7714
- Excel 列宽是否足够（防止 `###` 截断）
- 高级检索条件是否正确应用
- PDF 下载链接是否有效
- 中英文文献混合时排序是否合理

## 已知问题

1. **验证码阻断**：知网反爬机制不定期升级，需手动处理
2. **结果页数限制**：知网默认每页 20 条，翻页需调用 cnki-navigate-pages
3. **批量导出限制**：一次性导出大量文献可能被限流
4. **OfficeCLI 路径引用**：含中文的文件路径需用引号包裹

## 版本信息

本技能整理自 [cookjohn/cnki-skills](https://github.com/cookjohn/cnki-skills)，MIT 许可。
