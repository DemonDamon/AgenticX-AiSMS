# Telecom Fraud Detection Based on Large Language Models: A Multi-Role, Multi-Layer Prompting Strategy

**来源**: Applied Sciences (MDPI), 2026, 16(1), 544  
**作者**: Jianpeng Ding, Houpan Zhou  
**机构**: School of Automation, Hangzhou Dianzi University, Hangzhou 310018, China  
**DOI**: https://doi.org/10.3390/app16010544  
**发表时间**: 2026年1月5日  
**论文链接**: https://www.mdpi.com/2076-3417/16/1/544

## Abstract（摘要）

Telecom network fraud continues to evolve, and its textual expressions have become increasingly concealed, making automated detection more challenging. When combined with mainstream prompting strategies, large language models (LLMs) often exhibit unstable performance when handling diverse fraud texts, particularly for long-tail categories and confusing cases where consistent detection is difficult to maintain.

This study proposes a **Multi-Role, Multi-Layer (MRML) prompting strategy**. The strategy constructs three expert roles—text analysis, business process analysis, and security analysis—and adopts a conditional hierarchical reasoning mechanism to achieve a structured detection process that transitions from rapid binary screening to deep multi-class classification.

Experiments conducted on two public datasets show that the proposed framework significantly outperforms mainstream prompting strategies and surpasses deep learning baselines such as BERT, TextCNN, and Transformer in terms of precision, recall, and F1-score.

## 核心创新点：MRML 提示策略

### 三个专家角色（Multi-Role）
1. **文本分析专家角色**: 负责低层次的文本特征感知（词汇、语法、语义特征）
2. **业务流程分析专家角色**: 负责分析短信中涉及的业务逻辑（如转账、快递、贷款等）
3. **安全分析专家角色**: 负责高层次的安全语义判断（是否为欺诈行为）

### 多层次推理（Multi-Layer）
- **第一层（快速二分类筛选）**: 快速判断是否可疑，过滤明显安全的短信
- **第二层（深度多分类）**: 仅对可疑短信进行深度分析，判断具体欺诈类型（钓鱼、诈骗、垃圾营销等）
- **条件触发机制**: 只有第一层判定为可疑时，才触发第二层深度分析，提升计算效率

## 技术架构

```
输入短信文本
    ↓
[第一层：快速二分类筛选]
    ├── 安全 → 直接输出"安全"
    └── 可疑 → 进入第二层
         ↓
[第二层：三角色并行分析]
    ├── 文本分析角色 → 文本特征
    ├── 业务流程角色 → 业务逻辑
    └── 安全分析角色 → 安全判断
         ↓
[决策融合] → 欺诈类型分类 + 置信度
```

## 实验结果

- 在两个公开数据集上均显著优于主流提示策略（Zero-shot, Few-shot, CoT）
- 超越 BERT、TextCNN、Transformer 等深度学习基线
- 在 Precision、Recall、F1-score 上均表现优越
- 对长尾类别（少见欺诈类型）和混淆案例的处理能力显著提升

## 对 AgenticX-AiSMS 的启示

1. **MRML 策略可直接映射到 AgenticX 的 Multi-Agent 架构**:
   - 文本分析 Agent → 负责 NLP 特征提取
   - 业务流程 Agent → 负责短信业务场景理解
   - 安全分析 Agent → 负责最终风险判断
   
2. **条件触发机制** 可通过 AgenticX 的 Orchestration Engine 中的条件路由（Conditional Routing）实现，避免对所有短信进行深度分析，节省计算成本

3. **可解释性** 通过多角色的推理过程自然产生，每个角色的分析结果都可作为最终判断的依据

## 关键词
telecom fraud, LLM, prompt engineering, fraud detection
