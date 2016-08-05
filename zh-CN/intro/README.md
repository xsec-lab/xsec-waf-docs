---
name: 简介
---

# X-WAF

X-WAF是一款适用中、小企业的云WAF系统，让中、小企业也可以非常方便地拥有自己的免费云WAF。

# 主要特性

- 安装、部署与维护非常简单
- 支持在线管理waf规则
- 支持在线管理后端服务器
- 支持IP和URL的黑、白名单
- 支持对get\post\cookies\user_agent参数进行过滤
- 支持多台waf的配置可自动同步
- 跨平台，支持在linux\unix\mac和windows服务器中部署

# 架构简介
x-waf由waf主体与Waf管理后台组成：

- [waf主体](https://github.com/xsec-lab/x-waf/tree/master/waf)：基于openresty + lua开发。
- [waf管理后台](https://github.com/xsec-lab/x-waf/tree/master/waf-admin)：采用golang + xorm + macrom开发的，支持二进制的形式部署。

waf和waf-admin必须同时部署在充当云waf的反向代理服务器中。