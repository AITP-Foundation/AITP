AITP 开源项目 README

AITP: AI Transport Protocol

AI 时代互联网的通用连接层标准 | Universal Connectivity Layer for the AI-Era Internet

📋 关于项目 | 🎯 核心目标 | 🏗️ 架构概述 | 🚀 快速入门 | 🤝 贡献指南 | 📄 开源协议


** ⚠️ 重要： **本项目是 AI 时代互联网通用连接层协议（AITP）的设想

1. 关于项目

AITP（AI Transport Protocol，AI 传输协议）是一套面向 AI 时代互联网的通用连接层协议，旨在解决当前互联网服务碎片化、机器语义歧义、跨平台信任缺失等核心问题，让 AI 能够像访问变量一样安全、统一、标准化地访问全网服务、设备与数据。

本项目是 AITP 协议的官方开源草案实现，提供完整的协议规范、技术文档与示例，致力于推动 AITP 成为 AI 时代与 HTTP、TCP/IP 同级的公共基础设施。

2. 核心目标

- 统一 AI 与服务、AI 与设备、AI 与 AI 的通信语言，消除接口异构与语义歧义

- 支持现有 Web、API、APP、IoT 设备零代码/低代码接入，降低 AI 适配成本

- 建立以用户为中心、隐私优先的跨域信任模型，保障 AI 交互的安全性与可控性

- 打造开放、中立、免版权费的公共协议，成为 AI 时代互联网的基础连接标准

3. 架构概述

AITP 采用轻量级四层架构，自上而下分别为：

1. 意图层（Intent Layer）：接收自然语言或结构化指令，解析用户/AI 意图并拆解任务

2. 核心协议层（Core Protocol Layer）：定义服务描述、调用格式、统一语义类型与请求/响应结构

3. 桥接层（Bridge Layer）：自动适配 HTML、REST、GraphQL 等现有技术栈，实现零改造接入

4. 信任层（Trust Layer）：提供统一的权限管理、信任令牌与签名机制，保障跨域交互安全

4. 快速入门

4.1 服务提供方接入

所有支持 AITP 的服务，需在域名根目录暴露元数据文件：https://<domain>/.well-known/aitp.json

元数据示例（详细规范见 协议草案）：
```json
{
  "protocol": "AITP/1.0",
  "provider": "示例商店",
  "trust_scope": "public",
  "capabilities": [
    {
      "id": "exampleshop.order.query",
      "name": "查询订单",
      "description": "查询用户指定时间范围内的订单",
      "input": {
        "user_id": "uid",
        "time_range": "date_range"
      },
      "output": ["order"],
      "permission": "order.read",
      "endpoint": "/aitp/v1/call"
    }
  ]
}
```

4.2 发起 AITP 请求

使用 AITP 标准请求格式调用服务能力，示例：
```json
{
  "aitp": "1.0",
  "id": "req-12345",
  "method": "aitp.invoke",
  "intent": "显示我2026年3月的订单",
  "capability": "exampleshop.order.query",
  "params": {
    "user_id": "user-789",
    "time_range": "2026-03-01 to 2026-03-31"
  },
  "trust": {
    "token": "trust-abc-123",
    "scopes": ["order.read"],
    "sign": "a1b2c3d4e5f6"
  }
}
```

5. 贡献指南

AITP 是开放的公共项目，欢迎所有开发者、研究者参与贡献，共同完善协议规范与生态建设。

5.1 贡献方式

- 提交 Issue：反馈 bug、提出需求或建议

- 提交 Pull Request：修复 bug、完善文档、补充示例代码

- 参与讨论：在 Discussions 中交流协议设计与落地思路

5.2 贡献规范

- 代码与文档需遵循项目统一风格，中文文档使用规范简体中文，英文文档使用规范美式英语

- 提交 PR 前需进行测试，确保不影响现有功能

- 所有贡献需符合开源协议要求，不包含任何侵权、涉密内容

6. 开源协议

本项目采用 Apache License 2.0 开源协议，允许免费使用、修改、分发，无需支付版权费用，详情见 LICENSE 文件。



