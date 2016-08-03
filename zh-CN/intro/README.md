---
name: 简介
---

# xsec-waf

xsec-waf是一款适用小企业的云waf系统。

# 主要特性

- 安装、部署与维护非常简单，小企业也可以非常方便地拥有自己的云WAF
- 规则可以后台管理
- 后端服务器可以后端管理
- 支持IP和URL的黑白名单
- 支持对get\post\cookies\user_agent参数进行过滤
- 多台waf的配置可自动同步

# 架构
xsec-waf由waf与Waf管理后台组成：

- waf是基于openresty + lua开发的
- waf管理后台是采用golang + xorm + macrom开发的