# CDN与Cloudflare验证绕过技术指南

> 本项目整理了主流CDN与WAF防护机制的验证方法，帮助安全研究人员和运维团队评估自身基础设施的防护强度。

## 📋 目录

- [Cloudflare验证机制分析](#cloudflare验证机制分析)
- [CDN源站发现技术](#cdn源站发现技术)
- [WAF规则绕过验证](#waf规则绕过验证)
- [JA3/JA4指纹技术](#ja3ja4指纹技术)
- [实用工具推荐](#实用工具推荐)

## Cloudflare验证机制分析

Cloudflare作为全球最大的CDN服务商之一，其防护体系包含多个层级：

### 1. DNS层防护
- 代理模式隐藏真实IP
- Anycast网络分散流量
- DNSSEC增强可信度

### 2. 应用层防护
- **Browser Integrity Check**：检测非浏览器客户端
- **JS Challenge**：JavaScript计算挑战
- **Turnstile**：新型验证码替代方案
- **Rate Limiting**：请求频率限制

### 3. 网络层防护
- L3/L4 DDoS防护
- SYN Cookie防护
- 数据包异常检测

## CDN源站发现技术

在进行防护评估时，定位CDN背后的真实源站IP是关键步骤：

| 方法 | 原理 | 适用场景 |
|------|------|----------|
| 历史DNS记录 | 查询DNS历史数据库 | 启用CDN前的记录 |
| 邮件头分析 | 查看邮件 originating IP | 目标有邮件服务 |
| 子域名扫描 | 边缘子域名可能未走CDN | 大型企业 |
| SSL证书搜索 | 通过证书关联域名 | 使用唯一证书的目标 |
| Favicon哈希 | 利用favicon hash搜索 | shodan/censys |
| 信息泄露 | GitHub/pastebin泄露 | OSINT收集 |

## WAF规则绕过验证

常见WAF规则的验证方法：

### SQL注入规则测试
- 大小写混合：`UnIoN SeLeCt`
- 编码绕过：URL编码、Unicode编码
- 注释嵌套：`/*!50000UNION*/`
- 空格替换：`/**/`、`%0a`、`%09`

### XSS规则测试
- 标签变形：`<ScRiPt>`、`<img/src=x>`
- 事件处理器：`onerror`、`onload`
- 编码绕过：HTML实体编码、Base64

## JA3/JA4指纹技术

JA3是TLS客户端指纹识别技术，通过分析Client Hello数据包特征识别客户端类型：

```
JA3 = md5(SSLVersion, Cipher, SSLExtension, EllipticCurve, EllipticCurvePointFormat)
```

JA4是改进版本，提供了更细粒度的指纹识别能力。

### 应用场景
- 识别非浏览器自动化工具
- 检测爬虫和扫描器
- WAF规则增强

## 实用工具推荐

| 工具 | 用途 | 链接 |
|------|------|------|
| Censys | 互联网设备搜索 | censys.io |
| Shodan | IoT设备搜索 | shodan.io |
| SecurityTrails | DNS历史记录 | securitytrails.com |
| crt.sh | SSL证书搜索 | crt.sh |
| VirusTotal | 综合威胁情报 | virustotal.com |

## 🔗 相关资源

- **[破晓安全平台](https://zerodawnsec.com)** - 专业的网络基础设施压力测试与安全评估平台
- **[DDoS压力测试完整指南](https://zerodawnsec.com/ddos-stress-test-guide.html)** - 详细测试方法教程
- **[Cloudflare验证绕过详解](https://zerodawnsec.com/cloudflare-bypass.html)** - 完整绕过技术分析

## 📄 License

MIT License - 仅供安全研究和授权测试使用

---

<p align="center">⚠️ 本项目仅供授权安全测试与学术研究使用</p>
