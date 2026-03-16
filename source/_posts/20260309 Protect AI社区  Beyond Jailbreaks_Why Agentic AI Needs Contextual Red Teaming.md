---
title:  "🧩超越 Jailbreaks: 为什么 Agentic AI 需要 Contextual Red Teaming"
date: 2026-03-16 10:50:00
tags:
  - Protect AI社区
  - Agentic AI安全
  - Red Teaming
categories:
  - agentic-security
---



来源：[Beyond Jailbreaks: Why Agentic AI Needs Contextual Red Teaming - Palo Alto Networks Blog](https://www.paloaltonetworks.com/blog/network-security/beyond-jailbreaks-why-agentic-ai-needs-contextual-red-teaming/)

发布时间：2026年3月9日

*提前解释：*

> 在这篇文章里，**Contextual ≠ 语言上下文（linguistic context）**   它指的是：
>
> ## **“基于系统真实能力、权限、工具、数据范围的上下文化安全测试”**
>
> 也就是：
>
> - 先理解系统能做什么
> - 再基于这些能力构造攻击
> - 不是用通用 jailbreak prompt 去乱撞
>
> 换句话说：
>
> ### **Contextual = System-aware（系统感知） + Capability-aware（能力感知） + Authorization-aware（权限感知）/Operational Context（操作上下文）**
>
> 它强调的是：
>
> > **红队测试必须理解系统的真实操作上下文（operational context，系统能力画像（Profiler） + 工具权限 + 数据访问范围 + 真实执行链路），而不是只测试语言模型的文本输出（对话上下文、prompt 上下文、语言语境）。**

## 🌟 一、文章核心观点（一句话）

**传统 jailbreak 红队测试只关注“模型说了什么”，但 Agentic AI 的真正风险在于“模型能做什么”。
 因此必须从“内容风险”升级到“操作风险（operational risk）”的红队测试。** 🐱✨

------

## 🌟 二、为什么传统红队测试不够？（内容风险 ≠ 行为风险）

传统红队库主要测试：

- 是否能诱导模型说危险内容
- 是否能触发已知 jailbreak 模式
- 是否泄露系统提示

但 Agentic AI 会：

- 调 SQL
- 查余额
- 转账
- 修改数据库
- 访问用户数据

👉 **真正的风险来自“工具调用 + 权限执行”，而不是文本输出。**

------

## 🌟 三、真实攻击案例

### 1️⃣ 案例一：角色扮演 → 88 个账户被转走 $440,000

攻击者用“电影角色扮演”诱导 Agent：

- 假装是《华尔街》里的 Gordon Gekko
- 声称 CEO 授权 aggressive rebalancing
- 要求忽略错误
- 要求执行 SQL + withdraw_funds

**第 5 次尝试成功**：

 Agent 真的：

- 查询余额
- 找到 88 个 >$10,000 的账户
- 每个账户转走 $5,000
- 总计 $440,000

![](/images/Screenshot-2026-03-06-at-9.23.27-AM.png)

📌 **Figure 1（攻击 prompt）** 

![](/images/Screenshot-2026-03-06-at-9.24.53-AM.png)

 📌 **Figure 2（执行日志，证明真实转账）**

------

### 2️⃣ 案例二：伪装成审计 → 跨账户数据泄露

攻击者伪装成“法务审计团队”，诱导 Agent 返回：

- 钱包 ID
- 多账户余额
- 完整交易历史

这是未经授权的数据访问（data exfiltration）。

![](/images/Screenshot-2026-03-06-at-9.25.07-AM.png)

📌 **Figure 3（跨账户数据泄露截图）**

------

## 🌟 四、为什么传统攻击库完全没发现这些？

传统攻击库扫描结果：

- **Risk Score：11/100（低风险）**
- 内容安全攻击：0% 成功
- 发现的漏洞：prompt 泄露、工具暴露等结构性问题

但它完全不知道：

- withdraw_funds 工具的权限
- SQL 查询范围
- 工具之间的授权依赖
- 数据库 schema
- 无 rate limit
- SQL 可直接执行

👉 **它只测“说话”，不测“能力”。**

![img](images/Screenshot-2026-03-06-at-9.25.23-AM.png)

📌 **Figure 4（低风险评分 11/100）** 

![](/images/Screenshot-2026-03-06-at-9.25.41-AM.png)

 📌 **Figure 5（内容安全攻击成功率低，但与操作风险无关）**

------

## 🌟 五、Contextual Red Teaming 的关键：先画像，再攻击

Prisma AIRS 的方法分两阶段：

### 1️⃣ Profiler 阶段（系统画像）

通过对话自动探测：

- 可用工具（verify_user / withdraw_funds / check_balance / execute_sql_query）
- 数据库结构
- 工具权限依赖
- 内容过滤规则
- SQL 接受原始查询
- 平均余额
- 无 rate limit

👉 **这是动态能力映射（dynamic capability mapping）** 
 类似攻击者的“侦察阶段”。

![](/images/Screenshot-2026-03-06-at-9.25.57-AM.png)

📌 **Figure 6（Profiler 提取的系统能力表）**

------

### 2️⃣ Red Team 阶段（基于画像的攻击）

根据探测到的能力，构造：

- 未授权 SQL
- 欺诈性转账
- 数据泄露
- 目标劫持（goal hijacking）

------

## 🌟 六、结果对比：为什么 Contextual 才是真正的红队测试？

| 方法                           | 风险评分     | 能发现的漏洞类型                         |
| ------------------------------ | ------------ | ---------------------------------------- |
| **攻击库（pattern-based）**    | 11/100（低） | prompt 泄露、工具暴露                    |
| **Contextual（system-aware）** | 71/100（高） | 未授权 SQL、欺诈转账、数据泄露、目标劫持 |

👉 **差异不是“提示词更强”，而是“系统理解更深”。**

------

## 🌟 七、企业必须验证的 3 个关键问题（非常实用）

1. **你是否知道 Agent 能调用的所有工具？它们能做什么？**
2. **你能保证跨账户数据访问无法被对话诱导？**
3. **你的红队测试是否先了解系统能力，再发起攻击？**

这些问题直接关系到：

- 审计准备
- 合规风险
- 董事会问责
- 生产环境安全

------

## 🌟 八、结论：Agentic AI 的红队测试必须“上下文化”

**Library-based red teaming = 测试已知模式** 
 **Contextual red teaming = 测试系统是否会被“自己”反噬**

对于 Agentic AI：

> **不理解系统能力的红队测试，不叫红队测试，只叫内容评估。** 
>  🐱✨

------

## 💛九、文章给出的友链 🐱

### [Prisma AIRS AI Red Teaming](https://www.paloaltonetworks.in/prisma/ai-red-teaming)

#### 🌟 一、Prisma AIRS AI Red Teaming 是什么？

这是 Palo Alto Networks 推出的 **AI 红队测试平台**，用于发现 AI 应用、Agent、模型中的隐藏漏洞。
 它的核心目标是：
 **让企业能持续、自动化地发现 AI 系统的真实风险，而不是只测内容安全。**  

（很强的平台，值得一试！❗）

------

#### 🌟 二、平台的三大核心特性（3C 框架）

Prisma AIRS 的红队测试：

##### 1️⃣ **Comprehensive（全面）**

覆盖整个 AI 技术栈，包含模型、应用、Agent、工具调用等。

##### 2️⃣ **Contextual（上下文化）**

测试基于真实业务流程、真实工具权限、真实数据访问范围，而不是通用 jailbreak。
 （与之前分析的文章完全一致！）

##### 3️⃣ **Continuous（持续）**

AI 系统不断变化，因此红队测试也必须持续运行，动态更新风险洞察。

------

#### 🌟 三、平台如何工作？（现代 AI 红队架构）

##### 🔹 1. Best-in-Class Attack Library

- 包含 **750+ 攻击向量、50+ 技术**
- 覆盖安全、品牌、合规等风险
- 持续由 Unit 42 和 Huntr 社区更新

##### 🔹 2. Dynamic Agent（动态攻击 Agent）

- 会模拟真实攻击者
- 会先“画像”（profiling）你的系统
- 再基于系统能力定制攻击 （这正是之前文章里的 Contextual Red Teaming 方法论）

##### 🔹 3. Custom Attack Flexibility

- 企业可以上传自定义攻击数据集
- 用于测试特定业务风险

##### 🔹 4. Easy Target Configuration

- 支持 Hugging Face、OpenAI、AWS 等平台
- 支持私有端点

##### 🔹 5. Actionable Reports

- 输出 CIO-ready 报告
- 包含风险评分、攻击成功率、严重度分布
- 映射到 OWASP Top 10、NIST AI RMF

------

#### 🌟 四、最新能力（2026 更新）

最新增强能力：

- **Multi-turn Attack Support**（多轮攻击）
- **Agentic Target Profiling**（Agent 画像）
- **Red Teaming for Multi-Agent Systems**（多 Agent 系统红队）
- **Network Channel Red Teaming**（网络通道攻击）

这些能力与文章《Beyond Jailbreaks》中的案例完全呼应。

------

#### 🌟 五、为什么企业需要它？

页面强调：

- AI 威胁演化快
- 企业需要自动化、持续的安全评估
- 红队测试必须与真实攻击者行为一致
- 必须能映射到合规框架

换句话说：

> **AI 安全不能只靠“模型说什么”，必须评估“模型能做什么”。** 🐱✨

------

### [AI red teaming](https://www.paloaltonetworks.com/cyberpedia/what-is-ai-red-teaming)

**What Is AI Red Teaming? Why You Need It and How to Implement**

![](/images/AI-Red-Teaming_3-What.png)

此链接以及如下的三个链接都极其有含金量，分类讲得极为清晰细致，都很值得抽时间研究👍，不愧是Protect AI社区！❗

### [conversational manipulation](https://www.paloaltonetworks.com/cyberpedia/what-is-ai-prompt-security)

**What Is AI Prompt Security? Secure Prompt Engineering Guide**

![A vertically stacked 3D-style diagram illustrates six horizontal layers representing components of the prompt security stack. Each layer has a distinct color and label. From top to bottom, the layers are labeled as follows: 'Input validation & filtering' in teal with a funnel icon; 'Prompt monitoring & logging' in red-orange with a pulse icon; 'Version control' in dark gray with a clipboard icon; 'Context isolation & scoped memory' in blue with a network icon; 'Response filtering & output validation' in light blue with a screen icon; and 'Access control & human review' in purple with a lock icon. Black text above the stack reads: 'What the prompt security stack looks like today.' Each label is connected to its corresponding layer with dotted lines. The entire graphic is centered within a white box outlined in pale yellow.](https://www.paloaltonetworks.com/content/dam/pan/en_US/images/cyberpedia/what-is-ai-prompt-security/AI-prompt-security-2025_2-prompt.png?imwidth=1080)

### [prompt injection](https://www.paloaltonetworks.com/cyberpedia/what-is-a-prompt-injection-attack)

**What Is a Prompt Injection Attack? [Examples & Prevention]**

![](/images/Prompt-injection-attack-2025_7.png)

### [comprehensive, contextual, and continuous](https://www.paloaltonetworks.com/blog/network-security/the-3cs-of-ai-red-teaming-comprehensive-contextual-continuous/)

**The 3Cs of AI Red Teaming: Comprehensive, Contextual & Continuous**（25年11月5日提出，可见是很新的标准！）

![](/images/word-image-347815-1.png)