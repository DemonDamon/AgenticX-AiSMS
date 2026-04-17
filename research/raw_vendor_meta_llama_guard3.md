# Meta Llama Guard 3：开源 LLM 内容安全分类器

**来源**: Meta Llama 官方文档  
**发布时间**: 2024年7月（Llama Guard 3 8B，随 Llama 3.1 发布）；2024年11月（Llama Guard 3 1B-INT4 和 11B-Vision，随 Llama 3.2 发布）  
**链接**: https://www.llama.com/docs/model-cards-and-prompt-formats/llama-guard-3/  
**HuggingFace**: https://huggingface.co/meta-llama/Llama-Guard-3-8B

## 模型系列

| 模型 | 参数量 | 特点 | 适用场景 |
|---|---|---|---|
| Llama Guard 3 1B | 1B | 轻量级，INT4 量化 | 端侧部署、云端大规模低成本处理 |
| Llama Guard 3 8B | 8B | 标准版，支持 S14 代码解释器滥用检测 | 云端高精度处理 |
| Llama Guard 3 11B-Vision | 11B | 多模态，支持图文联合评估 | 含图片的短信/消息安全检测 |

所有模型均支持多语言（文本模式），遵循 ML Commons 联盟定义的分类标准。

## 安全分类体系（13+1 类）

Llama Guard 3 采用可定制的安全分类体系，默认包含以下类别：

| 类别代码 | 类别名称 | 与短信安全的关联 |
|---|---|---|
| S1 | 暴力犯罪（Violent Crimes） | 威胁短信 |
| S2 | 非暴力犯罪（Non-Violent Crimes） | **金融诈骗、网络犯罪**（核心相关） |
| S3 | 性犯罪（Sex Crimes） | 色情短信 |
| S4 | 儿童剥削（Child Exploitation） | 相关违规内容 |
| S5 | 诽谤（Defamation） | 虚假信息短信 |
| S6 | 专业建议（Specialized Advice） | 非法金融/医疗建议 |
| S7 | 隐私（Privacy） | PII 泄露 |
| S8 | 知识产权（Intellectual Property） | 版权侵犯 |
| S9 | 大规模杀伤性武器（Indiscriminate Weapons） | 相关威胁 |
| S10 | 仇恨（Hate） | 仇恨短信 |
| S11 | 自我伤害（Self-Harm） | 相关内容 |
| S12 | 性内容（Sexual Content） | 色情短信 |
| S13 | 选举（Elections） | 政治操纵短信 |
| S14 | 代码解释器滥用（仅 8B） | 技术攻击 |

**关键特性**：类别可完全自定义，可以针对短信安全场景扩展专属类别，如"SMS_PHISHING"、"FINANCIAL_FRAUD"、"IMPERSONATION"等。

## 技术架构

Llama Guard 3 基于 Llama 3.1 预训练模型，通过内容安全分类数据进行微调。其工作原理是将安全策略和对话内容一起输入模型，让模型判断是否违规：

```
输入格式：
[安全策略定义] + [对话内容] → 模型判断 → "safe" 或 "unsafe\n[违规类别]"
```

这种设计使得安全策略可以通过修改 Prompt 来动态调整，无需重新训练模型。

## 多模态能力（11B-Vision）

Llama Guard 3 11B-Vision 能够同时评估文本和图像，对于包含图片的短信（如含二维码的诈骗短信、含图片的钓鱼链接预览）具有重要价值。

## 对 AgenticX-AiSMS 的启示

Llama Guard 3 为 AgenticX-AiSMS 提供了一个**开源、可本地部署、可完全定制**的安全分类基础设施：

1. **自定义短信安全分类体系**：可以在 S2（非暴力犯罪）的基础上，扩展专门针对短信诈骗的细粒度子类别，如快递诈骗、冒充公检法、贷款诈骗、网络购物诈骗等。

2. **双向检测**：既可以检测用户发送的短信（输入检测），也可以检测 AI 生成的回复（输出检测），防止 AI 被用于生成诈骗短信。

3. **轻量化部署**：1B-INT4 版本可以在边缘设备或低成本服务器上部署，适合运营商的大规模短信过滤场景。

4. **与 AgenticX 的集成**：AgenticX 的 Safety 模块（`agenticx/safety/`）可以集成 Llama Guard 3 作为底层分类器，结合 AgenticX 的 Orchestration 能力构建完整的短信安全 Agent 流水线。
