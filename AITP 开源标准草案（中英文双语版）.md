# AITP 开源标准草案（中英文双语版）

# 中文版：AI Transport Protocol (AITP) 官方草案规范 v0.1.0

**草案状态：工作草案**

**日期：2026-03-21**

**类别：标准轨道**

**地址：https://github.com/AITP-Foundation/AITP**

## 摘要

本规范定义了 AI 传输协议（AI Transport Protocol，简称 AITP），一种面向 AI 时代互联网的通用连接层协议。AITP 标准化了 AI 智能体、应用程序、设备与 Web 服务之间的服务发现、意图表示、能力调用、语义互通及跨域信任机制。

它被设计为一套公共、开放、中立且向后兼容的协议，允许 AI 安全、统一地与整个数字世界进行交互。

## 1. 引言

### 1.1 动机

现代互联网是为人类用户设计的（基于 HTTP、HTML、移动应用）。随着 AI 智能体向自主化、任务导向型发展，当前生态系统存在以下关键局限：

- 接口碎片化、不兼容

- 机器可读数据格式存在歧义

- 平台孤立化与围墙花园模式

- 缺乏统一的、用户可控的信任与权限机制

- AI 无法通过标准方式发现并使用全网服务

### 1.2 目标

- 定义 AI 与服务、AI 与设备、AI 与 AI 通信的通用语言

- 支持现有服务零代码或低代码接入

- 实现跨平台一致的语义理解

- 建立以隐私为核心、以用户为中心的信任模型

- 成为与 HTTP、TCP/IP、HTML 同级的公共产品

### 1.3 范围

AITP 涵盖以下内容：

- 服务发现与元数据

- 标准化意图与调用

- 语义数据类型

- 请求/响应结构

- 信任、认证与授权

- 面向传统 Web 与 API 的桥接层

不涵盖以下内容：

- 新编程语言

- 替代 HTTP、TLS 或传输层协议

- 模型训练或推理逻辑

- 专有商业模式

## 2. 架构概述

AITP 定义了四层架构：

1. **意图层（Intent Layer）**：接收自然语言或结构化意图输入

2. **核心协议层（Core Protocol Layer）**：服务描述、调用、语义定义

3. **桥接层（Bridge Layer）**：将 HTML、REST、GraphQL、应用、物联网设备适配为 AITP 标准

4. **信任层（Trust Layer）**：权限、令牌、签名、隐私保护

## 3. 核心概念

### 3.1 服务提供方（Provider）

任何暴露 AITP 能力的系统，包括：

- 网站

- 应用程序

- 云服务

- 设备

- AI 智能体

### 3.2 能力（Capability）

可复用、可发现的功能，例如：

- 查询订单

- 创建提醒

- 控制设备

- 发起支付

### 3.3 语义类型（Semantic Type）

全球标准化的数据类型，确保 AI 永远不会误解数据值，包括：

- uid（用户ID）、text（文本）、number（数字）、boolean（布尔值）

- currency（货币）、date_time（时间）、date_range（时间范围）

- location（位置）、phone（电话）、email（邮箱）

- order（订单）、payment（支付）、file（文件）、image（图片）

### 3.4 信任范围（Trust Scope）

用户授予的一组权限，例如：

- user.info.read（读取用户信息）

- order.read（读取订单）

- payment.verify（验证支付）

- device.control（控制设备）

## 4. 服务发现

### 4.1 标准元数据

所有 AITP 服务提供方**必须**暴露以下地址：

https://<domain>/.well-known/aitp.json

### 4.2 aitp.json 示例

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

## 5. 协议格式

### 5.1 请求（Request）

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

### 5.2 响应（Response）

```json
{
  "aitp": "1.0",
  "id": "req-12345",
  "status": "success",
  "data": [
    {
      "order_id": "ord-999",
      "amount": "CNY 129.90",
      "status": "completed",
      "created_at": "2026-03-15T14:22:00Z"
    }
  ],
  "semantic": "structured"
}
```

### 5.3 URI 协议方案

AITP 的 URI 协议方案为：

aitp://<provider>/<capability>

