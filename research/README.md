# AI 与短信安全结合方向深度调研报告

本目录包含了关于"AI + 短信安全分析"方向的深度调研资料，涵盖了国内外顶尖学术论文和头部厂商的技术解决方案。这些资料为构建 `AgenticX-AiSMS` 项目提供了坚实的理论基础和工程参考。

## 目录结构

### 1. 学术论文研究 (Academic Research)

- [SmishX: Explainable SMS Phishing Detection Using LLM-Based Agents](./raw_paper_smishx_soups2025.md)
  - **来源**：USENIX SOUPS 2025
  - **核心价值**：最权威的将 Agentic AI 应用于短信钓鱼检测的论文，提供了完整的工具调用链（URL截图、网页提取、搜索验证）和可解释性设计。
- [Telecom Fraud Detection Based on Large Language Models: A Multi-Role, Multi-Layer Prompting Strategy](./raw_paper_mrml_telecom_fraud_mdpi2026.md)
  - **来源**：Applied Sciences, 2026
  - **核心价值**：提出了 MRML（多角色多层次）提示策略，通过文本分析、业务流程分析、安全分析三个专家角色协同，显著提升了 LLM 在长尾欺诈类别上的检测稳定性。
- [Advanced Real-Time Fraud Detection Using RAG-Based LLMs](./raw_paper_rag_fraud_detection_arxiv2501.md)
  - **来源**：arXiv, 2025
  - **核心价值**：证明了 RAG 架构在欺诈检测中的有效性，通过检索企业合规政策文档，实现了无需重新训练即可动态更新检测策略的能力。
- [TeleAntiFraud-28k: An Audio-Text Slow-Thinking Dataset for Telecom Fraud Detection](./raw_paper_teleantifraud28k_and_openai_moderation.md)
  - **来源**：ACM MM 2025
  - **核心价值**：首个音频-文本慢思考电信欺诈检测数据集，提供了基于 LLM 增强和多 Agent 对抗合成的数据构建方法论。

### 2. 头部厂商解决方案 (Industry Solutions)

- [Google AI 短信诈骗检测技术方案](./raw_vendor_google_scam_detection.md)
  - **核心技术**：基于 Gemini Nano 的端侧（On-Device）实时检测，强调隐私保护和对话式诈骗（Conversational Scams）的动态演变识别。
- [Meta Llama Guard 3 内容安全分类器](./raw_vendor_meta_llama_guard3.md)
  - **核心技术**：开源的 LLM 内容安全分类基础设施，支持自定义安全类别（如扩展专门的短信诈骗类别），提供 1B 到 11B-Vision 多种规模。
- [阿里云 & 字节跳动火山引擎内容安全方案](./raw_vendor_alibaba_bytedance_content_safety.md)
  - **核心技术**：基于通义大模型/豆包大模型的上下文语义理解、多模态关联分析，以及可定制的"审核智能体"（Moderation Agent）。
- [中兴通讯 Smart Safeguard 多模态 LLM 反欺诈系统](./raw_vendor_zte_smart_safeguard.md)
  - **核心技术**：与中国联通合作，融合文本、音频、图像特征，采用"大小模型协同"架构，实现毫秒级拦截。
- [国内三大运营商与华为 AI 反诈方案](./raw_vendor_china_operators_huawei.md)
  - **核心技术**：华为的通话中实时阻断、中国联通的图算法与时空网络分析、中国移动的多维风险标签体系。

## 核心洞察与 AgenticX-AiSMS 落地建议

综合以上调研，构建 `AgenticX-AiSMS` 项目应遵循以下技术路线：

1. **Agentic 架构是最佳实践**：单一 LLM 调用无法应对复杂的短信欺诈。应采用类似 SmishX 和 MRML 的多 Agent 协同架构，将文本分析、URL 验证、合规检查解耦为不同的 Agent 角色。
2. **RAG 增强知识库**：结合俊雄负责的知识库安全方向，利用 RAG 技术将最新的诈骗话术、黑产特征、合规政策注入 LLM 的上下文中，解决模型幻觉和知识滞后问题。
3. **多模态与工具调用**：短信中的 URL 是高危载体。系统必须具备调用外部工具（如 Puppeteer 截图、OCR 识别、搜索引擎）的能力，实现文本与视觉证据的交叉验证。
4. **可解释性输出**：不仅要输出"是否为诈骗"的二分类结果，更要输出详细的推理链条（Chain of Thought），这对于用户信任和人工复核至关重要。
5. **端云协同/大小模型结合**：参考 Google 和中兴的方案，可以设计轻量级模型（如 Llama Guard 3 1B）进行初步筛查，高危样本再交由云端大模型（AgenticX 复杂工作流）进行深度分析。
