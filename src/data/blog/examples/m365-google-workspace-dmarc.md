---
author: Admin
pubDatetime: 2026-03-22T09:15:00Z
title: 拒绝被判为垃圾邮件：Microsoft 365 与 Google Workspace 域名邮箱终极配置指南
postSlug: m365-google-workspace-dmarc
featured: false
tags: [企业邮箱, 域名解析, DMARC, 网络安全]
description: 解析自定义域名邮箱的核心安全记录：如何正确配置 SPF, DKIM, DMARC 以及进阶的 MTA-STS 协议。
---

## 域名邮箱的痛点：发信必进垃圾箱？

很多站长在购买了心仪的域名后，都会尝试配置专属的域名邮箱（如 `admin@yourdomain.com`）。不管是选择巨头级的 **Microsoft 365** 还是 **Google Workspace**，亦或是折腾自建的 Roundcube，大家都会面临同一个致命问题：发出去的邮件，直接被对方的服务器扔进了垃圾邮件分类。

这通常不是邮箱服务商的问题，而是因为你的域名缺少了必要的**身份验证记录**。

### 邮箱安全的三驾马车：SPF, DKIM, DMARC

要想让收件方（比如 Gmail、Outlook）相信这封邮件真的是你发出的，而不是骗子伪造的，你必须在 Cloudflare 或你的域名解析后台配置以下三个 TXT 记录：

1. **SPF (发件人策略框架)**：告诉全世界，只有哪些 IP 地址或服务器有权以你域名的名义发邮件。比如 Google Workspace 的 SPF 记录通常是 `v=spf1 include:_spf.google.com ~all`。
2. **DKIM (域名密钥识别邮件)**：给你的每一封邮件盖上一个“数字签名”。收件方会用你 DNS 里的公钥来解密这个签名，如果对得上，说明邮件内容没有在半路被篡改。
3. **DMARC (基于域的邮件身份验证)**：这是最关键的“指挥官”。它告诉收件方，如果一封邮件没有通过 SPF 或 DKIM 的验证，该怎么处理（是直接拒收 `p=reject`，还是放进垃圾箱 `p=quarantine`），并向你发送一份安全报告。

### 高阶防护：配置 MTA-STS

当你的基础三项配置完美后，作为极客，我们还可以更进一步：配置 **MTA-STS (邮件传输代理严格传输安全)**。

传统的邮件在服务器之间传输时，可能是不加密的（明文传输）。MTA-STS 的作用是强制要求其他服务器在给你的域名发邮件时，**必须使用 TLS 加密连接**。如果不加密，就直接拒收。

**如何配置 MTA-STS？**
1. 创建一个子域名 `mta-sts.yourdomain.com`。
2. 在该子域名下托管一个纯文本文件（通常放在 `.well-known/mta-sts.txt` 路径下），声明你的加密策略。
3. 在主域名添加一个 `_mta-sts` 的 TXT 记录指向这个策略。

配置完这些，你的域名邮箱权重将达到满分，不管是发推广邮件还是日常沟通，都畅通无阻。