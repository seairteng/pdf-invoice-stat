# Changelog

## 2.2.0 (2026-08-13)

### Added
- **PaddleOCR 3.7 集成**（针对纯图片发票场景）
  - 新增 `paddle_ocr.py` 适配器（单例引擎）
  - 中文精准度 95%+（PP-OCRv6_medium 模型）
  - 火车票 OCR 置信度 0.988（实测）
  - 智能 fallback：pdfplumber 提取为空时自动用 PaddleOCR
- **火车票字段抽取升级**
  - 起点/终点/车次/席别/车厢座位/身份证/姓名 等
  - 通过 PaddleOCR OCR 结果抽取字段

### Changed
- **依赖新增**：paddlepaddle>=3.0 + paddleocr>=3.0（首次需下载 ~180MB 模型）
- 依赖说明更新：标注 PaddleOCR 为可选依赖

### Impact
- 火车票字段精准度大幅提升
- 支持纯图片 PDF 场景（扫描件、拍照）
- 兼容旧 PDF（pdfplumber 优先 + PaddleOCR 兜底）

## 2.1.0 (2026-08-13)
- 火车票自动识别（按 SKILL.md 规则填字段）
- 备注自动填 "票面价 ¥XX.XX"

## 2.0.0 (2026-08-11)
- 修复通行费税额缺失 bug
- 布局感知
- 康熙字典 `**` 修复
- 水印页兜底
- 多税率留空

## 1.0.1
- 增值税发票 + 火车票混合支持
