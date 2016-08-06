---
name: X-WAF简介
---

# X-WAF

X-WAF是一款适用中、小企业的云WAF系统，让中、小企业也可以非常方便地拥有自己的免费云WAF。

# 主要特性

- 支持对常见WEB攻击的防御，如sql注入、xss、路径穿越，阻断扫描器的扫描等
- 对持对CC攻击的防御
- waf为反向模式，后端保护的服务器可直接用内网IP，不需暴露在公网中
- 支持IP、URL、Referer、User-Agent、Get、Post、Cookies参数型的防御策略
- 安装、部署与维护非常简单
- 支持在线管理waf规则
- 支持在线管理后端服务器
- 多台waf的配置可自动同步
- 跨平台，支持在linux、unix、mac和windows操作系统中部署

# 架构简介
x-waf由waf自身与Waf管理后台组成：

- [waf](https://github.com/xsec-lab/x-waf)：基于openresty + lua开发。
- [waf管理后台](https://github.com/xsec-lab/x-waf-admin)：采用golang + xorm + macrom开发的，支持二进制的形式部署。

waf和waf-admin必须同时部署在每一台云WAF服务器中。