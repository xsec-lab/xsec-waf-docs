---
name: 高级用法
---

# 如何修改默认的虚拟主机模板

如果waf管理后台默认生成主要的模板无法满足您的业务需求，您可以到x-waf-admin的`templates`目录下修改模板文件`proxy.tmpl`，默认的模板内容如下：

```ini
upstream proxy_{{.site.SiteName}} { {{range .site.BackendAddr}}
        server {{.}} max_fails=3  fail_timeout=20s;
        {{end}} }

server  {
        listen       {{.site.Port}};
        ssl          {{.site.Ssl}};
        server_name  {{.site.SiteName}};
        client_max_body_size 100m;
        charset utf-8;
        access_log      /var/log/nginx/{{.site.SiteName}}-access.log;
        error_log       /var/log/nginx/{{.site.SiteName}}-debug.log {{.site.DebugLevel}};
        
        location ~* ^/ {
            proxy_pass_header Server;
            proxy_set_header Host $http_host;
            proxy_set_header Host api.miren.t.n.mi.com;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_pass $scheme://proxy_{{.site.SiteName}};
            }

            error_page  404              /index.html;
            error_page   500 502 503 504  /index.html;
        }

```

后端反向代理使用了Go语言内置的模板引擎`html/template`来渲染，详细语法可参考[官方文档](https://godoc.org/html/template)与[《Go WEB编程》模板处理章节](https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/07.4.md)