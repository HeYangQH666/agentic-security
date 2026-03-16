---
title: OpenClaw (之前的 Moltbot, Clawdbot) 可能标示下一次AI安全危机
date: 2026-03-16 10:50:00
tags:
  - Protect AI社区
  - OpenClaw安全
categories:
  - agentic-security
---



来源：[OpenClaw (formerly Moltbot, Clawdbot) May Signal the Next AI Security Crisis - Palo Alto Networks Blog](https://www.paloaltonetworks.com/blog/network-security/why-moltbot-may-signal-ai-crisis/)

发布时间：2026年1月9日

## 🧭 1. **什么是 OpenClaw？为什么突然爆火？**

OpenClaw（原名 Moltbot / Clawdbot）被其作者称为：

> **“the AI that actually does things.”**

它具备极强的自主能力，并在一周内获得：

- ⭐ **85,000+ GitHub stars**
- 🍴 **11,500+ forks**

### **核心能力（高度自主）**

- 浏览网页
- 总结 PDF
- 读写本地文件
- 控制桌面应用
- 发送邮件、消息
- 访问密码、API 密钥、浏览器 Cookie
- **最关键：持久化记忆（persistent memory）**

它几乎就是一个“能操作你电脑的一体化 AI 助手”。

------

## 🛑 2. **核心问题：强大，但极度不安全**

为了正常工作，OpenClaw 需要：

- root 文件访问
- 所有密码与 API 密钥
- 浏览器历史与 Cookie
- 全盘文件访问
- 可被 WhatsApp / Telegram 等远程触发

**这意味着：任何输入都可能触发高权限操作。**

> OpenClaw 具备：
>
> - root 权限
> - 全盘文件访问
> - 密码与 API 密钥
> - 浏览器 Cookie
> - 能执行命令
> - 能发消息
> - 能读网页
> - 能读 Telegram / WhatsApp

------

## ⚠️ 3. **三大典型使用场景 → 三大高危风险**

### **场景 1：研究主题 + 生成社交媒体内容**

风险：

- 网页结果可能包含 **间接提示注入（indirect prompt injection）**
- OpenClaw 会执行恶意命令、泄露机密，并自动发布到社交媒体
- 无人工审核

------

### **场景 2：读取 Telegram 消息并生成行动项**

风险：

- 恶意链接与正常消息同等对待
- 即使是加密消息（Signal/WhatsApp）也会被解密后处理
- 持久化记忆导致“延迟多轮攻击链”
  - 恶意指令可在一周后触发
  - 当前安全系统无法检测

------

### **场景 3：使用第三方托管的 OpenClaw 技能**

风险：

- 技能无审核、无沙箱
- 恶意代码可写入持久化记忆
- 可窃取机密、发送私密对话、窃取商业数据

------

## 🔥 4. **OpenClaw 的根本问题：没有信任边界**

OpenClaw **不区分可信与不可信输入**：

- 网页内容
- 消息
- 第三方技能
- 用户命令
- 旧记忆

全部被视为同等可信，并能直接影响：

- 推理
- 计划
- 工具调用（bash、文件、邮件、API）

这导致：

> **外部输入 → 直接控制高权限操作**

------

## 💣 5. **“致命三角”（Lethal Trifecta）被 OpenClaw 扩展为“四角”**

![](/images/Screenshot-2026-01-28-at-11.01.07-AM.png)

Simon Willison 的原始“致命三角”：

1. 访问私密数据
2. 暴露于不可信内容
3. 能执行外部操作

```
文章引用这张图，是为了说明：

任何 AI Agent 只要同时具备这三种能力，就天然处于极高风险区。

为什么？

因为：

- 它能读你的隐私
- 它会接触不可信输入（网页、消息、第三方技能）
- 它能执行外部操作（发邮件、执行命令、上传数据）

这三者一旦同时存在，就形成了一个结构性安全漏洞：
外部输入 → 内部推理 → 高权限操作  
中间没有任何隔离层。

OpenClaw 恰恰具备这三者，而且每一项都做得“极致”。
```

OpenClaw 新增第 4 个能力：

#### **4. 持久化记忆（Persistent Memory）**

![](/images/Screenshot-2026-01-28-at-11.25.23-AM.png)

带来：

- **状态化攻击（stateful attacks）**
- **延迟执行（delayed execution）**
- **逻辑炸弹（logic bombs）**
- **记忆投毒（memory poisoning）**

攻击者可以：

- 把恶意指令分段写入记忆（恶意内容可以在记忆中潜伏）
- 等待未来某个时机自动触发（攻击不再是即时的）
- 形成“时间偏移攻击链（time-shifted attack chain）”

```
持久化记忆

→ 让攻击变成：

- 可延迟
- 可跨会话
- 可跨天
- 可组合
- 可潜伏
- 可自我强化
```

------

## 🧨 6. **OpenClaw 在 OWASP Agent Top 10 中几乎全线失守**

文章逐条映射了 OpenClaw 的漏洞：

| OWASP 风险              | OpenClaw 的问题               |
| ----------------------- | ----------------------------- |
| A01 Prompt Injection    | 网页、消息、技能都能注入指令  |
| A02 不安全工具调用      | 工具调用基于不可信记忆        |
| A03 过度自治            | root 权限 + 网络访问 + 无边界 |
| A04 无人工审核          | 删除文件、泄露数据无需确认    |
| A05 记忆投毒            | 所有记忆无信任等级            |
| A06 不安全第三方技能    | 技能可写入记忆并执行          |
| A07 权限缺乏隔离        | 同一 agent 处理所有权限       |
| A08 供应链风险          | 上游 LLM 未验证               |
| A09 无限制 agent 间通信 | 未来版本风险更大              |
| A10 缺乏运行时监控      | 无策略层、无异常检测          |

附：[OWASP Agentic AI Survival Guide](https://www.paloaltonetworks.com/resources/ebooks/owasp-agentic-top-10-survival-guide)

文章结论非常明确：

> **OpenClaw 是一个无限攻击面，并且拥有你的所有凭证。**

------

## 🧭 7. **持久化记忆：未来 AI 助手必须有，但必须安全**

文章承认：

- 持久化记忆是迈向 AGI 的关键能力
- 但在无治理的自主 agent 中，它是“给火上浇汽油”

------

## 🛡️ 8. **最终结论：OpenClaw 不适合企业使用**

作者观点：

- OpenClaw 的架构无法治理
- 自主性过强
- 攻击面不可控
- 即使 UI 做了安全强化，也无济于事

```
因为：

- 它具备致命三角
- 又具备持久化记忆
- 又没有信任边界
- 又没有人类审批
- 又没有沙箱
- 又没有权限隔离
- 又没有运行时监控
- 又没有策略层
- 又没有记忆分级

换句话说：

> **OpenClaw = 一个拥有你所有权限、会执行外部命令、会记住所有输入、且无法治理的 AI。**

这就是文章的核心警告。
```

未来的 AI 助手必须：

- **更智能**
- **更安全**
- **有治理能力**
- **知道什么时候不该行动**

------

## 🔥9.可视化图表——攻击链图、趋势图、风险矩阵

### 📊 **图表 1：OpenClaw 攻击链（Attack Chain）**

#### **OpenClaw 的 AI 驱动攻击链（基于原文三大场景 + 持久化记忆）**

```
[1. Untrusted Input Ingestion 不可信输入摄取]
    - 网页搜索结果（含 HTML 注入）
    - Telegram / WhatsApp 消息
    - 第三方技能描述与代码
    - 这些内容全部进入 OpenClaw 的上下文与记忆

                     ↓

[2. Memory Poisoning 记忆投毒]
    - 所有输入被写入持久化记忆
    - 无信任等级、无过期机制
    - 恶意指令可分段写入（fragmented payload）

                     ↓

[3. Reasoning Manipulation 推理操控]
    - OpenClaw 的推理链被污染
    - 旧记忆 + 恶意内容共同影响计划生成
    - 无策略层、无隔离区

                     ↓

[4. Tool Invocation 工具调用]
    - 高权限工具被自动调用：
        * bash（rm -rf）
        * 文件读写
        * 邮件发送
        * 消息发送
        * API 调用
    - 无人工审批（A04）

                     ↓

[5. External Communication 外部通信]
    - 自动发布社交媒体内容
    - 自动发送消息/邮件
    - 自动上传数据

                     ↓

[6. Delayed Execution 延迟触发]
    - 持久化记忆使攻击可跨天/跨周触发
    - 形成“时间偏移攻击链（time-shifted attack chain）”

                     ↓

[7. Full Compromise 全面失陷]
    - 凭证泄露
    - 私密文件泄露
    - 业务数据泄露
    - 系统被远程操控
```

------

### 📈 **图表 2：AI 攻击演进趋势图（Trend Chart）**

#### **OpenClaw 在 AI 攻击演进中的位置**

```
攻击复杂度 ↑
│
│                ┌───────────────────────────────
│                │ 未来：自主 AI 攻击者（Autonomous AI Attackers）
│                │   - 自主决策
│                │   - 自我学习
│                │   - 多agent协同攻击
│                │   - 大规模并行攻击
│        ┌───────┘
│        │
│   ┌────┘     当前：OpenClaw 阶段（Agentic + Persistent Memory）
│   │            - 全链路自动化
│   │            - 持久化记忆（延迟攻击）
│   │            - 无信任边界
│───┘
│
│ 过去：AI 辅助攻击（AI-assisted）
│       - 生成 phishing 文本
│       - 自动化程度低
│
└────────────────────────────────────────→ 时间 →
```

**关键洞察：** 
 OpenClaw 是从“AI 辅助攻击”向“AI 自主攻击”的关键跃迁点。

------

### 🟥🟧🟨🟩 **图表 3：OpenClaw 风险矩阵（Risk Matrix）**

#### **Impact × Likelihood（基于原文三大场景 + OWASP 映射）**

| 风险事件                                        | 可能性（Likelihood） | 影响（Impact） | 风险等级 |
| ----------------------------------------------- | -------------------- | -------------- | -------- |
| **持久化记忆导致延迟攻击链**                    | 高                   | 高             | 🔴 极高   |
| **第三方技能注入恶意代码**                      | 高                   | 高             | 🔴 极高   |
| **网页/消息中的间接提示注入**                   | 高                   | 高             | 🔴 极高   |
| **高权限工具被自动调用（rm -rf / send email）** | 中高                 | 高             | 🔴 极高   |
| **凭证泄露（API 密钥、密码、Cookie）**          | 中高                 | 高             | 🔴 极高   |
| **业务数据被自动发布到外部平台**                | 中                   | 高             | 🟧 高     |
| **加密消息（Signal/WhatsApp）被恶意利用**       | 中                   | 中高           | 🟧 高     |
| **供应链风险（上游 LLM 未验证）**               | 中                   | 中             | 🟨 中     |
| **用户误操作导致权限滥用**                      | 中                   | 中             | 🟨 中     |

**结论：** 
 OpenClaw 的风险集中在 **高可能性 × 高影响** 的象限，是典型的“不可接受风险”。

------



