# Can You Walk Me Through It? Explainable SMS Phishing Detection using LLM-based Agents

**来源**: USENIX SOUPS 2025 (Twenty-First Symposium on Usable Privacy and Security)  
**作者**: Yizhu Wang, Haoyu Zhai, Chenkai Wang, Qingying Hao (University of Illinois Urbana-Champaign); Nick A. Cohen, Roopa Foulger, Jonathan A. Handler (OSF Healthcare); Gang Wang (UIUC)  
**论文链接**: https://www.usenix.org/system/files/soups2025-wang.pdf  
**会议时间**: August 11-12, 2025, Seattle, WA, USA

## Abstract（摘要）

SMS phishing poses a significant threat to users, especially older adults. Existing defenses mainly focus on phishing detection, but often cannot explain *why* the SMS is malicious to lay users. In this paper, we use large language models (LLMs) to detect SMS phishing while generating evidence-based explanations. The key challenge is that SMS is short, lacking the necessary context for security reasoning. We develop a prototype called **SmishX** which gathers external contexts (e.g., domain and brand information, URL redirection, and web screenshots) to augment the chain-of-thought (CoT) reasoning of LLMs. Then, the reasoning process is converted into a short explanation message to help users with their decision-making.

Evaluation using real-world SMS datasets shows SmishX can achieve an overall accuracy of **98.8%**, outperforming existing methods. Through user studies (N = 175), we show that SmishX's explanation can significantly improve users' phishing detection efficacy across age groups. Its usability is rated "excellent" by participants (SUS score 82.6). We conclude by discussing open challenges in resolving human-AI disagreements and safely handling AI errors.

## 核心创新点

1. **SmishX 系统设计**: 基于 Agentic AI 架构，通过外部上下文增强（域名信息、品牌信息、URL 重定向、网页截图）来弥补短信内容过短的不足。
2. **Chain-of-Thought (CoT) 推理**: 利用 LLM 的链式思维推理，不仅给出判断结果，还生成可解释的证据性说明。
3. **可解释性**: 将推理过程转化为用户可理解的短解释，帮助用户（尤其是老年用户）做出判断。
4. **高准确率**: 整体准确率 98.8%，超越现有方法。
5. **用户研究**: 175 名参与者的用户研究证明 SmishX 的解释显著提升了各年龄段用户的钓鱼检测能力。

## 技术架构

- **输入**: 原始 SMS 短信文本
- **外部上下文增强**: 
  - 域名和品牌信息查询
  - URL 重定向追踪
  - 网页截图分析
- **LLM 推理**: CoT（链式思维）推理
- **输出**: 安全/钓鱼判断 + 可解释的证据性说明

## 对 AgenticX-AiSMS 的启示

- 可以参考 SmishX 的"外部上下文增强"思路，在 AgenticX 框架中设计"情报检索 Agent"，负责查询 URL 黑名单、域名 WHOIS 信息等
- CoT 推理模式可以通过 AgenticX 的 Orchestration Engine 实现多步骤推理工作流
- 可解释性输出对于企业级短信安全产品至关重要，用户需要知道"为什么"这条短信被判定为风险
