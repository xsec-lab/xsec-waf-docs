---
name: 常见问题
---

## 常见问题
### openresty的语法正确，却启动失败

以下的错误原因为有些linux发布版本要求监听1024以下的端口的服务必须以root权限启动。
```bash
/usr/local/openresty/nginx/sbin/nginx -t
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /usr/local/openresty/nginx/conf/nginx.conf:1
nginx: the configuration file /usr/local/openresty/nginx/conf/nginx.conf syntax is ok
nginx: [emerg] bind() to 0.0.0.0:80 failed (13: Permission denied)
nginx: configuration file /usr/local/openresty/nginx/conf/nginx.conf test failed

```
解决方案：以root账户或用sudo命令执行。

### 在waf admin中同步全部策略报错

返回`{"Status":1,"Message":"reload nginx configure faild"}`错误的原因为waf admin是以普通用户权限启动的，在同步完策略后无法操作openresty的master进程完成策略加载的操作。

解决方案：以root账户或用sudo命令启动waf admin。

### 正常访问被误报为CC攻击

如果waf前端有高可用VIP，需要将用户真实地址传递给后端waf，否则waf获取到的是vip的地址，访问量大到触发cc攻击的阀值后就会将正常的请求误报为cc攻击。

解决方案：在前端VIP中将用户真实地址传递到后端服务器。