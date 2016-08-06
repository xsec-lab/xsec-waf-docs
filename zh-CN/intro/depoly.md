---
name: 部署配置
---

# 部署配置
## waf部署与配置
### openresty的配置

将x-waf的代码目录放置到openresty的`/usr/local/openresty/nginx/conf`目录下，然后在openresty的conf的目录下新建vhosts目录

```bash
cd /usr/local/openresty/nginx/conf/
git clone https://github.com/xsec-lab/x-waf
mkdir -p /usr/local/openresty/nginx/conf/vhosts
```
以下为openresty的配置范例：

```ini
user  nginx;
worker_processes auto;
worker_cpu_affinity auto;

#error_log  logs/error.log;
#error_log  logs/error.log  debug;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  409600;
}

http {
    include       mime.types;
    lua_package_path "/usr/local/openresty/nginx/conf/x-waf/?.lua;/usr/local/lib/lua/?.lua;;";
    lua_shared_dict limit 100m;
    lua_shared_dict badGuys 100m;
    default_type  application/octet-stream;

	#开启lua代码缓存功能
	lua_code_cache on;

	init_by_lua_file /usr/local/openresty/nginx/conf/x-waf/init.lua;
    access_by_lua_file /usr/local/openresty/nginx/conf/x-waf/access.lua;

	#log_format shield_access    '$remote_addr - $http_host - "$request" - "$http_cookie"';
	#access_log pipe:/usr/local/shield/redisclient shield_access;
    
    #ssl on;
    #ssl_certificate certs/cert_chain.crt;
    #ssl_certificate_key certs/server.key;
    ssl_session_timeout     5m;
    ssl_protocols SSLv2 SSLv3 TLSv1;
    ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
    ssl_prefer_server_ciphers on;


    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;
 
    #gzip  on;
    include vhosts/*.conf;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        	}
        }
}

```
### waf的配置

waf的配置文件位于`/usr/local/openresty/nginx/conf/waf/config.lua`中，详细的配置项如下：

```lua

-- WAF config file, enable = "on", disable = "off"

local _M = {
    -- waf status
    config_waf_enable = "on",
    -- log dir
    config_log_dir = "/tmp/waf_logs",
    -- rule setting
    config_rule_dir = "/usr/local/openresty/nginx/conf/x-waf/rules",
    -- enable/disable white url
    config_white_url_check = "on",
    -- enable/disable white ip
    config_white_ip_check = "on",
    -- enable/disable block ip
    config_black_ip_check = "on",
    -- enable/disable url filtering
    config_url_check = "on",
    -- enalbe/disable url args filtering
    config_url_args_check = "on",
    -- enable/disable user agent filtering
    config_user_agent_check = "on",
    -- enable/disable cookie deny filtering
    config_cookie_check = "on",
    -- enable/disable cc filtering
    config_cc_check = "on",
    -- cc rate the xxx of xxx seconds
    config_cc_rate = "10/60",
    -- enable/disable post filtering
    config_post_check = "on",
    -- config waf output redirect/html/jinghuashuiyue
    config_waf_model = "html",
    -- if config_waf_output ,setting url
    config_waf_redirect_url = "http://xsec.io",
    config_expire_time = 600,
    config_output_html=[[
    <html>
    <head>
    <meta charset="UTF-8">
    <title>xsec waf</title>
    <style type="text/css">
        body {
      font-family: "Helvetica Neue", Helvetica, Arial;
      font-size: 14px;
      line-height: 20px;
      font-weight: 400;
      color: #3b3b3b;
      -webkit-font-smoothing: antialiased;
      font-smoothing: antialiased;
      background: #f6f6f6;
    }
    .wrapper {
      margin: 0 auto;
      padding: 40px;
      max-width: 980px;
    }
    .table {
      margin: 0 0 40px 0;
      box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
      display: table;
    }
    @media screen and (max-width: 580px) {
      .table {
        display: block;
      }
    }
    .row {
      display: table-row;
      background: #f6f6f6;
    }
    .row:nth-of-type(odd) {
      background: #e9e9e9;
    }
    .row.header {
      font-weight: 900;
      color: #ffffff;
      background: #ea6153;
    }
    .row.green {
      background: #27ae60;
    }
    .row.yellow {
      background: #FF8C00;
    }
    @media screen and (max-width: 580px) {
      .row {
        padding: 8px 0;
        display: block;
      }
    }
    .cell {
      padding: 6px 12px;
      display: table-cell;
    }
    @media screen and (max-width: 580px) {
      .cell {
        padding: 2px 12px;
        display: block;
      }
    }
    </style>
    </head>
      <body>
        <div class="wrapper">
      <div class="table">
        <div class="row header yellow">
          <div class="cell">
            您的IP为 %s
          </div>
          <div class="cell">
            欢迎在遵守白帽子道德准则的情况下进行安全测试。
          </div>
          <div class="cell">
            联系方式：x@xsec.io
          </div>
        </div>
      </div>
    </div>
      </body>
    </html>
    ]],
}

return _M

```

### waf测试

使用root权限执行以下命令测试配置文件的正确性，如果测试结果返回ok则表示配置是正确的。

```bash
$ sudo /usr/local/openresty/nginx/sbin/nginx -t
[sudo] hartnett 的密码：
nginx: the configuration file /usr/local/openresty/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/openresty/nginx/conf/nginx.conf test is successful
```

如果配置文件正常，可以通过以下命令正式启动waf

```bash
$ sudo /usr/local/openresty/nginx/sbin/nginx
```

### WAF防御效果测试

在服务器中提交`curl http://127.0.0.1/\?id\=1%20union%20select%201,2,3`，
如果返回的内容中包含`欢迎在遵守白帽子道德准则的情况下进行安全测试`等字样就表示waf已经在正常运行了。


## waf-admin配置

waf-admin需要mysql的支持，事先需要准备一个mysql数据库的账户，以下为app.ini的配置范例：

```ini
RUN_MODE = dev
;RUN_MODE = prod

[server]
HTTP_PORT = 5000
API_KEY = xsec.io||secdevops.cn
NGINX_BIN = /usr/local/openresty/nginx/sbin/nginx
NGINX_VHOSTS = /usr/local/openresty/nginx/conf/vhosts/
API_SERVERS = 127.0.0.1, 8.8.8.8

[database]
USER = waf-admin
PASSWD = passw0rd
HOST = mysqlhost:3306
NAME = waf

[waf]
RULE_PATH = /usr/local/openresty/nginx/conf/waf/rules/
```
配置完成后在当前目录执行./server测试程序是否可以正常启动，waf-admin需要操作nginx的master进程，所以需要以root权限启动。

可以使用supversisor、nohup、systemd等将waf-admin跑在后台。