---
name: 下载安装
---

# 下载安装
## waf安装
### centos平台

从[openresty](http://openresty.org/en/download.html)官方下载最新版本的源码包。

编译安装openresty：

```bash
yum -y install pcre pcre-devel
wget https://openresty.org/download/openresty-1.9.15.1.tar.gz
tar -zxvf openresty-1.9.15.1.tar.gz 
cd openresty-1.9.15.1
./configure 
gmake && gmake install

/usr/local/openresty/nginx/sbin/nginx  -t
nginx: the configuration file /usr/local/openresty/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/openresty/nginx/conf/nginx.conf test is successful
/usr/local/openresty/nginx/sbin/nginx 
```

安装luarocks

```bash
wget http://keplerproject.github.io/luarocks/releases/luarocks-2.3.0.tar.gz
tar -zxvf luarocks-2.3.0.tar.gz 
cd luarocks-2.3.0
./configure --prefix=/usr/local/openresty/luajit \
--with-lua=/usr/local/openresty/luajit/ \
--lua-suffix=jit-2.1.0-beta2 \
--with-lua-include=/usr/local/openresty/luajit/include/luajit-2.1
make build && make install
yum -y install sqlite sqlite-devel unzip 
/usr/local/openresty/luajit/bin/luarocks install luafilesystem 
```

### ubuntu平台安装

编译安装openresty：

```bash
apt-get install libreadline-dev libncurses5-dev libpcre3-dev libssl-dev perl make build-essential
sudo ln -s /sbin/ldconfig /usr/bin/ldconfig
wget https://openresty.org/download/openresty-1.9.15.1.tar.gz
tar -zxvf openresty-1.9.15.1.tar.gz
cd openresty-1.9.15.1
make && sudo make install
```
安装luarocks
```bash
wget http://keplerproject.github.io/luarocks/releases/luarocks-2.3.0.tar.gz
tar -zxvf luarocks-2.3.0.tar.gz 
cd luarocks-2.3.0
./configure --prefix=/usr/local/openresty/luajit \
--with-lua=/usr/local/openresty/luajit/ \
--lua-suffix=jit-2.1.0-beta2 \
--with-lua-include=/usr/local/openresty/luajit/include/luajit-2.1
make build && make install
yum -y install sqlite sqlite-devel unzip 
/usr/local/openresty/luajit/bin/luarocks install luafilesystem 
```

## 安装waf管理后台x-waf-admin

### 二进制安装

直接从github中下载对应操作系统的二进制版本，https://github.com/xsec-lab/x-waf-admin/releases

### 源码安装

- 首先需要搭建好go语言开发环境，可以参考[Go Web 编程](https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/01.1.md)
- 安装依赖包

```bash
go get gopkg.in/macaron.v1
go get gopkg.in/ini.v1
go get github.com/go-sql-driver/mysql
go get github.com/go-xorm/xorm
go get github.com/xsec-lab/x-waf-admin
```

- 从github中下载最新的版本
- 执行go build server.go编译出二进制版本，然后将server、conf、publib和templates目录一起打包上传到服务器中即可运行。
