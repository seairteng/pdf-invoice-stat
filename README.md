# PDF 发票统计系统

> 基于 `pdfplumber` + `PaddleOCR` + `PP-StructureV3` 的本地 PDF 发票提取方案  
> 支持增值税电子普票、火车票、通行费等多场景，输出格式化 Excel

[![Latest](https://img.shields.io/badge/version-v2.4.1-blue)](v2.4.1/)
[![License](https://img.shields.io/badge/license-MIT-green)](#许可证)
[![Python](https://img.shields.io/badge/python-3.9%2B-blue)](https://www.python.org)

## 📌 简介

`pdf-invoice-stat` 是一个本地运行的 PDF 发票批量提取工具，把多张发票（增值税电子普票、铁路电子客票、通行费、过路费等）从 PDF 文本或扫描件中结构化抽取，导出到 Excel 表格。

**核心场景**：会计/财务/审计人员拿到一个合并的发票 PDF，需要快速统计金额、税额、销方信息。

## ✨ 特性

| 类别 | 能力 |
|---|---|
| **票据类型** | 增值税电子普票、铁路电子客票（火车票）、通行费、餐饮、过路费 |
| **提取字段** | 发票号、日期、购买方/销售方（名称 + 税号）、项目名称、金额、税率、税额、价税合计 |
| **输出格式** | Excel（15 列，冻结首行，会计数字格式）|
| **本地运行** | 纯 Python + pdfplumber，无外部 API 调用，数据不外传 |
| **鲁棒性** | 通行费跨行 bug 修复、水印页兜底、多税率自动留空 |
| **OCR 兜底** | pdfplumber 提取失败时自动走 PaddleOCR / PP-StructureV3 |

## 📦 安装

### 方式一：通过 ClawHub（推荐）

```bash
clawhub install seairteng/pdf-invoice-stat
```

### 方式二：直接使用源码

```bash
git clone https://github.com/seairteng/pdf-invoice-stat.git
cd pdf-invoice-stat/v2.4.1
pip install pdfplumber paddleocr paddlex
```

依赖：
- Python 3.9+
- `pdfplumber`（文本提取）
- `paddleocr >= 2.7`（扫描件 OCR 兜底）
- `paddlex[ocr] >= 3.7`（PP-StructureV3 复杂版面）
- `openpyxl`（Excel 导出）

## 🚀 使用

### 命令行

```bash
python3 scripts/invoice_extractor.py <PDF路径> [输出Excel路径]
```

- `<PDF路径>`：必填，合并 PDF 文件路径
- `[输出Excel路径]`：选填，默认在 PDF 同目录生成 `<原文件名>_发票统计.xlsx`

### 示例

```bash
# 示例 1：指定输出路径
python3 scripts/invoice_extractor.py ~/Downloads/invoices_2026Q3.pdf ~/Desktop/发票汇总.xlsx

# 示例 2：默认输出（同目录）
python3 scripts/invoice_extractor.py ~/Downloads/invoices_2026Q3.pdf
```

### 作为 Python 模块

```python
from invoice_extractor import extract_invoices
data = extract_invoices("invoices.pdf", "output.xlsx")
print(f"提取了 {len(data)} 条发票记录")
```

## 📊 输出字段（15 列）

| 列 | 说明 | 示例 |
|---|---|---|
| 序号 | 自动编号 | 1, 2, 3... |
| 发票号码 | 8 位数字 | 25117000000123456 |
| 开票日期 | YYYY-MM-DD | 2026-08-15 |
| 购买方信息-名称 | 公司全称 | 上海某某有限公司 |
| 购买方信息-统一社会信用代码 | 18 位 | 91310000123456789X |
| 销售方信息-名称 | 公司全称 | 北京某某科技公司 |
| 销售方信息-统一社会信用代码 | 18 位 | 91110108123456789Y |
| 项目名称 | 商品/服务 | *餐饮服务*餐费 |
| 金额（不含税）| 元，2 位小数 | 1000.00 |
| 税率/征收率 | 1%/3%/6%/9%/13% 或 免税 | 6% |
| 税额 | 元，2 位小数 | 60.00 |
| 价税合计 | 元，2 位小数 | 1060.00 |
| 发票来源 | 票据类型 | 增值税电子普票 / 火车票 / 通行费 |
| 备注 | 火车票起终点等 | 徐州东站-上海虹桥站 G1234 |
| 是否验证 | 提取是否成功 | 是/否 |

## 📜 版本历史

| 版本 | 日期 | 主要变化 |
|---|---|---|
| **v2.4.1** | 2026-08-17 | ClawHub 显示名修正（→「发票提取统计系统」）|
| v2.4.0 | 2026-08-13 | 火车票规则重构（购方/税号抽取 + 9%/6% 税率 + 站点补全）|
| v2.3.0 | 2026-08-13 | PP-StructureV3 集成（处理纯图片发票 + 复杂版面）|
| v2.2.0 | 2026-08-13 | PaddleOCR 集成（扫面件兜底）|
| v2.1.0 | 2026-08-13 | pdfplumber 布局感知（按 top 分组 + 邻近组合并）|
| v2.0.0 | 2026-08-12 | 通行费税额 bug 修复 + 命令行参数化 |

## 🛠 故障排查

| 症状 | 原因 | 解决 |
|---|---|---|
| 通行费税额=0 | 旧版本 bug | 升级到 v2.0.0+ |
| 水印遮挡字段为空 | 字体叠加识别失败 | 启用 PaddleOCR 兜底路径 |
| 多税率发票只有一行 | 旧版本会重复行 | v2.0.0+ 自动留空税率列 |
| 火车票购方信息缺失 | 票面格式不规范 | v2.4.0+ 走 fallback OCR 路径 |
| OCR 报 `paddlex not found` | 未装 PP-StructureV3 | `pip install paddlex[ocr]` |

## 🔗 相关链接

- **ClawHub 主页**：https://clawhub.ai/seairteng/pdf-invoice-stat
- **作者主页**：https://clawhub.ai/seairteng
- **问题反馈**：GitHub Issues

## 📄 许可证

MIT License — 详见 [LICENSE](v2.4.1/LICENSE) 文件

## 👤 作者

- **Kent Wang** — 上海 · 中传文创 / idunique consultant
- 邮箱：18516101620@163.com

---

> 💡 **Tip**：仓库保留了所有历史版本（v2.0.0 ~ v2.4.1），方便对比演进和回溯问题。