## 6. 信任与安全模型

### 6.1 原则

- 用户同意优先

- 最小权限原则

- 短期、目的限定令牌

- 尽可能匿名身份

- 禁止跨服务提供方追踪用户

### 6.2 信任令牌（Trust Token）

一种 JWT 或等效令牌，包含以下内容：

- 签发者（Issuer）

- 主体（Subject，哈希后的用户ID）

- 权限范围（Scopes）

- 过期时间（Expiration）

- 签名（Signature）

### 6.3 安全要求

- 所有 AITP 调用**必须**使用 TLS 加密

- 任何 payload 中**禁止**包含明文密码

## 7. 应用场景

### 7.1 跨平台任务自动化

示例：“显示我这个月的外卖总消费”

### 7.2 设备全域控制

通过一个 AI 智能体控制家居、汽车、移动设备

### 7.3 AI 与 AI 协作

智能体之间以 AITP 为通用语言协商任务

### 7.4 传统 Web 现代化

网站无需重写即可实现 AI 兼容

## 8. 设计理念

- 公共产品：AITP 不属于任何单一公司

- 开放：免版权费，规范向所有人开放

- 极简：仅标准化必须统一的内容

- 务实：优先考虑实际落地与适配

- 向前兼容：可扩展且不破坏现有功能

## 9. IANA 相关说明

本草案请求 IANA 完成以下工作：

- 注册 aitp URI 协议方案

- 建立 AITP 语义类型注册表

- 建立 AITP 信任范围注册表

## 10. 安全考量

- 隐私与用户同意至关重要

- 明确禁止跨服务提供方追踪

- 与支付、设备控制相关的能力需高可信度信任机制

## 11. 参考文献

### 11.1 规范性参考文献

- URI 协议方案：RFC 3986

- 标准 URI：RFC 8615

- TLS：RFC 5246

### 11.2 信息性参考文献

- HTTP/1.1：RFC 2616

- JSON：RFC 8259

- W3C 语义 Web 标准

## 附录 A：完整示例流程

1. 用户：“显示我3月份的订单”

2. 智能体解析 /.well-known/aitp.json 获取服务信息

3. 智能体请求 order.read 权限范围

4. 用户确认授权

5. 智能体发送 AITP 调用请求

6. 服务提供方返回结构化 order 数据

7. 智能体向用户总结结果

---

# English Version: AI Transport Protocol (AITP) Official Draft Specification v0.1.0

**Draft Status: Working Draft**

**Date: 2026-03-21**

**Category: Standards Track**

**URL: https://github.com/AITP-Foundation/AITP**

## Abstract

This specification defines the AI Transport Protocol (AITP), a universal connectivity layer for the AI‑era Internet. AITP standardizes service discovery, intent representation, capability invocation, semantic interoperability, and cross‑domain trust between AI agents, applications, devices, and web services.

It is designed as a public, open, neutral, and backward‑compatible protocol that allows AI to safely and uniformly interact with the entire digital world.

## 1. Introduction

### 1.1 Motivation

The modern Internet is built for human users (HTTP, HTML, mobile apps). As AI agents become autonomous and task‑oriented, the current ecosystem presents critical limitations:

- Fragmented, incompatible APIs

- Ambiguous data formats for machines

- Siloed platforms and walled gardens

- Lack of unified, user‑controlled trust and permission

- No standard way for AI to discover and use services across the Internet

### 1.2 Goals

- Define a universal language for AI‑to‑service, AI‑to‑device, and AI‑to‑AI communication

- Enable zero‑code or low‑code adoption for existing services

- Provide consistent semantic understanding across platforms

- Establish a privacy‑first, user‑centric trust model

- Become a public good like HTTP, TCP/IP, and HTML

### 1.3 Scope

AITP covers:

- Service discovery and metadata

- Standardized intent and invocation

- Semantic data types

- Request/response structure

- Trust, authentication, and authorization

- Bridge layer for legacy web and APIs

Out of scope:

- New programming languages

- Replacement of HTTP, TLS, or transport layers

- Model training or inference logic

- Proprietary business models

## 2. Architecture Overview

