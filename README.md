# AgenticX-AiSMS：基于多智能体的短信安全与风险识别系统

## 项目背景

随着生成式 AI 技术的普及，电信网络诈骗（特别是短信钓鱼、虚假营销、社会工程学攻击）的文本表达变得越来越隐蔽和个性化。传统的基于规则引擎、黑名单或简单机器学习模型的拦截系统，在面对语义模糊、上下文依赖强的新型诈骗话术时，往往表现出较高的误报率和漏报率。

本项目 **AgenticX-AiSMS** 旨在探索 **AI 与短信业务的深度结合**，重点聚焦**安全领域**。基于强大的企业级多智能体框架 [AgenticX](https://github.com/DemonDamon/AgenticX)，我们构建了一个从脱敏、意图识别到知识库比对的完整短信安全防线，利用大语言模型（LLM）对短信内容进行深度的语义分析与风险识别。

## 核心调研成果

为了确保项目的技术前瞻性和落地可行性，我们对近一年（2024-2026）国内外顶尖学术论文和头部厂商（Google、Meta、阿里、字节、华为等）的解决方案进行了深度调研。详细的原始调研资料已落盘至 [`research/`](./research/) 目录。

### 1. 学术界前沿探索

学术界的研究表明，单一的 LLM 调用在处理多样化、长尾的欺诈文本时表现不稳定。当前的最优解是**多智能体协同（Multi-Agent Collaboration）**与**检索增强生成（RAG）**的结合。

*   **可解释的 Agentic 架构**：USENIX SOUPS 2025 的顶级论文《SmishX: Explainable SMS Phishing Detection Using LLM-Based Agents》证明了 Agentic AI 在短信钓鱼检测中的有效性。该系统通过并行调用 URL 截图、网页内容提取和网络搜索验证等工具，指导 LLM Agent 以类似人类分析师的方式综合多模态证据，不仅输出判定结果，还提供详细的解释，显著提升了用户的信任度 [1]。
*   **多角色多层次提示策略（MRML）**：Applied Sciences 2026 年发表的研究提出，通过构建文本分析、业务流程分析和安全分析三个专家角色进行协同推理，并采用条件触发的分层机制（先快速二分筛查，再深度多类分类），能够有效克服 LLM 在长尾欺诈类别上的不稳定性，在精确率和召回率上全面超越 BERT 等深度学习基线 [2]。
*   **基于 RAG 的动态策略更新**：arXiv 2025 年的论文展示了 RAG 架构在实时欺诈检测中的潜力。通过将企业合规政策、历史诈骗案例构建为向量知识库，系统可以在不重新训练模型的情况下，通过检索相似案例作为上下文，动态适应新型诈骗手法，准确率高达 97.98% [3]。

### 2. 工业界头部厂商方案

工业界的解决方案更侧重于**端云协同**、**多模态融合**以及**大规模高并发**的处理能力。

*   **Google 的端侧实时检测**：Google 在 2025 年 3 月为 Android 推出了基于 Gemini Nano 的端侧短信诈骗检测功能。该方案强调在保护用户隐私的前提下，对对话式诈骗（Conversational Scams）进行实时模式识别，即使在初始消息接收后，也能在多轮对话中动态预警 [4]。
*   **Meta 的开源安全分类器**：Meta 发布的 Llama Guard 3 系列模型（包含 1B、8B 和 11B-Vision）为内容安全提供了强大的开源基础设施。其基于 Prompt 的自定义分类体系，允许开发者针对金融诈骗、网络犯罪等特定场景扩展专属类别，支持文本与图像的联合评估 [5]。
*   **国内厂商的大模型赋能**：阿里云基于通义大模型推出了"审核智能体"，支持上下文语义理解和暗喻对抗识别；字节跳动火山引擎则利用其在短视频平台积累的海量样本，提供 AIGC 专属的内容风险识别。华为与国内三大运营商合作，推出了基于多模态大模型的通话中实时反诈方案，实现了从"事后追损"到"事前预防"的跨越 [6] [7]。
*   **5G 消息与 AI Agent 的融合（如 OpenClaw）**：业界（如蜂动科技）已开始探索将 5G 消息（RCS）作为开源 AI Agent 框架（如 OpenClaw）的触达通道。这种方案不仅实现了"零门槛"的个人 AI 助手，更展示了短信平台与 AI Agent 打通在主动营销、富媒体交互、动态用户画像生成及 CRM 系统对接方面的巨大潜力 [8]。

## 系统架构设计

基于上述调研，AgenticX-AiSMS 的架构设计充分利用了 AgenticX 框架的优势，并与内部的知识库安全能力深度融合。

### 核心模块

1.  **预处理与脱敏层 (Preprocessing & Anonymization)**
    *   利用轻量级模型（如 Llama Guard 3 1B）或规则引擎进行快速的二分筛查，过滤掉明显的正常短信。
    *   对进入深度分析的短信进行 PII（个人身份信息）脱敏，提取 URL、电话号码、机构名称等关键实体。
2.  **多智能体协同分析层 (Multi-Agent Analysis)**
    *   **文本语义 Agent**：分析词汇选择、句式结构、情感倾向（如制造紧迫感、诱导点击）。
    *   **工具调用 Agent**：针对提取的 URL，调用无头浏览器进行截图和内容抓取；针对电话号码，调用搜索引擎或信誉库进行背景验证。
    *   **合规与逻辑 Agent**：判断短信请求是否符合正常的业务流程。
3.  **知识库检索增强层 (Knowledge Retrieval)**
    *   接入现有的**知识库安全模块**（基于 AgenticX 的 GraphRAG/Hybrid Retriever）。
    *   将历史已确认的诈骗短信、黑产话术特征、运营商合规政策构建为向量知识库。
    *   为 LLM 提供高度相关的上下文，减少幻觉，提高判断准确率。
4.  **决策融合与解释层 (Decision & Explanation)**
    *   综合各 Agent 的分析结果和知识库检索内容，输出最终的风险等级（安全、可疑、高危）。
    *   生成结构化的、可解释的判定依据，便于用户理解和人工复核。

## 落地实施建议

1.  **复用 AgenticX 安全组件**：直接复用 AgenticX 底层的 `safety` 模块（如 `leak_detector.py`, `policy.py`），结合短信业务场景快速构建注入检测和敏感信息泄漏检测策略。
2.  **改造意图识别示例**：参考 AgenticX 官方的 `agenticx-for-intent-recognition` 示例，将其分层 Agent 设计和混合实体提取机制改造为"短信风险意图识别"流水线。
3.  **知识库无缝对接**：与负责知识库安全的团队（俊雄）紧密合作，将历史诈骗案例库作为 RAG 的数据源，实现"无需重新训练即可动态更新"的防御能力。
4.  **拓展营销与服务场景（结合 OpenClaw/彩讯平台）**：
    *   **打通通道**：探索将 AgenticX 的能力通过 API 接入现有的短信平台（如彩讯 Claw），或直接利用 5G 消息（RCS）作为触达通道。
    *   **动态用户画像**：在移动套餐变更、续费等营销场景中，利用 Agent 分析用户的上行回复（如"太贵了"、"不需要"）或富媒体卡片的点击行为，动态生成或更新用户画像（如"价格敏感型"）。
    *   **CRM 闭环**：将更新后的用户画像自动同步到企业的 CRM 系统中，为下一次精准营销提供数据支持，实现从"安全防御"到"主动营销与服务"的价值延伸。

## 参考文献

[1] Wang, Y., et al. (2025). Can You Walk Me Through It? Explainable SMS-Phishing Detection Using LLM-Based Agents. *Proceedings of the 2025 Symposium on Usable Privacy and Security (SOUPS)*. USENIX Association. https://www.usenix.org/system/files/soups2025-wang.pdf
[2] Ding, J., & Zhou, H. (2026). Telecom Fraud Detection Based on Large Language Models: A Multi-Role, Multi-Layer Prompting Strategy. *Applied Sciences*, 16(1), 544. https://doi.org/10.3390/app16010544
[3] Singh, G., et al. (2025). Advanced Real-Time Fraud Detection Using RAG-Based LLMs. *arXiv preprint arXiv:2501.15290*. https://arxiv.org/abs/2501.15290
[4] Google Security Blog. (2025). New AI-Powered Scam Detection Features to Help Protect You on Android. https://security.googleblog.com/2025/03/new-ai-powered-scam-detection-features.html
[5] Meta Llama. (2024). Llama Guard 3 | Model Cards and Prompt formats. https://www.llama.com/docs/model-cards-and-prompt-formats/llama-guard-3/
[6] 阿里云. (2026). 内容安全的产品优势. https://help.aliyun.com/zh/document_detail/28419.html
[7] C114通信网. (2025). 华为AI通话反诈方案帮助运营商实现通话中电信诈骗实时防控. https://m.c114.com.cn/w126-1287553.html
[8] 蜂动科技. (2026). 业界首发：5G消息 + OpenClaw，个人AI助手进入"零门槛"时代. https://mp.weixin.qq.com/s/sUEffDhOFQmoNLgZ4dj6zQ
