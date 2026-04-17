# Anthropic Claude 内容审核技术方案

**来源**: Anthropic Claude API 官方文档  
**链接**: https://platform.claude.com/docs/en/about-claude/use-case-guides/content-moderation

## 核心定位

Claude 的内容审核能力基于大语言模型的语义理解，相比传统 ML 或规则引擎具有以下优势：

| 优势维度 | 说明 |
|---|---|
| 快速部署 | 无需大量训练数据，直接通过 Prompt 定义审核规则 |
| 语义理解 | 能理解隐喻、上下文、讽刺等复杂语言现象 |
| 策略一致性 | 通过 Prompt 统一策略，避免规则碎片化 |
| 动态更新 | 修改 Prompt 即可更新审核策略，无需重新训练 |
| 可解释性 | 每次判断都可以输出解释原因 |
| 多语言 | 无需为不同语言维护独立模型 |
| 多模态 | 支持文本和图像的联合审核 |

## 技术实现方案

### 1. 基础审核框架（单次 API 调用）

Claude 通过 Prompt 定义不安全类别，返回结构化 JSON：

```python
unsafe_categories = [
    "Child Exploitation",
    "Conspiracy Theories",
    "Hate",
    "Non-Violent Crimes",
    "Privacy",
    "Violent Crimes",
    # 可自定义扩展，如 "SMS Phishing", "Financial Fraud" 等
]

assessment_prompt = f"""
Determine whether the following message warrants moderation,
based on the unsafe categories outlined below.

Message: <message>{message}</message>
Unsafe Categories: <categories>{unsafe_category_str}</categories>

Respond with ONLY a JSON object:
{{
  "violation": <Boolean>,
  "categories": [violated categories],
  "explanation": [optional explanation]
}}"""
```

### 2. 成本估算（以10亿条/月为例）

| 模型 | 月成本估算 |
|---|---|
| Claude Haiku 3（轻量级） | ~$2,590/月 |
| Claude Opus 4.7（旗舰级） | ~$51,800/月 |

对于短信场景（每条约100字符），Haiku 3 是最佳性价比选择。

### 3. 批量处理优化

对于大规模短信审核，推荐使用 Batch Processing API：
- 异步批量提交，降低延迟要求
- 成本可进一步降低 50%

### 4. 分层审核架构（推荐）

```
第一层：规则引擎（黑名单、关键词）→ 快速过滤明显违规
第二层：Claude Haiku（轻量语义分析）→ 处理模糊案例
第三层：Claude Opus（深度推理）→ 处理高风险/复杂案例
```

## Constitutional Classifiers（宪法分类器）

Anthropic 于 2025年2月发布了 Constitutional Classifiers 研究：
- 通过定义"宪法"（规则集）来训练分类器
- 能有效防御通用越狱攻击（Universal Jailbreaks）
- 在保持低误报率的同时大幅提升安全性

**参考**: https://www.anthropic.com/research/constitutional-classifiers

## 对 AgenticX-AiSMS 的启示

Claude 的内容审核方案为 AgenticX-AiSMS 提供了以下参考：

1. **自定义类别体系**：可以将短信安全类别定制化，如"钓鱼诈骗"、"金融欺诈"、"虚假快递"、"冒充客服"等，直接通过 Prompt 注入，无需重新训练模型。

2. **结构化输出**：返回 JSON 格式的判断结果，包含违规类别和解释，便于下游系统处理和人工审核。

3. **分层成本控制**：轻量模型处理大量低风险短信，重量级模型只处理可疑案例，大幅降低整体成本。

4. **可解释性**：每条被拦截的短信都有解释，便于用户申诉和系统优化。
