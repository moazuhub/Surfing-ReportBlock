Surfing-ReportBlock

面向 Mihomo / Clash.Meta 的系统报告、应用遥测与广告 SDK 规则项目。

<p align="center">"Mihomo" (https://img.shields.io/badge/Mihomo-Compatible-blue)
"Format" (https://img.shields.io/badge/Rule-Classical-green)
"Validation" (https://img.shields.io/badge/Validation-GitHub%20Actions-success)
"License" (https://img.shields.io/badge/Status-Developing-orange)

</p>项目简介

Surfing-ReportBlock 是一个面向 Mihomo 内核的规则集合项目，主要用于识别和管理系统报告、应用数据统计、第三方广告 SDK 及应用遥测相关域名。

项目采用分类管理方式，将不同用途的域名规则拆分为独立文件，方便维护、审核和按需引用。

本项目不以扩大拦截范围为目标，而是优先考虑规则准确性、可追溯性和网络服务兼容性。

核心原则：宁可少拦截，也不误伤正常网络服务。

项目目标

- 整理经过验证的系统报告与诊断遥测域名。
- 识别第三方应用统计和分析 SDK。
- 管理经过验证的广告 SDK 域名。
- 识别应用自身的遥测和诊断上报行为。
- 使用 GitHub Actions 自动检查规则文件的基础格式。
- 保持规则来源透明，便于审查和追踪变更。
- 兼容 Mihomo classical 规则集格式。

目录结构

Surfing-ReportBlock/
│
├── SystemReport.list
├── Analytics.list
├── AdSDK.list
├── AppTelemetry.list
│
├── README.md
│
└── .github/
    └── workflows/
        └── validate.yml

规则分类

文件| 用途
SystemReport.list| 系统报告、诊断及错误信息上报
Analytics.list| 应用统计、使用情况分析及第三方分析 SDK
AdSDK.list| 第三方广告 SDK 及广告追踪域名
AppTelemetry.list| 应用遥测、诊断及行为数据上报

各分类之间尽量避免重复收录。

同一个域名如果存在多种业务用途，应先调查其实际功能，再决定是否纳入规则。

规则格式

项目使用 Mihomo classical 规则格式。

示例：

DOMAIN,example.com
DOMAIN-SUFFIX,example.net

以上仅为格式示例，不代表这两个域名属于拦截目标。

规则文件遵循以下约定：

1. 一条规则占一行。
2. 使用 Mihomo 支持的规则类型。
3. 注释以 "#" 开头。
4. 不添加未经验证的域名。
5. 不使用过宽的关键词规则扩大拦截范围。
6. 不将普通业务 API 直接认定为遥测服务。

安全与兼容性原则

本项目特别关注 Android 系统和常见应用的网络兼容性。

以下服务属于重点保护对象：

- Android 系统更新及厂商 OTA 服务。
- 系统推送、应用推送及消息通知。
- DNS 解析及必要的网络探测服务。
- IMS、VoLTE、VoWiFi 和运营商相关服务。
- 银行、支付、金融及身份认证服务。
- 应用登录、验证码及核心业务 API。
- 游戏登录、匹配、语音和 UDP 通信。
- 必要的崩溃报告和安全诊断服务。

不能仅凭域名名称、IP 地址或第三方标签判断一个服务属于广告或遥测。

发现域名存在多种业务用途时，优先保留正常业务功能。

规则审核机制

每个准备加入项目的域名，建议经过以下审核：

1. 确认域名真实存在。
2. 确认域名所属应用或服务。
3. 查明域名实际用途。
4. 检查是否承担登录、支付、推送等功能。
5. 评估拦截后可能产生的影响。
6. 确认有可靠证据后再提交。

不确定用途的域名不应直接加入正式拦截列表。

自动化校验

项目使用 GitHub Actions 检查规则文件。

当前工作流主要负责：

- 检查四个规则文件是否存在。
- 检查非注释行的基础规则格式。
- 在规则文件更新时自动执行。
- 在 Pull Request 中执行基础检查。

需要注意：

格式校验通过不等于域名真实性验证，也不代表规则不会误伤。

后续可以逐步增加域名重复检查、规则来源检查和安全审核机制。

Mihomo 使用方式

项目规则文件可以通过 Mihomo 的远程 rule-provider 引用。

以下为配置示例，仓库默认分支如不是 "main"，请相应调整 URL。

rule-providers:
  SystemReport:
    type: http
    behavior: classical
    format: text
    path: ./ruleset/SystemReport.list
    url: https://raw.githubusercontent.com/moazuhub/Surfing-ReportBlock/main/SystemReport.list
    interval: 86400

  Analytics:
    type: http
    behavior: classical
    format: text
    path: ./ruleset/Analytics.list
    url: https://raw.githubusercontent.com/moazuhub/Surfing-ReportBlock/main/Analytics.list
    interval: 86400

  AdSDK:
    type: http
    behavior: classical
    format: text
    path: ./ruleset/AdSDK.list
    url: https://raw.githubusercontent.com/moazuhub/Surfing-ReportBlock/main/AdSDK.list
    interval: 86400

  AppTelemetry:
    type: http
    behavior: classical
    format: text
    path: ./ruleset/AppTelemetry.list
    url: https://raw.githubusercontent.com/moazuhub/Surfing-ReportBlock/main/AppTelemetry.list
    interval: 86400

对应规则引用示例：

rules:
  - RULE-SET,SystemReport,REJECT
  - RULE-SET,Analytics,REJECT
  - RULE-SET,AdSDK,REJECT
  - RULE-SET,AppTelemetry,REJECT

重要：目前四个规则文件只有注释，没有实际拦截规则。以上配置仅供后续正式启用时参考。

建议先完成规则审核和应用测试，再将规则引用加入日常使用的 Mihomo 配置。

规则来源与更新

本项目优先考虑：

- 官方公开的域名资料。
- 有持续维护记录的开源规则项目。
- 可以追溯到具体应用或 SDK 的域名资料。
- 有明确用途说明和可靠证据的安全研究资料。

未经审核的第三方规则不直接自动合并。

后续自动更新应保留来源、更新时间和变更记录，并尽量通过 Pull Request 审核后再进入正式规则文件。

免责声明

本项目仅用于网络流量管理、隐私保护和规则研究。

域名的用途可能随着应用版本、服务架构和业务调整而变化。

使用者应根据自身设备和应用情况进行测试。

项目维护者不保证所有规则在所有设备、地区和应用版本中均适用。

因使用规则导致的应用异常，应及时停用相关规则并检查日志。

项目状态

当前阶段：基础框架建设完成，规则收集与审核尚未开始。

当前四个 ".list" 文件均为注释内容，不包含实际拦截域名。

后续开发计划：

- [x] 建立四个分类规则文件
- [x] 建立 GitHub Actions 基础校验
- [x] 建立项目基础目录
- [ ] 完善规则来源和审核标准
- [ ] 收集并验证第一批候选域名
- [ ] 增加重复域名和安全检查
- [ ] 建立可信上游同步机制
- [ ] 完成 Mihomo 实际环境测试

---

项目仓库：
https://github.com/moazuhub/Surfing-ReportBlock

维护原则：准确、透明、克制、安全。