AITP defines a four‑layer architecture:

1. **Intent Layer**: Natural language or structured intent input.

2. **Core Protocol Layer**: Service description, invocation, semantics.

3. **Bridge Layer**: Adapts HTML, REST, GraphQL, apps, IoT to AITP.

4. **Trust Layer**: Permissions, tokens, signatures, privacy.

## 3. Core Concepts

### 3.1 Provider

Any system that exposes AITP capabilities:

- Website

- App

- Cloud service

- Device

- AI Agent

### 3.2 Capability

A reusable, discoverable function:

- Query order

- Create reminder

- Control device

- Make payment

### 3.3 Semantic Type

A globally standardized data type so AI never misinterprets values:

- uid, text, number, boolean

- currency, date_time, date_range

- location, phone, email

- order, payment, file, image

### 3.4 Trust Scope

A set of permissions granted by the user:

- user.info.read

- order.read

- payment.verify

- device.control

## 4. Service Discovery

### 4.1 Well-Known Metadata

All AITP providers **MUST** expose:

https://<domain>/.well-known/aitp.json

### 4.2 Example aitp.json

```json
{
  "protocol": "AITP/1.0",
  "provider": "Example Shop",
  "trust_scope": "public",
  "capabilities": [
    {
      "id": "exampleshop.order.query",
      "name": "Query Orders",
      "description": "List user orders in a time range",
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

## 5. Protocol Format

### 5.1 Request

```json
{
  "aitp": "1.0",
  "id": "req-12345",
  "method": "aitp.invoke",
  "intent": "Show my orders in March 2026",
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

### 5.2 Response

```json
{
  "aitp": "1.0",
  "id": "req-12345",
  "status": "success",
  "data": [
    {
      "order_id": "ord-999",
      "amount": "CNY 129.90",
      "status": "completed",
      "created_at": "2026-03-15T14:22:00Z"
    }
  ],
  "semantic": "structured"
}
```

### 5.3 URI Scheme

The AITP URI scheme is:

aitp://<provider>/<capability>

## 6. Trust and Security Model

### 6.1 Principles

- User consent first

- Least privilege

- Short-lived, purpose-bound tokens

- Anonymized identity where possible

- No tracking across providers

### 6.2 Trust Token

A JWT or equivalent token that contains:

- Issuer

- Subject (hashed user ID)

- Scopes

- Expiration

- Signature

### 6.3 Security Requirements

- All AITP calls **MUST** use TLS.

- Plaintext passwords **MUST NOT** be included in any payload.

## 7. Use Cases

### 7.1 Cross‑platform task automation

"Show my total spending on food delivery this month."

### 7.2 Universal device control

Control home, car, and mobile via one AI agent.

### 7.3 AI-to-AI cooperation

Agents negotiate tasks using AITP as common language.

### 7.4 Legacy web modernization

Websites become AI‑compatible without rewriting.

## 8. Design Philosophy

- Public good: No single company owns AITP.

- Open: Royalty-free, specification open to all.

- Minimalist: Only standardize what must be universal.

- Pragmatic: Prioritize real-world adoption.

- Forward-compatible: Extensible without breaking changes.

## 9. IANA Considerations

This draft requests IANA to:

- Register the `aitp` URI scheme

- Establish a registry for AITP semantic types

- Establish a registry for AITP trust scopes

## 10. Security Considerations

- Privacy and user consent are critical.

- Cross-provider tracking is explicitly discouraged.

- Capabilities related to payment or device control require high-assurance trust.

## 11. References

### 11.1 Normative

- URI Scheme: RFC 3986

- Well-Known URIs: RFC 8615

- TLS: RFC 5246

### 11.2 Informative

- HTTP/1.1: RFC 2616

- JSON: RFC 8259

- W3C Semantic Web

## Appendix A: Full Example Flow

1. User: "Show my March orders."

2. Agent resolves `/.well-known/aitp.json`

3. Agent requests `order.read` scope

4. User approves

5. Agent sends AITP invoke request

6. Provider returns structured `order` data

7. Agent summarizes to user
> （注：文档部分内容可能由 AI 生成）