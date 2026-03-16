---
layout: post
title: "Skill、Prompt、MCP、SubAgent选型-Claude官方建议"
date: 2026-03-16 09:10:00 +0800
categories: [AI, Agent]
tags: [Skill, Prompt, MCP, SubAgent]
---

# Skill、Prompt、MCP、SubAgent选型-Claude官方建议

****
在Agent架构设计中Skill、Prompt、MCP、SubAgent是关键的构件，他们是在Agent演进过程不同阶段、场景下的产物，用于适配解决不同的问题，同时也是上下文管理策略的重要手段；由于相互之间在特定问题下存在替代性，在实际的Agent架构设计中如何来决策采纳哪种解决方案？是否应该只采用一种组件来解决所有问题呢，根据Claude官方的说明汇总一些方案方便大家参考。

**1、核心区别对比**：


| (https://agentskills.io/home "Skill")（技能） | (https://modelcontextprotocol.io/docs/getting-started/intro "MCP")（Model Context Protocol） | Subagent（子代理） | Prompt（提示词） |
| --- | --- | --- | --- |
| **定义** | Skill 是包含指令、脚本和资源的文件夹，Claude 能够在相关任务时动态发现和加载。 | MCP 是一个开放标准协议，用于连接 AI 应用与外部系统（数据源、工具、业务系统）。 | Subagent 是具有独立上下文窗口、自定义系统提示词和特定工具权限的专用 AI 助手。 | 用户在对话过程中以自然语言向 Claude 提供的指令。 |
| **核心定义** | </br>- **程序化知识（Procedural Knowledge）**的载体</br>- 类似于专业领域的"培训手册"</br>- 使 Claude 具备特定领域的专业能力</br></br> | </br>- **工具连接层（Tool Connectivity）**</br>- 类似于 AI 世界的"USB-C 接口"</br>- 提供标准化的外部系统集成方式</br></br> | </br>- **任务委托与并行执行（Task Delegation）**</br>- 类似于组织中的"专业员工"</br>- 独立处理离散任务并返回结果给主代理</br></br> | </br>- **即时指令（Moment-to-moment Instructions）**</br>- 对话式的、反应式的交互方式</br>- 最基础、最直接的交互形式</br></br> |
| **工作原理** | 采用**渐进式披露（Progressive Disclosure）**架构：</br>1. **元数据层**（~100 tokens）：首先加载，用于判断 Skill 是否相关</br>2. **指令层**（<5k tokens）：匹配后加载完整指令</br>3. **资源层**：仅在需要时加载捆绑的文件或脚本</br></br> | </br>- MCP Server：暴露数据和功能</br>- MCP Client（如 Claude）：连接并使用这些服务</br>- 通过单一协议对接多种数据源，无需为每个系统集成单独开发</br></br> | </br>- 每个 Subagent 拥有独立的配置：定义职责、工作方式、可访问的工具</br>- 主代理可根据描述自动委派任务，或显式指定特定 Subagent</br>- 支持并行执行多个 Subagent</br></br> | </br>- 在每次对话轮次中传递</br>- 临时的、一次性的上下文注入</br>- 通过自然语言直接控制 AI 行为</br></br> |
| **主要载体** | 文件夹（指令+脚本+资源） | 协议+Server | 独立Agent实例 | 自然语言文本 |
| **加载时机** | 动态按需加载 | 始终可用 | 被调用时启动 | 每轮对话注入 |
| **复用范围** | 任何会话可加载 | 多客户端共享 | 特定工作流内复用 | 当前对话内有效 |

**2、选型决策依据**：


| 一句话总结 | 适用场景 | 何时需要替换为Skill |
| --- | --- | --- |
| **Skill** | 可复用的"专业技能手册"，教 Agent 如何做特定任务 | 重复性工作流，需要进行更精细的上下文管理提升执行效率 | - |
| **MCP** | AI 世界的"USB-C"，标准化连接外部数据和工具 | 需要访问外部数据 | 如果要解释如何使用工具或遵循流程时可以考虑Skill替代，在Skill中进行MCP访问。 |
| **SubAgent** | 独立的"专业员工"，处理特定任务并可并行执行 | 复杂任务分解 | 如果多个SubAgent需要相同的专业知识就应创建技能，而不是将这些知识构建到各个SubAgent中。Skill教授任何Agent都可以应用的专业知识；当需要具有特定工具权限和上下文隔离的独立任务执行时，请使用SubAgent。 |
| **Prompt** | 即时的"对话指令"，最灵活但最临时的控制方式 | 一次性任务，可以独立上下文、工具隔离 | 如果发现在多个对话中重复输入相同的流程性提示词，就该考虑创建 Skill。 |

**3、最佳实践建议**


1. **从 Prompt 开始，向 Skill 演进：**先用 Prompt 验证工作流、重复使用时提炼为 Skill。
2. **MCP 解决连接，Skill 解决流程：**MCP 让你能访问数据，Skill 教你如何处理数据。
3. **Subagent 用于隔离和并行：**需要工具权限限制时使用 Subagent，复杂任务可分解为多个 Subagent 并行处理。
4. **组合使用发挥最大威力：**MCP 提供数据，Skill 提供专业能力，Subagent 提供执行隔离，Prompt 提供即时调用。



---


- (https://claude.com/blog/skills-explained "Skill、MCP、Subagent、Prompt、Project 的详细对比")
- (https://modelcontextprotocol.io/introduction "MCP 协议定义、架构、使用场景")
- (https://github.com/anthropics/skills "官方 Skill 示例库")
- (https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview "Prompt 编写最佳实践")
- (https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills "渐进式披露架构详解")





