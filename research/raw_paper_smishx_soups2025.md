# SmishX: Explainable SMS Phishing Detection Using LLM-Based Agents

**标题**: Can You Walk Me Through It? Explainable SMS-Phishing Detection Using LLM-Based Agents  
**作者**: Yizhu Wang, Haoyu Zhai, Chenkai Wang, Qingying Hao, Nick A. Cohen, Roopa Foulger, Jonathan A. Handler, Gang Wang  
**机构**: University of Illinois Urbana-Champaign (UIUC)  
**发表**: SOUPS 2025（Symposium on Usable Privacy and Security）  
**GitHub**: https://github.com/yizhu-joy/SmishX  
**论文**: https://www.usenix.org/system/files/soups2025-wang.pdf  
**引用次数**: 9（截至2026年4月）

## 核心贡献

SmishX 是目前**最权威的将 Agentic AI 应用于短信钓鱼检测**的学术论文，发表于 USENIX SOUPS 2025（可用隐私与安全顶级会议）。

**核心创新**：采用 Agentic AI 方法进行短信钓鱼检测，指导 LLM Agent 以类似人类分析师的方式分析短信，同时提供**可解释的检测结果**（不仅告诉用户"这是钓鱼"，还解释"为什么是钓鱼"）。

## 系统架构

SmishX 的 Agent 系统集成了以下能力：

1. **URL 截图捕获**（使用 Puppeteer/Crawlee）：自动访问短信中的 URL 并截图，获取视觉证据
2. **网页内容读取**（使用 Jina Reader API）：提取 URL 目标页面的文本内容
3. **网络搜索验证**（使用 Google Programmable Search Engine）：搜索发送者号码、机构名称等信息进行真实性验证
4. **LLM 综合分析**（使用 OpenAI GPT）：综合所有证据进行最终判断并生成解释

### 检测流程

```
短信输入
  ↓
1. 提取 URL、电话号码、机构名称等实体
  ↓
2. 并行工具调用：
   - URL 截图（视觉证据）
   - 网页内容提取（文本证据）
   - 网络搜索验证（背景信息）
  ↓
3. LLM 综合所有证据进行推理
  ↓
4. 输出：判断结果（钓鱼/合法）+ 详细解释
```

## 数据集

- 手动重新标注了 **1200 条**来自公开数据集的短信（含有效 URL 或无 URL）
- 三分类：**smishing（钓鱼）**、**spam（垃圾）**、**legitimate（合法）**
- 同时标注了每条短信中 URL、邮箱、电话号码的存在情况

## 用户研究发现

论文进行了严格的用户研究，发现：
- SmishX 的可解释性显著提升了用户对检测结果的信任度
- 用户能够理解 AI 的推理过程，并据此做出更好的决策
- 对于老年用户等高风险群体，可解释性尤为重要

## 技术依赖

```python
# 核心 API 依赖
openai_api_key      # GPT 模型（LLM 推理）
jina_api_key        # Jina Reader（网页内容提取）
google_cloud_API_key # Google Cloud（视觉 API）
search_engine_ID    # Google 搜索（背景信息验证）
```

## 对 AgenticX-AiSMS 的启示

SmishX 是 AgenticX-AiSMS 最直接的学术参考，提供了完整的技术蓝图：

1. **Agentic 架构是正确方向**：SmishX 证明了 LLM Agent 在短信安全检测中的有效性，AgenticX 框架天然支持这种 Agentic 架构。

2. **工具调用链**：URL 截图 → 内容提取 → 网络搜索 → LLM 综合分析，这个工具调用链可以直接在 AgenticX 的 Tool 系统中实现。

3. **可解释性是关键**：不仅要给出判断，还要给出解释。这对于运营商向用户展示拦截原因、处理用户申诉至关重要。

4. **三分类体系**：smishing/spam/legitimate 的三分类比二分类更实用，spam 和 smishing 需要不同的处置策略。

5. **多模态证据融合**：文本 + URL 截图 + 网页内容 + 搜索结果的多模态融合，与 ZTE Smart Safeguard 的多模态 LLM 方案不谋而合。

6. **可直接复用**：SmishX 的代码已开源，AgenticX-AiSMS 可以将其作为一个 Agent 工具集成到 AgenticX 框架中。
