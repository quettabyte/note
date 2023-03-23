# Nginx笔记

# 一、初步了解Nginx

## 1、Nginx的优点

```
1、高并发，高性能
2、可扩展性好
3、高可靠性
4、热部署
5、BSD许可证
```

## 2、Nginx的四个主要组成部分

```
1）Nginx二进制可执行文件
	由各模块源码编译出的一个文件
2）Nginx.conf配置文件
	控制Nginx的行为
3）access.log访问日志
	记录每一条http请求信息
4）error.log错误日志
	定位问题
```

## 3、Nginx命令行

```
格式： nginx -s reload
帮助：-？ -h
使用指定的配置文件：-c
指定运行目录：-p
发送信号：-s
		立刻停止服务：stop
		优雅的停止服务：quit
		重载配置文件：reload
		重新开始记录日志文件：reopen
测试配置文件是否有语法错误：-t -T
打印nginx的版本信息、编译信息等：-v -V
```

### 依赖的安装包

```
yum install -y gcc zlib-devel pcre-devel openssl-devel libxml2 libxslt-devel 
```

```
wget http://luajit.org/download/LuaJIT-2.0.5.tar.gz
tar xf LuaJIT-2.0.5.tar.gz 
cd LuaJIT-2.0.5
make && make install PREFIX=/app/LuaJIT
echo "export LUAJIT_LIB=/app/LuaJIT/lib" >> /etc/profile
echo "export LUAJIT_INC=/app/LuaJIT/include/luajit-2.0" >> /etc/profile
source /etc/profile 
echo "/app/LuaJIT/lib" >> /etc/ld.so.conf
ldconfig 
```

### 安装三方模块

```
nginx Lua模块:
FTP服务器：wget ftp.lan.gyyx.cn/sre/nginx/lua/v0.10.9rc7.tar.gz
GitHub(最新版)：wget https://github.com/openresty/lua-nginx-module.git

ngx_devel_kit包
GitHub(0.3.0):https://github.com/vision5/ngx_devel_kit/archive/refs/tags/v0.3.0.tar.gz
GitHub(0.3.2):https://github.com/vision5/ngx_devel_kit/archive/refs/tags/v0.3.2.tar.gz


以上的第三方包解压缩，找好存放目录，
本文中使用的是nginx Lua：0.10.9rc7 与 ngx_devel_kit:0.3.0
```

### 编译

```
tengine：https://github.com/alibaba/tengine/archive/refs/tags/2.4.0.tar.gz
本文使用的tengine版本为2.4.0
```



### 案例：

### Nginx的重载、热部署、日志切割

#### 1）重载配置文件

```
打开tcp_nopush on;
#./nginx -s reload
```

#### 2）热部署

```
首先备份sbin/nginx
找到编译好新版本的nginx二进制文件：objs/nginx
替换旧的nginx
给nginx的master进程发送“kill -USR2 nginx的master的pid”

备份nginx日志
重新生成nginx日志 #nginx -s reopen
后期可以定制计划任务脚本来收集日志
```

## 4、Nginx配置文件

#### 1）定义访问日志格式

```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
                      

$remote_addr	客户端IP地址
$remote_user	客户端用户
$time_local		访问时间
$request		访问请求(请求方法   文件名称    HTTP协议版本)
$status			状态码
$body_bytes_sent	响应数据大小
$http_referer		超链接地址
$http_user_agent	浏览器类型
```

#### 2）访问日志存放位置

```
access_log  logs/access.log  main;

可以通过GoAccess来查看access日志
#goaccess access.log -o 指定生成的html文件 --real-time-html --time-format='%H:%M:%S' --date-format='%d/%b/%Y' --log-format=COMBINED
server{
	listen 端口号；
	access_log logs/access.log main;
	location /report.html {
		alias 重定向html位置；
	}
}
```

#### 4) 长连接超时时间

```
keepalive_timeout  65;
```

#### 5) 限制长连接所能发送的最大请求数 

```
keepalive_requests 100;
```

#### 6) 启用gzip压缩 

```
gzip  on;
```

#### 7) 启用sendfile机制，加快nginx工作效率

```
sendfile        on;

默认的行为是内核获取到文件内容，将内容加载到内核对应的内存，再从内核内存拷贝nginx对应的内存空间，比较耗时
sendfile机制：
    由内核直接将内容复制到nginx进程对应的内容，节省中间拷贝的时间 
```

## 5、Nginx反向代理

```
upstream local{
		server x.x.x.x:XXXX;
}
server {
	server_name 域名、IP；
	listen 监听端口；
	location / {
	proxy_set_header Host $host;
	proxy_set_header X-Real $remote_add;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	
	proxy_cache my_cache;	#控制缓存
	
	proxy_cache_key $host$uri$is_args$args;
	proxy_cache_valid 200 304 302 1d;
	proxy_pass 代理服务器地址；#将所有请求“ / ”位置传递到 “代理服务器地址”。
	}
}




————————————————————————————————————————————————————————————-----------------------------------------------------------------
	*****控制缓存*****
http{
****
	proxy_cache_path /tmp/nginxcache levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m use_temp_path=off;
****
}

```

## 6、源码编译安装Nginx时configure的详解

```
[root@localhost nginx-1.22.1]# ./configure --help

  --help                             print this message

  --prefix=PATH                      set installation prefix    #设置安装路径
  --sbin-path=PATH                   set nginx binary pathname	#设置Nginx二进制目录
  --modules-path=PATH                set modules path			#设置Nginx模块目录
  --conf-path=PATH                   set nginx.conf pathname	#设置Nginx配置文件目录
  --error-log-path=PATH              set error log pathname		#设置错误日志目录
  --pid-path=PATH                    set nginx.pid pathname		#设置pid进程目录
  --lock-path=PATH                   set nginx.lock pathname	#设置lock目录

  --user=USER                        set non-privileged user for	#设置Nginx启动时使用的用户
                                     worker processes
  --group=GROUP                      set non-privileged group for	#设置Nginx用户组
                                     worker processes

  --build=NAME                       set build name				#设置编译Nginx时的名字
  --builddir=DIR                     set build directory		#设置编译Nginx目录

  --with-select_module               enable select module		#选择启用的模块
  --without-select_module            disable select module		#选择禁用的模块
  --with-poll_module                 enable poll module			## 设置是否将poll()方法模块编译进nginx中。如果系统平台不支持kqueue、epoll、rtsig或/dev/poll等更合适的方法， 该模块会被自动编译。
  --without-poll_module              disable poll module		###

  --with-threads                     enable thread pool support #启用线程池

  --with-file-aio                    enable file AIO support	#启用异步文件IO

  --with-http_ssl_module             enable ngx_http_ssl_module  					#启用nginx_ssl模块（https）
  --with-http_v2_module              enable ngx_http_v2_module						#启用nginx_v2模块（http2.0）
  --with-http_realip_module          enable ngx_http_realip_module					#启用获取真实IP模块
  --with-http_addition_module        enable ngx_http_addition_module				#启用nginx 响应之前和之后添加文本，默认情况下未构建此模块###https://blog.51cto.com/u_15127521/2658080
  --with-http_xslt_module            enable ngx_http_xslt_module					#一个过滤器，可使用一个或多个XSLT样式表来转换XML响应，默认不够建此模块，这个模块需要libxml2和libxslt库
  --with-http_xslt_module=dynamic    enable dynamic ngx_http_xslt_module			#启用动态ngx_http_xslt模块
  --with-http_image_filter_module    enable ngx_http_image_filter_module			#启用ngx_http_image_filter_module模块；是一个过滤器，它可以对JPEG,GIF和PNG等图像进行变换。 
  --with-http_image_filter_module=dynamic
                                     enable dynamic ngx_http_image_filter_module 	#启用动态ngx_http_image_filter_module模块
  --with-http_geoip_module           enable ngx_http_geoip_module					#启用ngx_http_geoip模块，使用预编译的MaxMind数据库解析客户端IP地址，得到变量值。默认不编译。
  --with-http_geoip_module=dynamic   enable dynamic ngx_http_geoip_module			#启用动态ngx_http_geoip模块
  --with-http_sub_module             enable ngx_http_sub_module						#ngx_http_sub_module模块是一个过滤器，它通过将一个指定的字符串替换为另一个字符串来修改响应。这个模块不是默认构建的。
  --with-http_dav_module             enable ngx_http_dav_module						#ngx_http_dav_module模块处理HTTP和WebDAV方法PUT, DELETE, MKCOL, COPY和MOVE。这个模块不是默认构建的。
  --with-http_flv_module             enable ngx_http_flv_module						#为Flash Video(FLV)文件 提供服务端伪流媒体支持，默认不构建。
  --with-http_mp4_module             enable ngx_http_mp4_module						#为H.264/AAC文件，主要是以 .mp4、.m4v、和.m4a为扩展名的文件， 提供伪流媒体服务端支持。默认不够建。
  --with-http_gunzip_module          enable ngx_http_gunzip_module					#为不支持“gzip”编码方法的客户端解压具有“Content-Encoding: gzip”头的响应。默认不构建。
  --with-http_gzip_static_module     enable ngx_http_gzip_static_module				#允许发送以“.gz”作为文件扩展名的预压缩文件，以替代发送普通文件。默认不构建。
  --with-http_auth_request_module    enable ngx_http_auth_request_module			#基于子请求结果实现客户端授权。默认不够建。
  --with-http_random_index_module    enable ngx_http_random_index_module			#在文件夹中随机选择一个文件作为默认页。默认不构建。
  --with-http_secure_link_module     enable ngx_http_secure_link_module				#检查请求链接的有效性。默认不构建。
  --with-http_degradation_module     enable ngx_http_degradation_module				#在低内存的情形下允许Nginx服务器返回444错误或204错误，可以起到灾备作用。默认不构建。
  --with-http_slice_module           enable ngx_http_slice_module					#可以看作是反向的字节范围请求头，将一个大文件分割成小块(字节范围)，同时允许使用动态gzip压缩。默认不构建。
  --with-http_stub_status_module     enable ngx_http_stub_status_module				#提供基本监控功能，默认不构建。
  

  --without-http_charset_module      disable ngx_http_charset_module				#关闭为响应头的“Contene-Type”添加指定的字符集。 但有限制：只能单向转换，从服务器到客户端；只有单字节字符集能被转换；或者单字节字符集和UTF-8之间的互相转换。
  --without-http_gzip_module         disable ngx_http_gzip_module					#关闭gzip压缩。
  --without-http_ssi_module          disable ngx_http_ssi_module					#关闭处理SSI(服务器端包含)命令的过滤器在经过它的响应中。目前，支持的SSI命令列表不完整。
  --without-http_userid_module       disable ngx_http_userid_module					#关闭设置适合客户端识别的cookie。接收和设置的cookie可以使用$uid_got获得的嵌入式变量进行记录$uid_set。该模块与Apache的mod_uid模块兼容。
  --without-http_access_module       disable ngx_http_access_module					#关闭限制某些IP地址的客户端访问.
  --without-http_auth_basic_module   disable ngx_http_auth_basic_module				#关闭使用“HTTP基本认证”协议验证用户名和密码来限制对资源的访问。 
  --without-http_mirror_module       disable ngx_http_mirror_module					#关闭用来实现流量拷贝。将生产环境的流量拷贝到预上线环境或测试环境。
  --without-http_autoindex_module    disable ngx_http_autoindex_module				#关闭可以列出目录中的文件。 一般当ngx_http_index_module模块找不到默认主页的时候，会把请求转给 ngx_http_autoindex_module模块去处理。
  --without-http_geo_module          disable ngx_http_geo_module					#关闭创建变量，并根据客户端IP地址对变量赋值。 
  --without-http_map_module          disable ngx_http_map_module					#关闭可以创建一些和另外变量相关联的变量。
  --without-http_split_clients_module disable ngx_http_split_clients_module			#关闭创建适合于A/B测试（也叫做分离测试）的变量。
  --without-http_referer_module      disable ngx_http_referer_module				 #关闭拦截“Referer”请求头中含有非法值的请求，阻止它们访问站点。（预防大规模的非法请求；还有一点需要注意，即使正常浏览器发送的合法请求，也可能没有“Referer”请求头。 ）
  --without-http_rewrite_module      disable ngx_http_rewrite_module				#关闭允许正则替换URI，返回页面重定向，和按条件选择配置。 
  --without-http_proxy_module        disable ngx_http_proxy_module					#关闭允许传送请求到其它服务器。
  --without-http_fastcgi_module      disable ngx_http_fastcgi_module				#关闭允许传递请求到FastCGI服务器。（FastCGI：快速通用网关接口）
  --without-http_uwsgi_module        disable ngx_http_uwsgi_module					#关闭允许将请求传递到uwsgi服务器。（UWSGI：Web服务器，它实现了WSGI协议、uwsgi、http等协议，把HTTP协议转化成语言支持的网络协议供python使用。Nginx中HttpUwsgiModule的作用是与uWSGI服务器进行交换。）
  --without-http_scgi_module         disable ngx_http_scgi_module					#关闭允许将请求传递到scgi服务器。（SCGI：简单通用网关接口）
  --without-http_grpc_module         disable ngx_http_grpc_module					#关闭将请求传递给 gRPC 服务器
  --without-http_memcached_module    disable ngx_http_memcached_module				#关闭从memcached服务器上获取响应。
  --without-http_limit_conn_module   disable ngx_http_limit_conn_module				#关闭设定单一IP来源的连接数。 
  --without-http_limit_req_module    disable ngx_http_limit_req_module				#关闭限制来自单个IP地址的请求处理频率。
  --without-http_empty_gif_module    disable ngx_http_empty_gif_module				#关闭返回一个透明像素的GIF图片。
  --without-http_browser_module      disable ngx_http_browser_module				#关闭创建变量，这个模块的值取决于请求头中的“User-Agent”的值。
  --without-http_upstream_hash_module
                                     disable ngx_http_upstream_hash_module			#关闭一致性哈希。（模块作用：可减少缓存数据的失效。）
  --without-http_upstream_ip_hash_module
                                     disable ngx_http_upstream_ip_hash_module		#关闭IP哈希。（模块作用：保持会话）
  --without-http_upstream_least_conn_module
                                     disable ngx_http_upstream_least_conn_module	#关闭最少连接数。（模块作用：可均分连接）
  --without-http_upstream_random_module
                                     disable ngx_http_upstream_random_module		#关闭加权轮询。（模块作用：可以均分请求，是默认的HTTP负载均衡算法，集成在框架中）
  --without-http_upstream_keepalive_module
                                     disable ngx_http_upstream_keepalive_module		#关闭后端长连接超时功能
  --without-http_upstream_zone_module
                                     disable ngx_http_upstream_zone_module			#关闭使用共享内存使负载均衡策略对所有worker进程生效

  --with-http_perl_module            enable ngx_http_perl_module					#开启在Perl中实现位置和变量处理程序，并将Perl调用插入到SSI中。
  --with-http_perl_module=dynamic    enable dynamic ngx_http_perl_module			#开启动态在Perl中实现位置和变量处理程序，并将Perl调用插入到SSI中。
  --with-perl_modules_path=PATH      set Perl modules path							#设置Perl模块的路径
  --with-perl=PATH                   set perl binary pathname						#设置Perl模块二进制文件路径

  --http-log-path=PATH               set http access log pathname					#设置http的access日志文件路径
  --http-client-body-temp-path=PATH  set path to store								
                                     http client request body temporary files		#设置http连接的请求实体临时文件设置路径
  --http-proxy-temp-path=PATH        set path to store						
                                     http proxy temporary files						#设置http代理临时目录
  --http-fastcgi-temp-path=PATH      set path to store
                                     http fastcgi temporary files					#设置FastCGI的临时目录
  --http-uwsgi-temp-path=PATH        set path to store
                                     http uwsgi temporary files						#设置uwsgi的临时目录
  --http-scgi-temp-path=PATH         set path to store	
                                     http scgi temporary files						#设置scgi的临时目录

  --without-http                     disable HTTP server							#关闭http服务
  --without-http-cache               disable HTTP cache								#关闭http缓存

  --with-mail                        enable POP3/IMAP4/SMTP proxy module			#开启邮件服务
  --with-mail=dynamic                enable dynamic POP3/IMAP4/SMTP proxy module	#开启动态邮件服务
  --with-mail_ssl_module             enable ngx_mail_ssl_module						#开启ssl协议的邮件服务
  --without-mail_pop3_module         disable ngx_mail_pop3_module					#关闭pop3协议
  --without-mail_imap_module         disable ngx_mail_imap_module					#关闭imap协议
  --without-mail_smtp_module         disable ngx_mail_smtp_module					#关闭smtp协议

  --with-stream                      enable TCP/UDP proxy module					#开启TCP/UDP代理模块
  --with-stream=dynamic              enable dynamic TCP/UDP proxy module			#动态开启TCP/UDP代理模块
  --with-stream_ssl_module           enable ngx_stream_ssl_module					#开启流代理服务器与SSL / TLS协议工作必要的支持。默认不构建。
  --with-stream_realip_module        enable ngx_stream_realip_module				#将客户端地址和端口更改为在PROXY协议报头中发送的地址和端口。
  --with-stream_geoip_module         enable ngx_stream_geoip_module					#依赖于客户端的IP地址值，并使用预编译的创建变量的MaxMind数据库。默认不构建
  --with-stream_geoip_module=dynamic enable dynamic ngx_stream_geoip_module			#动态开启
  --with-stream_ssl_preread_module   enable ngx_stream_ssl_preread_module			#提取所述信息的ClientHello而不终止SSL / TLS。默认不构建。
  --without-stream_limit_conn_module disable ngx_stream_limit_conn_module			#限制每个定义的键连接的数量，尤其是单一的IP地址的连接数量。
  --without-stream_access_module     disable ngx_stream_access_module				#限制访问某些客户端地址。
  --without-stream_geo_module        disable ngx_stream_geo_module					#该模块取决于客户端的IP地址值的变量。
  --without-stream_map_module        disable ngx_stream_map_module					#该模块创建变量，其值依赖于其他变量的值。
  --without-stream_split_clients_module
                                     disable ngx_stream_split_clients_module		#该模块创建一个适合于A / B测试，也称为分割测试变量。
  --without-stream_return_module     disable ngx_stream_return_module				#该模块允许发送一个指定的值给客户端，然后关闭连接。
  --without-stream_set_module        disable ngx_stream_set_module					#该模块允许为变量设置值。
  --without-stream_upstream_hash_module
                                     disable ngx_stream_upstream_hash_module		#关闭upstream选项哈希模块。
  --without-stream_upstream_least_conn_module
                                     disable ngx_stream_upstream_least_conn_module	#关闭upstream选项上游最少连接数。
  --without-stream_upstream_random_module
                                     disable ngx_stream_upstream_random_module		#关闭负载均衡中的轮询。
  --without-stream_upstream_zone_module
                                     disable ngx_stream_upstream_zone_module		#关闭负载均衡中的共享内存

  --with-google_perftools_module     enable ngx_google_perftools_module				#用来支持Google PerTools的使用的
  --with-cpp_test_module             enable ngx_cpp_test_module						#测试程序中引用的头文件是否与C++兼容

  --add-module=PATH                  enable external module							#启用外部模块
  --add-dynamic-module=PATH          enable dynamic external module					#启用动态外部模块

  --with-compat                      dynamic modules compatibility					#动态模块兼容性

  --with-cc=PATH                     set C compiler pathname						#设置c预处理器路径名
  --with-cpp=PATH                    set C preprocessor pathname					#设置c++预处理器路径名
  --with-cc-opt=OPTIONS              set additional C compiler options				#设置额外的C编译器选项
  --with-ld-opt=OPTIONS              set additional linker options					#设置其他链接器选项
  --with-cpu-opt=CPU                 build for the specified CPU, valid values:
                                     pentium, pentiumpro, pentium3, pentium4,
                                     athlon, opteron, sparc32, sparc64, ppc64		#指定CPU构建

  --without-pcre                     disable PCRE library usage						#关闭PCRE库文件使用
  --with-pcre                        force PCRE library usage						#强制使用PCRE库文件
  --with-pcre=DIR                    set path to PCRE library sources				#设置PCRE库文件目录
  --with-pcre-opt=OPTIONS            set additional build options for PCRE			#PCRE额外的构建选项
  --with-pcre-jit                    build PCRE with JIT compilation support		#构建时用JIT编译支持构建PCRE
  --without-pcre2                    do not use PCRE2 library						#不使用PCRE2的库文件

  --with-zlib=DIR                    set path to zlib library sources				#设置zlib的库文件目录
  --with-zlib-opt=OPTIONS            set additional build options for zlib			#zlib额外的构建选项
  --with-zlib-asm=CPU                use zlib assembler sources optimized
                                     for the specified CPU, valid values:
                                     pentium, pentiumpro							#使用为指定CPU优化的zlib汇编程序源。

  --with-libatomic                   force libatomic_ops library usage				#强制使用libatomic_ops。
  --with-libatomic=DIR               set path to libatomic_ops library sources		#设置libatomic的目录。

  --with-openssl=DIR                 set path to OpenSSL library sources			#设置OpenSSL的库文件目录。
  --with-openssl-opt=OPTIONS         set additional build options for OpenSSL		#为OpenSSL设置额外的构建选项。

  --with-debug                       enable debug logging							#启用调试日志记录。


```

## 7、解析业务上编译的nginx

```
./configure --prefix=/usr/local/tengine  \  #安装路径“/usr/local/tengine”
--user=root \								#启动用户“root”
--group=root \								#用户组“root”
--with-http_ssl_module \					#启用nginx_ssl模块（开启https）
--with-http_flv_module \					#为Flash Video(FLV)文件 提供服务端伪流媒体支持。
--with-file-aio \							#启用异步文件IO
--with-http_realip_module \					#启用获取真实IP
--with-http_addition_module \				#启用nginx 响应之前和之后添加文本
--with-http_xslt_module \					#一个过滤器，可使用一个或多个XSLT样式表来转换XML响应
--with-http_sub_module \					#是一个过滤器，它通过将一个指定的字符串替换为另一个字符串来修改响应。
--with-http_dav_module \					#处理HTTP和WebDAV方法PUT, DELETE, MKCOL, COPY和MOVE。
--with-http_gunzip_module \					#为不支持“gzip”编码方法的客户端解压具有“Content-Encoding: gzip”头的响应。
--with-http_gzip_static_module \			#允许发送以“.gz”作为文件扩展名的预压缩文件，以替代发送普通文件。
--with-http_secure_link_module \			#检查请求链接的有效性。
--with-http_degradation_module \			#在低内存的情形下允许Nginx服务器返回444错误或204错误，可以起到灾备作用。
--with-http_stub_status_module  \			#提供基本监控功能
--with-debug \								#启用调试日志记录。
--with-stream \								#开启负载均衡	
--add-module=/usr/local/src/lua-nginx-module-0.10.9rc7/ \	#添加luajit模块以支撑tengine
--add-module=/usr/local/src/ngx_devel_kit-0.3.0				#添加kit依赖
```

## 8、解析原nginx.conf文件

```
#启动用户
#user  nobody;
#启动时开启的worker线程数量，cat /proc/cpuinfo |grep "cores"   查看线程数量  
worker_processes  1;

#定义错误日志格式以及路径
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
#error_log  "pipe:rollback logs/error_log interval=1d baknum=7 maxsize=2G";


#存放nginx的pid文件位置
#pid        logs/nginx.pid;

#事件驱动模型配置
events {
	#每个工作进程所能接受的最大连接数
    worker_connections  1024;
}

#http服务相关配置
http {
		#子配置文件
		include       mime.types;
		default_type  application/octet-stream;
		
		#定义访问日志格式
		#log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
		#                  '$status $body_bytes_sent "$http_referer" '
		#                  '"$http_user_agent" "$http_x_forwarded_for"';
		#$remote_addr	客户端IP地址
		#$remote_user	客户端用户
		#$time_local	访问时间
		#$request		访问请求(请求方法   文件名称    HTTP协议版本)
		#$status			状态码
		#$body_bytes_sent	响应数据大小
		#$http_referer		超链接地址
		#$http_user_agent	浏览器类型
		#access_log  logs/access.log  main;
		#access_log  "pipe:rollback logs/access_log interval=1d baknum=7 maxsize=2G"  main;
		
		#启动sendfile机制，加快nginx工作效率
		sendfile        on;
		#该指令必须在sendfile打开的状态下才会生效，主要是用来提升网络包的传输效率。
		#tcp_nopush     on;
		
		#长连接超时
		#keepalive_timeout  0;
		keepalive_timeout  65;
		
		#开启gzip压缩。
		#gzip  on;
		#
		#进阶用法
		#gzip on; 				#开启gzip功能 
		#gzip_types *; 			#压缩源文件类型,根据具体的访问资源类型设定
		#gzip_comp_level 6; 	#gzip压缩级别 
		#gzip_min_length 1024; 	#进行压缩响应页面的最小长度,content-length 
		#gzip_buffers 4 16K; 	#缓存空间大小 
		#gzip_http_version 1.1; #指定压缩响应所需要的最低HTTP请求版本
		#gzip_vary on; 			#往头信息中添加压缩标识 
		#gzip_disable "MSIE [1-6]\."; #对IE6以下的版本都不进行压缩 
		#gzip_proxied off； 	#nginx作为反向代理压缩服务端返回数据的条件 

		
		
		#http服务相关配置
		server {
			#监听端口
			listen       80;
			#指定网站名称
			server_name  localhost;
			#设置字符集
			#charset koi8-r;
			#访问日志的格式以及存放路径
			#access_log  logs/host.access.log  main;
			#access_log  "pipe:rollback logs/host.access_log interval=1d baknum=7 maxsize=2G"  main;
			
			#匹配不同的客户端请求
			location / {
				#响应方式
				root   html;
				index  index.html index.htm;
			}
			
			#error_page  404              /404.html;
		
			# redirect server error pages to the static page /50x.html
			#
			#遇到http状态码返回指定页面
			error_page   500 502 503 504  /50x.html;
			location = /50x.html {
				root   html;
			}
		
			# proxy the PHP scripts to Apache listening on 127.0.0.1:80
			#
			#location ~ \.php$ {
			#    proxy_pass   http://127.0.0.1;
			#}
		
			# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
			#
			
			#location ~ \.php$ {
			#    root           html;
			#    fastcgi_pass   127.0.0.1:9000;
			#    fastcgi_index  index.php;
			#    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
			#    include        fastcgi_params;
			#}
		
			# pass the Dubbo rpc to Dubbo provider server listening on 127.0.0.1:20880
			#
			#location /dubbo {
			#    dubbo_pass_all_headers on;
			#    dubbo_pass_set args $args;
			#    dubbo_pass_set uri $uri;
			#    dubbo_pass_set method $request_method;
			#
			#    dubbo_pass org.apache.dubbo.samples.tengine.DemoService 0.0.0 tengineDubbo dubbo_backend;
			#}
		
			# deny access to .htaccess files, if Apache's document root
			# concurs with nginx's one
			#
			#location ~ /\.ht {
			#    deny  all;
			#}
		}
		
		# upstream for Dubbo rpc to Dubbo provider server listening on 127.0.0.1:20880
		#
		#upstream dubbo_backend {
		#    multi 1;
		#    server 127.0.0.1:20880;
		#}
		
		# another virtual host using mix of IP-, name-, and port-based configuration
		#
		#
		#server {
		#    listen       8000;
		#    listen       somename:8080;
		#    server_name  somename  alias  another.alias;
		
		#    location / {
		#        root   html;
		#        index  index.html index.htm;
		#    }
		#}
		
		
		# HTTPS server
		#
		#server {
		#    listen       443 ssl;
		#    server_name  localhost;
			#CA证书密钥
		#    ssl_certificate      cert.pem;
		#    ssl_certificate_key  cert.key;
		
		#    ssl_session_cache    shared:SSL:1m;
		#    ssl_session_timeout  5m;
		
		#    ssl_ciphers  HIGH:!aNULL:!MD5;
		#    ssl_prefer_server_ciphers  on;
		
		#    location / {
		#        root   html;
		#        index  index.html index.htm;
		#    }
		#}

}

```

## 9、解析小飞侠nginx.conf配置文件

```
############ 20130917 10:49:28 ################
#启动用户为root用户
user  root; 

#启动时开启3个worker线程数量
worker_processes  3;
#绑定CPU内核
worker_cpu_affinity 0001 0010 0100;

#定义error级别的错误日志，日志文件在/data/logs/error.log
error_log   /data/logs/error.log error;

#nginx的pia文件存放在/var/run/nginx.pid
pid        /var/run/nginx.pid;

#worker同时打开的文件总和为102400
worker_rlimit_nofile  102400;

#事件驱动模型
events {
	#采用通知机制
    use epoll;
	##扩展：select（采用周期性查询，有文件数量的限制 1024）
	##扩展：poll（采用周期性查询，无文件数量限制）
 
	#每个工作进程所能接受的最大连接数为102400
	worker_connections  102400; 
}


#http服务配置
http {
	#子配置文件
    include       ./mime.types;
	default_type  application/octet-stream;
    #安全优化，隐藏版本号
    server_tokens off;
	
	#定义访问日志格式
    log_format  main  '$hostname|$remote_addr|-|[$time_local]|$server_name'
                      '|$request|$request_length|$request_time'
                      '|"$status"|$body_bytes_sent|$bytes_sent|$connection|$connection_requests'
                      '|"$http_user_agent"|"$http_referer"'
                      '|$upstream_addr|$upstream_status|$upstream_response_time|tx-$hostname-$server_addr';
						#$hostname,主机名|$remote_addr,客户端IP地址|$time_local,访问时间|$server_name,提供服务的主机名
						#$request,访问请求|$request_length,表示HTTP请求的长度,包括请求行、请求头和请求正文|
						#$request_time,从接受用户请求的第一个字节到发送完响应数据的时间|$status,状态码|
						#$body_bytes_sent,表示Nginx发送给客户端的字节数，不包括HTTP头的大小|
						#$bytes_sent,表示Nginx发送给客户端的字节数，包括HTTP头的大小|
						#$connection,记录Nginx服务器处理HTTP请求时与客户端的连接信息|
						#$connection_requests,通过当前链接获得的请求数量|
						#$http_user_agent,客户端使用的浏览器标识|
						#$http_referer,请求的页面来源URL，为空时表示没有源页面|
						#$upstream_addr,反向代理时记录实际提供服务的服务器地址|
						#$upstream_status,反向代理时记录实际提供服务的服务器状态|
						#$upstream_response_time,与后端服务器之间的响应时间，包括建立连接、发送请求、等待响应，接受响应等所有时间|
						#$hostname-$server_addr,记录请求是从哪个服务器发出的。其中 $hostname 变量是通过指令 server_name 定义的服务器名，如果没有定义则为主机名；$server_addr 变量是 Nginx 监听的 IP 地址。这个变量可以用于区分多个服务器之间的请求|
	
	
	
	#json格式的访问日志
    log_format  main_json '{"hostname":"$hostname",'
                          '"server_addr":"$server_addr",' 
                          '"remote_addr":"$remote_addr",'
                          '"client_ip":"$http_x_forwarded_for",'
                          '"time_local":"$time_local",'
                          '"server_name":"$server_name",'
                          '"request":"$request",'
                          '"request_uri":"$uri",'
                          '"request_args":"$args",'
                          '"request_method":"$request_method",'
                          '"server_protocol":"$server_protocol",'
                          '"request_length":"$request_length",'
                          '"request_time":"$request_time",'
                          '"status":"$status",'
                          '"body_bytes_sent":"$body_bytes_sent",'
                          '"bytes_sent":"$bytes_sent",'
                          '"connection":"$connection",'
                          '"connection_requests":"$connection_requests",'
                          '"http_user_agent":"$http_user_agent",'
                          '"http_referer":"$http_referer",'
                          '"upstream_addr":"$upstream_addr",'
                          '"upstream_status":"$upstream_status",'
                          '"upstream_response_time":"$upstream_response_time",'
                          '"tx-hostname-server_addr":"tx-$hostname-$server_addr",'
                          '"token":"$http_token",'
                          '"cookie":"$http_cookie"'
                          '}';
						#$http_x_forwarded_for,获取最原始用户IP
						#$uri,请求中的当前URI，可以不同于浏览器传递的$request_uri的值，它可以通过内部重定向，或者使用index指令进行修改，$uri不包含主机名
						#$args,请求中的参数值
						#$request_method,HTTP请求方法，通常为"GET"或"POST"
						#$server_protocol,服务器的HTTP版本，通常为 "HTTP/1.0" 或 "HTTP/1.1"
						#$http_token,token令牌
						#$http_cookie,cookie值
						
    log_format  temp0918  '$remote_addr|$remote_addr|"$http_mycheck"|[$time_local]|$server_name'
                      '|$request|$request_length|$request_time'
                      '|"$status"|$body_bytes_sent|$bytes_sent|$connection|$connection_requests'
                      '|"$http_user_agent"|"$http_referer"'
                      '|$upstream_addr|$upstream_status|$upstream_response_time|"42.62.122.71"|"$http_Authorization"';
    log_format  temp0920  '$remote_addr|$remote_addr|"$http_mycheck"|[$time_local]|$server_name'
                      '|$request|$request_length|$request_time'
                      '|"$status"|$body_bytes_sent|$bytes_sent|$connection|$connection_requests'
                      '|"$http_user_agent"|"$http_referer"'
                      '|$upstream_addr|$upstream_status|$upstream_response_time|"42.62.122.71"|"$request_body"';
    
    access_log  /data/logs/access.log main;
    access_log  /data/json_logs/access.log main_json;
	#设置请求行与请求头的大小为不超过32k
    client_header_buffer_size    32k;
	#配置请求行不能超过32k，否则返回414错误；请求头中的每一个头部字段的大小不能超过8K，否则返回400错误；这里请求行+请求头的大小不能超过4*32k=128K。
    large_client_header_buffers  4 32k;
    #配置多个虚拟主机时必须加的，否则nginx启动不了，大致是配置哈希表大小
	server_names_hash_bucket_size 128;
	#启动sendfile机制，加快nginx工作效率
    sendfile           	on;
	#该指令必须在sendfile打开的状态下才会生效，主要是用来提升网络包的传输效率。
    tcp_nopush         	on;
	#禁用Nagle算法，允许小包的发送。
    tcp_nodelay	       	on;
	#长连接超时
    keepalive_timeout	600;
	#开启gzip压缩
    gzip		on;
	#进行压缩响应页面的最小长度,content-length 
    gzip_min_length	1k; 
	#缓存空间大小
    gzip_buffers	4 16k;
	#压缩源文件类型,根据具体的访问资源类型设定
    gzip_types		text/plain application/x-javascript text/css text/javascript application/xml;
    #往头信息中添加压缩标识 
	gzip_vary		on;
	
	#配置静态文件不存在返回404页面
    fastcgi_intercept_errors on;
   server{
      listen 80 default;
      server_name _;
      return 403;
   }
#服务端
server{
	#监听端口号443
    listen 443 ssl;
    server_name _;
	#CA证书密钥存放位置
    ssl_certificate      ssl/testssl/ssl.crt;
    ssl_certificate_key  ssl/testssl/ssl.key;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    return 403;
   }
    
	#定义子配置文件
    include cosvhosts/servername/cd-slave-kernel.xfxpower.cn;
    include cosvhosts/servername/app-cd-slave.xfxpower.cn;
    include cosvhosts/servername/www-cd-slave.xfxpower.cn;

    include cosvhosts/upstream/kernel-slave-xfxpower-master;
    include cosvhosts/upstream/xfxpower-app-exchange-gateway-slave-master;
    include cosvhosts/upstream/xfxpower-external-exchange-gateway-slave-master;
    
    ########################冷备##########################
    include cosvhosts/servername/kernel.xfxpower.cn;
    include cosvhosts/servername/cd-kernel.xfxpower.cn;
    include cosvhosts/servername/xfxpower.cn;
    include cosvhosts/servername/app.xfxpower.cn;
    include cosvhosts/servername/alarm.xfxpower.cn;
    include cosvhosts/servername/api.xfxpower.cn;
    include cosvhosts/servername/api.test.xfxpower.cn;
    include cosvhosts/servername/internal-api.xfxpower.cn;

    include cosvhosts/upstream/kernel;    
    include cosvhosts/upstream/xfxpower-web-admin;
    include cosvhosts/upstream/xfxpower-static;
    include cosvhosts/upstream/xfxpower_static; 
    include cosvhosts/upstream/xfxpower-gateway;
    include cosvhosts/upstream/xfxpower-app; 
    include cosvhosts/upstream/xfxpower-activity-gateway;
    include cosvhosts/upstream/xfxpower-activity-app-gateway; 
    include cosvhosts/upstream/xfxpower-complain-user-gateway; 
    include cosvhosts/upstream/xfxpower-complain-admin-gateway; 
    include cosvhosts/upstream/xfxpower-api; 
    include cosvhosts/upstream/api-costaccounting.www.gxcospower.cn;
    include cosvhosts/upstream/cost-xfxpower-cn;
    include cosvhosts/upstream/xfxpower-app-exchange-gateway;
    include cosvhosts/upstream/xfxpower-external-exchange-gateway;
    include cosvhosts/upstream/xfxpower-internal-api;
    
    ###################### 冷备end #######################
    #########add 2023-01-03 suweipeng###########
    include cosvhosts/upstream/cos-power-customer-service-admin;
    include cosvhosts/upstream/cos-power-customer-service-web-admin-xfxpower-cn;
    include cosvhosts/upstream/cos-power-mobile-h5;

}

```

# 二、详解HTTP请求的11个阶段

## 1、11个阶段

```
POST_READ				realip
SERVER_REWRITE			rewrite
FIND_CONFIG				
REWRITE					rewrite
POST_REWRITE			
PREACCESS				limt_conn,limit_req
ACCESS					auth_basic,access,auth_request
POST_ACCESS
PRECONTENT				try_files
CONTENT					index,autoindex,concat
LOG						access_log
```

### 1)realip模块

#### a、POST_READ 获取用户的真实IP地址（realip）

```
1、TCP连接四元组（src ip, src port, dst ip, dst port）
2、HTTP头部X-Forwarded-For用于传递IP
3、HTTP头部X-Real-IP用于传递用户IP
4、网络中存在许多反向代理
```

```
默认不会编译进Nginx
	通过--with-http_realip_modules启用功能
功能：
	修改客户端地址
变量：
	realip_remote_addr
	realip_remote_port
指令：
	set_real_ip_from
	real_ip_header
	real_ip_recursive
```

#### a、案例real_ip_recursive开启、关闭：

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf]# vim ../conf.d/realip.conf 
server {
        server_name realip.com;
        error_log /data/logs/error.log debug;
        set_real_ip_from 121.43.224.178;
        #real_ip_header X-Real-IP;
        real_ip_recursive off;
        #real_ip_recursive on;
        real_ip_header X-Forwarded-For;

        location / {
                return 200 "Client real ip: $remote_addr\n";
        }
}
    
[root@iZbp18iw6uhl07x7b6k1ctZ conf]# vim nginx.conf
    include /usr/local/nginx/conf.d/*.conf;
```

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf]# curl -H 'X-Forwarded-For: 1.1.1.1,121.43.224.178' 121.43.224.178
Client real ip: 121.43.224.178
```

```
修改realip.conf配置文件
	#real_ip_recursive off;
    real_ip_recursive on;
检查配置文件语法以及重载配置文件
[root@iZbp18iw6uhl07x7b6k1ctZ conf]# /usr/local/nginx/sbin/nginx -t
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
[root@iZbp18iw6uhl07x7b6k1ctZ conf]# /usr/local/nginx/sbin/nginx -s reload 
```

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf]# curl -H 'X-Forwarded-For: 1.1.1.1,121.43.224.178' 121.43.224.178
Client real ip: 1.1.1.1
```



### 2)rewrite模块

#### a、return指令：

```
return code [text];
return code URL;
return URL;
```

```
返回状态码：
	Nginx自定义
		444:关闭连接
	HTTP 1.0标准
		301：http1.0永久重定向
		302：临时重定向，禁止被缓存。
	HTTP 1.1标准
		303：临时重定向，允许改变方法，禁止被缓存
		307：临时重定向，不允许改变方法，禁止被缓存
		308：永久重定向，不允许改变方法
```

```
error_page
	语法：error_page code ...[=[reponse]]uri;
例子：
	1、error_page 404/404.html;
	2、error_page 500 502 503 504 /50x.html;
	3、errpr_page 404=200 /empty.gif;
	4、error_page 404=/404.php;
	5、location / {
		error_page 404=@fallback;
	}
	location @fallback{
		proxy_pass http://backend;
	}
	6、error_page 403 http://example.com/forbidden.html;
	7、error_page 404=301 http://example.com/notfound.html;
```

##### a案例）

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat return.conf 
server {
	server_name return.com;
	listen 8080;
	root /data/html;
	
	error_page 404 /403.html;
	#return 405;
	location /{
		#return 404 "find nothing!\n";
	}
}
```

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:8080/aaa.txt
这是403页面
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:8080/sss/saaa.txt
这是403页面
```

```
修改配置文件，return 404 "find nothing!\n";

[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# /usr/local/nginx/sbin/nginx -s reload 
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:8080/sss/saaa.txt
find nothing!

修改配置文件，return 405;
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# /usr/local/nginx/sbin/nginx -s reload 
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:8080/sss/saaa.txt
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html>
<head><title>405 Not Allowed</title></head>
<body>
<center><h1>405 Not Allowed</h1></center>
 Sorry for the inconvenience.<br/>
```

#### b、rewrite指令

```
语法：
	rewrite regex replacement[flag];
	rewrite uri地址  新uri地址 [标志]; 

功能：
	将regex指定的url替换成replacement这个新的url
		--可以使用正则表达式及变量提取
	当replacement以http://或者$schema开头，则直接返回302重定向
	替换后的url根据flag指定的方式进行处理
		--last：用replacement这个url进行新的location匹配
		--break：break指令停止当前脚本指令的执行，等价于独立的break指令
		--redirect：返回302重定向
		--permanent：返回301重定向

注意事项: 

1、server, location, if 
2、rewrite可以存在多条, 依次进行处理 
3、旧uri地址支持正则表达式; 新uri支持反向引用
4、旧uri地址匹配客户端时，不包括请求中的参数 
5、支持变量的使用
```

##### a案例)

```
网页目录结构：
[root@iZbp18iw6uhl07x7b6k1ctZ html]# tree
.
├── first
│   └── 1.txt
├── second
│   └── 2.txt
└── third
    └── 3.txt
```

```
配置文件
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat return.conf
server {
	server_name return.com;
	listen 8080;
	root /data/html;
	
	location /first {
		rewrite /first(.*)/second$1 last;
		return 200 'first!';
	}
	location /second {
		rewrite /first(.*)/third$1 break;
		return 200 'second!';
	}
	location /third {
		return 200 'third!';
	}
}
```

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat return.conf 
server {
	server_name return.com;
	listen 8080;
	root /data/html;
	
	location /first {
		rewrite /first(.*) /second$1 last;
		return 200 'first!\n';
	}
	location /second {
		#rewrite /second(.*) /third$1 break;
		rewrite /second(.*) /third$1;
		return 200 'second!\n';
	}
	location /third {
		return 200 'third!\n';
	}
}
```

```
访问/first/3.txt
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:8080/first/3.txt
second!
因为当前location /second{}中rewrite /second(.*) /third$1;  没有加break；所以返回second。

修改配置文件 rewrite /second(.*) /third$1 break; 
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:8080/first/3.txt
test3
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat /data/html/third/3.txt 
test3

访问second/3.txt
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:8080/second/3.txt
test3

访问third/3.txt
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:8080/third/3.txt
third!
```

##### b案例）

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat return.conf 
server {
	server_name return.com;
	listen 8080;
	root /data/html;
	#rewrite_log on;
	#error_log /data/logs/rewrite_error.log notice;
	
	location /redirect1 {
		rewrite /redirect1(.*) $1 permanent;
	}
	location /redirect2 {
		rewrite /redirect2(.*) $1 redirect;
	}
	location /redirect3 {
		rewrite /redirect3(.*) http://rewrite.nhxn.com$1;
	}
	location /redirect4 {
		rewrite /redirect4(.*) http://rewrite.nhxn.com$1 permanent;
	}
}
```

```
访问rewrite.nhxn.com:8080/redirect1
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl -I rewrite.nhxn.com:8080/redirect1
HTTP/1.1 301 Moved Permanently
Server: Tengine
Date: Fri, 24 Feb 2023 06:41:41 GMT
Content-Type: text/html
Content-Length: 239
Connection: keep-alive
Location: 

访问rewrite.nhxn.com:8080/redirect2
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl -I rewrite.nhxn.com:8080/redirect2
HTTP/1.1 302 Moved Temporarily
Server: Tengine
Date: Fri, 24 Feb 2023 06:42:06 GMT
Content-Type: text/html
Content-Length: 215
Connection: keep-alive
Location: 

访问rewrite.nhxn.com:8080/redirect3
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl -I rewrite.nhxn.com:8080/redirect3
HTTP/1.1 302 Moved Temporarily
Server: Tengine
Date: Fri, 24 Feb 2023 06:42:09 GMT
Content-Type: text/html
Content-Length: 215
Connection: keep-alive
Location: http://rewrite.nhxn.com
解读：虽然没有指定permanent与redirect,但是写了http/https返回302

访问rewrite.nhxn.com:8080/redirect4
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl -I rewrite.nhxn.com:8080/redirect4
HTTP/1.1 301 Moved Permanently
Server: Tengine
Date: Fri, 24 Feb 2023 06:42:11 GMT
Content-Type: text/html
Content-Length: 239
Connection: keep-alive
Location: http://rewrite.nhxn.com
```

```
打开rewrite_log

[root@iZbp18iw6uhl07x7b6k1ctZ logs]# cat rewrite_error.log 
2023/02/24 14:55:25 [notice] 16176#0: *53 "/redirect1(.*)" matches "/redirect1", client: 127.0.0.1, server: return.com, request: "HEAD /redirect1 HTTP/1.1", host: "rewrite.nhxn.com:8080"
2023/02/24 14:55:25 [notice] 16176#0: *53 rewritten redirect: "", client: 127.0.0.1, server: return.com, request: "HEAD /redirect1 HTTP/1.1", host: "rewrite.nhxn.com:8080"
2023/02/24 14:55:28 [notice] 16175#0: *54 "/redirect2(.*)" matches "/redirect2", client: 127.0.0.1, server: return.com, request: "HEAD /redirect2 HTTP/1.1", host: "rewrite.nhxn.com:8080"
2023/02/24 14:55:28 [notice] 16175#0: *54 rewritten redirect: "", client: 127.0.0.1, server: return.com, request: "HEAD /redirect2 HTTP/1.1", host: "rewrite.nhxn.com:8080"
2023/02/24 14:55:30 [notice] 16175#0: *55 "/redirect3(.*)" matches "/redirect3", client: 127.0.0.1, server: return.com, request: "HEAD /redirect3 HTTP/1.1", host: "rewrite.nhxn.com:8080"
2023/02/24 14:55:30 [notice] 16175#0: *55 rewritten redirect: "http://rewrite.nhxn.com", client: 127.0.0.1, server: return.com, request: "HEAD /redirect3 HTTP/1.1", host: "rewrite.nhxn.com:8080"
2023/02/24 14:55:32 [notice] 16174#0: *56 "/redirect4(.*)" matches "/redirect4", client: 127.0.0.1, server: return.com, request: "HEAD /redirect4 HTTP/1.1", host: "rewrite.nhxn.com:8080"
2023/02/24 14:55:32 [notice] 16174#0: *56 rewritten redirect: "http://rewrite.nhxn.com", client: 127.0.0.1, server: return.com, request: "HEAD /redirect4 HTTP/1.1", host: "rewrite.nhxn.com:8080"
```

#### c、if指令

```
if(condition){...}
规则：
	条件condition为真，则执行大括号内的指令；循环值指令的继承规则

if指令的条件表达式：
	1、检查变量为空或者值是否为0，直接使用
	2、将变量与字符串做匹配，使用=或者！=
	3、将变量与正则表达式做匹配
		·大小写敏感，~或者!~
		·大小写不敏感，~*或者!~*
	4、检查文件是否存在，使用-f或者!-f
	5、检查目录是否存在，使用-d或者!-d
	6、检查文件、目录、软连接是否存在，使用-e或者!-e
	7、检查是否为可执行文件，使用-x或者!-x
```

##### a案例)

```
if($http_user_agent~MSIE) {
    rewrite^(.*)$ /msie/$1 break;
}
if($http_cookie~*"id=([^;]+)(?:;|$)") {
    set $id $1;
}
if($request_method = POST) {
    return 405;
}
if($slow) {
    limit_rate 10k;
}
if($invalid_refer) {
    return 403;
}
```



### 3)FIND_CONFIG模块

#### a、location写法

```
语法: 

location [=|~|~*|^~] uri地址 {
    ........
    ........
}
1、=     精确匹配

location = /test {
    
}

location = / {

}
2、~   通过正则表达式匹配请求, 区分大小写 

匹配php请求

location ~ \.php$ {

}

匹配图片请求

location ~ \.(jpg|gif|jpeg|png)$ {

}
3、~*    通过正则表达式请求, 不区分大小写
4、^~    不以正则表达式匹配请求

location ^~ /test {

}
5、@		用于内部跳转，不直面用户的请求
```

#### ！！！！！优先级: 从高到低 非常重要

    =， ^~, ~, ~*, location /

##### a案例）

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat return.conf 
server {
	server_name location.nhxn.com;
	listen 8080;
	root /data/html;
	rewrite_log on;
	error_log /data/logs/rewrite_error.log notice;
	location ~/Test1/$ {
	    return 200 'first regular expressions match!\n';
	}
	location ~*/Test1/(\w+)$ {
	    return 200 'longest regular expressions match!\n';
	}
	location ^~/Test1/ {
	    return 200 'stop regular expressions match!\n';
	}
	location /Test1/Test2 {
	    return 200 'longest prefix string match!\n';
	}
	location /Test1 {
	    return 200 'prefix string match!\n';
	}
	location =/Test1 {
	    return 200 'exact match!\n';
	}	
}
```

```
匹配一下规则
/Test1
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl location.nhxn.com:8080/Test1
exact match!          ###精确匹配

/Test1/
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl location.nhxn.com:8080/Test1/
stop regular expressions match!

/Test1/Test2
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl location.nhxn.com:8080/Test1/Test2
longest regular expressions match!

/Test1/Test2/
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl location.nhxn.com:8080/Test1/Test2、
longest prefix string match!
```



### 4)limit_conn模块

#### ngx_http_preaccess_phase

```
生效阶段：ngx_http_preaccess_phase阶段
模块：http_limit_conn_module
默认编译进nginx，通过--without-http_limit_conn_module禁用
生效范围：
	·全部worker进程（基于共享内存）
	·进入preaccess阶段前不生效
	·限制的有效性取决于key的设计：依赖postread阶段的realip模块取到的真实ip
```

#### a）限制发生时的日志级别

```
语法：limit_conn_log_level info |notice|warn|error;
默认：limit_conn_log_level error;
编写位置：http,server,location
```

#### b）限制发生时向客户端返回的错误码

```
语法：limit_conn_status code;
默认：limit_conn_status 503;
编写位置：http,server,location
```

##### a案例）

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat limit_conn.conf 

limit_conn_zone $binary_remote_addr zone=addr:10m;		#定义10M共享内存，使用二进制格式的IP地址（IPV4协议下占4字节）
server {
	server_name limit.nhxn.com;
	root /data/html;
	error_log /data/logs/limit_error.log info;
	listen 8090;
	location / {
		limit_conn_status 500;			#定义向用户返回错误码为500
		limit_conn_log_level warn;		#定义错误日志级别为warn
		limit_rate 50;					#限制向用户返回的速度，每秒钟返回50字节
		limit_conn addr 1;				#限制同时并发连接数为1
	}
}
```

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl limit.nhxn.com:8090
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html>
<head><title>500 Internal Server Error</title></head>
<body>
<center><h1>500 Internal Server Error</h1></center>

查看错日志
[root@iZbp18iw6uhl07x7b6k1ctZ logs]# cat limit_error.log 
2023/02/24 18:24:30 [warn] 17584#0: *88 limiting connections by zone "addr", client: 127.0.0.1, server: limit.nhxn.com, request: "GET / HTTP/1.1", host: "limit.nhxn.com:8090"
```

#### ngx_http_limit_req_module模块

```
生效阶段：ngx_http_preaccess_phase阶段
模块：http_limit_req_module
默认编译进nginx，通过--without-http_limit_req_module禁用功能
生效算法：leaky bucket算法
生效范围：
	·全部worker进程（基于共享内存）
	·进入preaccess阶段前不生效
```

####  a）定义共享内存（包括大小），以及key关键字和限制速率

```
语法：limit_req_zone key zone=name:size rate=rate;
默认：  ~~~~
编写位置：http
	· rate单位为r/s或者r/m
```

#### b）限制并发连接数

```
语法：limit_req zone=name[burst=number][nodelay];
默认： ~~~~~
编写位置：http,server,location
	· burst默认为0
	· nodelay，对burst中的请求不在采用延时处理的做法，而是立刻处理。
```

#### c）限制发生时的日志级别

```
语法：limit_req_log_level info|notice|warn|error;
默认：limit_req_log_level error;
编写位置：http,server,location
```

#### d）限制发生时向客户端返回的错误码

```
语法：limit_red_status code;
默认：limit_red_status 503;
编写位置：http,server,location
```

##### a案例 ）

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat limit_conn.conf 
limit_conn_zone $binary_remote_addr zone=addr:10m;
limit_req_zone $binary_remote_addr zone=one:10m rate=2r/m;
server {
	server_name limit.nhxn.com;
	listen 8090;
	root /data/html;
	index index.html;
	error_log /data/logs/limit_error.log info;
	location / {
		limit_conn_status 500;
		limit_conn_log_level warn;
		#limit_rate 50;
		#limit_conn addr 1;
		#limit_req zone=one burst=3 nodelay;
		limit_req zone=one;
	}
}
```

```
第一次访问
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl limit.nhxn.com:8090
ok
第二次访问，返回503
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl limit.nhxn.com:8090
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html>
<head><title>503 Service Temporarily Unavailable</title></head>
<body>

修改配置文件：
	limit_req zone=one burst=3 nodelay; #设置一个大小为3的缓存区
	#limit_req zone=one;
第一次访问~第四次访问
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl limit.nhxn.com:8090
ok
第五次访问返回503

修改配置文件：
	limit_conn_status 500;
	limit_conn_log_level warn;
	limit_rate 50;
	limit_conn addr 1;
	#limit_req zone=one burst=3 nodelay;
	limit_req zone=one;
第一次访问：
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl limit.nhxn.com:8090
ok
第二次访问返回503，不是500，因为limit_req在limit_conn之前。
```

### 5）access阶段

#### ngx_http_access_module模块

```
生效阶段：ngx_http_access_phase阶段
模块：http_access_module
默认编译进nginx，通过--without-http_access_module禁用功能
生效范围：
	· 进入access阶段前不生效
```

#### a）指令介绍

```
允许
语法:allow address |CIDR|unix:|all;
默认： ~~~~
编写位置：http,server,location,limit_except
```

```
拒绝
语法：deny address |CIDR|unix:|all;
默认： ~~~
编写位置：http,server,location,limit_except
```

##### 示例

```
location / {
	deny 192.168.1.1;
	allow 192.168.1.0/24;
	allow 10.1.1.0/16;
	allow 2001:0db8::/32;
	deny all;
}
```

#### auth_basic

```
功能：
	基于HTTP Basic Authutication 协议进行用户名密码的认证
	默认编译进Nginx：
		通过--without-http_auth_basic_module禁用功能
```

```
指令
语法：auth_basic string|off;
默认：auth_basic off;
编写位置：http,server,location,limit_except

语法：auth_basic_user_file file;
默认： ~~~~
编写位置：http,server,location,limit_except
```

#### 生成密码文件

```
生成工具：htpasswd
依赖安装包：httpd-tools
htpasswd -c file -b user pass

生成的文件格式：
	#comment
	name1:password1
	name2:password2:comment
	name2:password3
```

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# htpasswd -c /root/auth.txt wtl 
New password: 
Re-type new password: 

[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat /root/auth.txt 
wtl:$apr1$5I4/Oh8Q$wSUoUAFoOC/le.jKSf.bh0
```

##### a案例）

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat access.conf
server {
	listen 9090;
	error_log /data/logs/access_error.log debug;
	location / {
		satisfy any;
		auth_basic "test auth_basic";
		auth_basic_user_file /root/auth.txt;
		deny all;
	}
	location /auth_request {
		auth_request /test_auth;
	}
	location = /test_auth {
		proxy_pass http://127.0.0.1;
		proxy_pass_request_body off;
		proxy_set_header Content-Length "";
		proxy_set_header X-Original-URI $request_uri;
	}
}
```

#### auth_request

```
功能：
	向上游服务转发请求，若上游服务返回的响应码是2xx，则继续执行，若上游服务返回的是401或者403，则将响应返回给客户端。
```

```
原理：
	收到请求后，生成子请求，通过反向代理技术传递给上游服务。
```

```
指令：
语法：auth_request uri|off;
默认：auth_request off;
编写位置：http,server,location

语法:auth_request_set $variable value;
默认： ~~~~
编写位置；http,server,location

默认未编译进Nginx
--with-http_auth_request_module
```

##### a案例）

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat access.conf 
server {
	listen 9090;
	error_log /data/logs/access_error.log debug;
	location /auth_basic {
		satisfy any;
		auth_basic "test auth_basic";
		auth_basic_user_file /root/auth.txt;
		deny all;
	}
	location / {
		auth_request /test_auth;
	}
	location = /test_auth {
		proxy_pass http://127.0.0.1:8080/Test1;
		proxy_pass_request_body off;
		proxy_set_header Content-Length "";
		proxy_set_header X-Original-URI $request_uri;
	}
}

[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat return.conf
server {
	listen 8080;
	root /data/html;
	rewrite_log on;
	error_log /data/logs/rewrite_error.log notice;
	location =/Test1 {
	    return 200 'exact match!\n';
	}	
}
```

```
第一次可以访问

第二次
修改return.conf
	return 403 ;
返回403
```

#### satisfy指令

```
语法：satisfy all | any;
默认：satisfy all;
编写位置：http,server,location
```

```
access阶段的模块：
	· access模块
	· auth_basic模块
	· auth_request模块
	· 其他模块
```

### 6）precontent阶段

#### try_files指令

```
语法：try_files file....uri;
	 try_files file....=code;
默认： ~~~
编写位置：server,location
```

```
功能：依次试图访问多个url对应的文件（由root或者alias指令指定），当文件存在时直接返回文件内容，如果所有文件都不存在，则按最后一个URL结果或者code返回。
ngx_http_try_files_module模块
```

##### a案例）

```
server {
	listen 9999;
	error_log /data/logs/tryfile_error.log info;
	root html/;
	default_type text/plain;
	location /first {
		try_files /data/html/index.html
					$uri $uri/index.html $uri.html
					@lasturl;
	}
	location @lasturl {
		return 200 'lasturi!\n';
	}
	location /second {
		try_files $uri $uri/index.html $uri.html =404;
	}

}
```

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:9999/first
lasturi!
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:9999/second
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
```

#### ngx_http_mirror_module模块(实时拷贝流量)

```
ngx_http_mirror_module模块，默认编译进Nginx，用过--without-http_mirror_module移除模块。
```

```
功能：
	处理请求时，生成子请求访问其他服务，对子请求的返回值不做处理。
```

```
语法：mirror uri | off;
默认：mirror off;
编写位置；http,server,location

语法：mirror_request_body on | off;
默认：mirror_request_body on;
编写位置；http,server,location
```

### 7）content阶段

#### static模块

```
语法：alias path;
默认：~~~
编写位置；location

语法：root path;
默认：root html;
编写位置；http,server,location,if in location
```

```
功能：
	将url映射为文件路径，以返回静态文件内容。
差别：
	root会将完整url映射进文件路径中
	alias只会将location后的URL映射到文件路径
```

##### a案例）

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat static.conf 
server {
	listen 9099;
	error_log /data/logs/static_error.log info;
	location /root {
		root html;
	}
	location /alias {
		alias html;
	}
	location ~ /root/(\w+\.txt) {
		root html/first/$1;		
	}		
	location ~ /alias/(\w+\.txt) {
		alias html/first/$1;		
	}
	location /RealPath/ {
		alias html/realpath/;
		return 200 '$request_filename:$document_root:$realpath_root\n';
	}
}
```

```
访问/root/
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:9099/root/
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html>
<head><title>404 Not Found</title></head>

访问/root/1.txt
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:9099/root/1.txt
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html>
<head><title>404 Not Found</title></head>

访问/alias/
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:9099/alias/
<title>Welcome to tengine!</title>

访问/alias/1.txt
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:9099/alias/1.txt
test1
```

生成带访问文件的三个相关的变量

```
request_filename
	待访问文件的完整路径
document_root
	由URI和root/alias规则生成的文件夹路径
realpath_root
	将document_root中的软连接等换成真实路径
```

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat static.conf 
server {
	listen 9099;
	error_log /data/logs/static_error.log info;
	location /root {
		root html;
	}
	location /alias {
		alias html;
	}
	location ~ /root/(\w+\.txt) {
		root html/first/$1;		
	}		
	location ~ /alias/(\w+\.txt) {
		alias html/first/$1;		
	}
	location /RealPath/ {
		alias html/realpath/;
		return 200 '$request_filename:$document_root:$realpath_root\n';
	}
}
```

```
[root@iZbp18iw6uhl07x7b6k1ctZ html]# ll /data/html/
total 20
-rw-r--r-- 1 root root   16 Feb 24 13:42 403.html
drwxr-xr-x 2 root root 4096 Feb 24 14:01 first
-rw-r--r-- 1 root root    3 Feb 24 21:54 index.html
lrwxrwxrwx 1 root root    6 Feb 25 01:21 realpath -> first/
drwxr-xr-x 2 root root 4096 Feb 24 14:02 second
drwxr-xr-x 2 root root 4096 Feb 24 14:02 third
```

```
访问
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 127.0.0.1:9099/RealPath/1.txt
/usr/local/nginx/html/realpath/1.txt:/usr/local/nginx/html/realpath/:/usr/local/nginx/html/first
```

##### b案例）

#### 访问目录时URL最后没有带/？

```
static模块实现了root/alias功能时，发现访问目标是目录，但URL末尾未加/时，会返回301重定向。
```

#### 重定向跳转的域名

```
语法：server_name_in_redirect on | off;
默认：server_name_in_redirect off;
编写位置：http,server,location

语法：port_in_redirect on | off;
默认：port_in_redirect on;
编写位置：http,server,location

语法：absolute_redirect on | off;
默认：absolute_redirect on;
编写位置：http,server,location
```

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat dirredirect.conf 
server {
	server_name localhost aasjkdfs.com;
	server_name_in_redirect off;
	listen 9091;
	port_in_redirect on;
	absolute_redirect on;

	root /data/html/;
}
```

```
访问
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# curl 121.43.224.178:9091/first -I
HTTP/1.1 301 Moved Permanently

修改配置文件
	#absolute_redirect on;
再次访问
```

#### autoindex模块

```
功能：
	当URL以/结尾时，尝试以html/xml/json/jsonp等格式返回root/alias中指向目录的目录结构。
```

```
模块：
	ngx_http_index_module
	默认编译进Nginx：--without-http_autoindex_module取消
```

```
autoindex模块的指令：
语法：autoindex on | off;
默认：autoindex off;
编写位置：http,server,location

语法：autoindex_exact_size on | off;
默认：autoindex_exact_size on;
编写位置：http,server,location

语法：autoindex_format html | xml | json | jsonp;
默认：autoindex_format html;
编写位置：http,server,location

语法：autoindex_localtime on | off;
默认：autoindex_localtime off;
编写位置：http,server,location
```

##### a案例）

```
[root@iZbp18iw6uhl07x7b6k1ctZ conf.d]# cat autoindex.conf
server {
	listen 9092;
	location / {
		alias html/;
		autoindex on;
		#index a.html;
		autoindex_exact_size off;
		autoindex_format json;
		autoindex_localtime on;
	}
}
```

```
访问时会返回主页

修改配置文件：	
	index a.html;
会以json格式返回页面。

再次修改配置文件：
	autoindex_format html;
以html形成访问
```

### 8）log阶段

#### access日志格式

```
语法：log_format name [excape=default|json|none]string ...;
默认：log_format combined "...";
编写位置：http
```

#### 默认的combined日志格式

```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';
```

#### 配置日志文件路径

```
语法：access_log path [format[buffer=size][gzip[=level]][flush=time][if=condition]];
默认：access_log logs/access.log combined;
编写位置：http,server,location,if in location,limit_except
```

```
path路径可以包含变量：不打开cache时每记录一条日志都需要打开、关闭日志文件
if通过变量值控制请求日志是否记录
日志缓存
	· 功能：批量将内存中的日志写入磁盘
	· 写入磁盘的条件
		· 所有待写入磁盘的日志大小超出缓存大小
		· 达到flush指定的过期时间
		· worker进程执行reopen命令，或者正在关闭
日志压缩
	· 功能：批量压缩内存中的日志，再写入磁盘
	· buffer大小默认为64KB
	· 压缩级别默认为1（1最快压缩率最低，9最慢压缩率最高 ）
```

#### 对日志文件名包含变量时的优化

```
语法：open_log_file_cache max=N[inactie=time][min_uses=N][valid=time];
	 open_log_file_cahe off;
默认：open_log_file_cache off;
编写位置：http,server,locaiton

max：缓存内的最大文件句柄数，超出后用LRU算法淘汰
inactive：文件访问完后在这段时间内不会被关闭。默认10秒。
min_uses：在inactive时间内使用次数超过min_uses才会继续存在内存中，默认1。
valid：超出valid时间后，将对缓存的日志文件检查是否存在，默认60秒。
off：关闭缓存功能。
```

# 三、应用于业务上的一键部署脚本

```
各软件版本：

软件：Tenginx 2.3.0 , LuaJIT-2.0.5 ，
Tenginx三方模块版本：lua-nginx-module-0.10.9rc7 ， ngx_devel_kit-0.3.0

```

```bash
#!/bin/bash
#Author SemanL
#Version 2.1
#软件：Tenginx 2.3.0 , LuaJIT-2.0.5 
#Tenginx三方模块版本：lua-nginx-module-0.10.9rc7 ， ngx_devel_kit-0.3.0

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
export PATH

if [ $(whoami) != "root" ];then
	echo"请使用root用户权限执行！"
	exit 1;
fi

#创建日志目录
echo "正在创建日志目录....."
mkdir -p /data/{logs,json_logs}
sleep 1

#检查nginx是否安装、80端口、443端口是否占用
echo "正在检查nginx是否安装、80端口、443端口是否占用....."

NGINX_CHECK=$(ps -ef|grep nginx|grep master)
HTTPD_CHECK=$(ps -ef |grep -E 'httpd|apache'|grep -v grep)

if [ "${NGINX_CHECK}" ] || [ "${HTTPD_CHECK}" ];then

	echo "请检查nginx是安装，以及80、443端口是否被占用"
	exit 1;
fi
sleep 1;

#调整内核参数
echo "正在调整内核参数...."
SYSCTL_ADD(){
	cat <<'EOF' > /etc/sysctl.conf
	net.ipv4.ip_forward = 0
	net.ipv4.conf.default.rp_filter = 1
	net.ipv4.conf.default.accept_source_route = 0
	kernel.sysrq = 0
	kernel.core_uses_pid = 1
	net.ipv4.tcp_syncookies = 1
	net.bridge.bridge-nf-call-ip6tables = 0
	net.bridge.bridge-nf-call-iptables = 0
	net.bridge.bridge-nf-call-arptables = 0
	kernel.msgmnb = 65536
	kernel.msgmax = 65536
	kernel.shmmax = 68719476736
	kernel.shmall = 4294967296
	net.ipv4.tcp_synack_retries=0
	net.ipv4.tcp_max_syn_backlog = 50000
	fs.file-max=8192000
	fs.nr_open =10000000
	net.core.somaxconn=65535
	net.core.rmem_max=16777216
	net.core.wmem_max=16777216
	net.core.netdev_max_backlog=165536
	net.ipv4.ip_local_port_range=1024 65535
	kernel.shmall = 154618822656
	net.ipv4.tcp_tw_reuse = 1
	net.ipv4.tcp_tw_recycle = 0
	net.ipv4.tcp_keepalive_time = 1200
	net.ipv4.tcp_fin_timeout = 10
	net.ipv4.tcp_max_tw_buckets = 360000
	net.netfilter.nf_conntrack_max = 2100000
	net.netfilter.nf_conntrack_tcp_timeout_established = 120
	net.ipv4.tcp_syn_retries=3
	net.ipv4.tcp_sack=0
EOF
}
SYSCTL_CHECK=$(ls /etc/sysctl.conf)
if [ $SYSCTL_CHECK ] ; then
	touch /etc/sysctl.conf
	SYSCTL_ADD
else
	cp /etc/sysctl.conf /etc/sysctl.conf.bak_`date +%F-%T`
	echo > /etc/sysctl.conf
	SYSCTL_ADD
fi

modprobe br_netfilter
modprobe ip_conntrack

#添加开机自动加载模块的脚本

CHECK_BR_NETFILTER=$(ls /etc/sysconfig/modules/br_netfilter.modules &> /dev/null)
CHECK_IP_CONNTRACK=$(ls /etc/sysconfig/modules/ip_conntrack.modules &> /dev/null)
CHECK_RC_SYSINIT=$(ls /etc/rc.sysinit &> /dev/null)

MODULES_ADD(){
	echo > /etc/sysconfig/modules/br_netfilter.modules
cat <<'EOF' > /etc/sysconfig/modules/br_netfilter.modules
	modprobe br_netfilter
EOF
	
	echo > /etc/sysconfig/modules/ip_conntrack.modules
cat <<'EOF' > /etc/sysconfig/modules/ip_conntrack.modules
	modprobe ip_conntrack
EOF

	echo > /etc/rc.sysinit
cat <<'EOF' > /etc/rc.sysinit
	#!/bin/bash
	for file in /etc/sysconfig/modules/*.modules ; do
	[- x $file ] && $file
	done
EOF
}
 
if [ $CHECK_BR_NETFILTER ] && [ $CHECK_IP_CONNTRACK ] && [ $CHECK_RC_SYSINIT ] ; then
    touch /etc/sysconfig/modules/{br_netfilter.modules,ip_conntrack.modules} && touch /etc/rc.sysinit
    MODULES_ADD
    chmod 755 /etc/sysconfig/modules/{br_netfilter.modules,ip_conntrack.modules}
    sleep 2;
else
    cp /etc/sysconfig/modules/br_netfilter.modules /etc/sysconfig/modules/br_netfilter.modules.bak_`date +%F-%T`
    cp /etc/sysconfig/modules/ip_conntrack.modules /etc/sysconfig/modules/ip_conntrack.modules.bak_`date +%F-%T`
    cp /etc/rc.sysinit /etc/rc.sysinit.bak_`date +%F-%T`
    echo "已备份“/etc/sysconfig/modules/{br_netfilter.modules,ip_conntrack.modules}”与“/etc/rc.sysinit”"
    MODULES_ADD
    chmod 755 /etc/sysconfig/modules/{br_netfilter.modules,ip_conntrack.modules}
    sleep 5;
fi

#优化文件句柄数
cp /etc/security/limits.conf /etc/security/limits.conf.bak_`date +%F-%T`
echo > /etc/security/limits.conf
cat <<'EOF'> /etc/security/limits.conf
* soft nofile 10000000
* hard nofile 10000000
root soft nofile 10000000
root hard nofile 10000000
* soft memlock unlimited
* hard memlock unlimited
* hard nproc 655350
* soft nproc 655350
EOF

#添加hosts文件
cp /etc/hosts /etc/hosts`date +%F-%T`
cat <<'EOF'> /etc/hosts
10.12.60.21     storm-cluster1.gyyx.cn
10.12.60.22     storm-cluster2.gyyx.cn
10.12.60.23     storm-cluster3.gyyx.cn
EOF

echo "正在安装Tengine所需依赖"
yum install -y  gcc zlib-devel pcre-devel openssl-devel libxml2 libxslt-devel wget unzip patch bash-completion lrzsz vim openssl make gcc-c++ iptables-services &> /dev/null


echo "正在下载LuaJIT源码安装包......"
cd /usr/local/src
wget --continue --tries=3 https://sa-os-1251840830.cos.ap-beijing.myqcloud.com/package/LuaJIT-2.0.5.tar.gz

if [ "$?" == "0" ] ; then
    tar xf LuaJIT-2.0.5.tar.gz 
    cd LuaJIT-2.0.5 && make && make install PREFIX=/usr/local/LuaJIT 
    PATH_LuaJIT=$(grep "export LUAJIT_LIB=/usr/local/LuaJIT/lib" /etc/profile |wc -l)
else
    echo "未能下载LuaJIT源码安装包请检查！！！！！"
    exit 1;
fi

if [ $PATH_LuaJIT -eq 1 ]; then
	source /etc/profile
	ldconfig
	sleep 2;
else
	echo "export LUAJIT_LIB=/usr/local/LuaJIT/lib" >> /etc/profile
	echo "export LUAJIT_INC=/usr/local/LuaJIT/include/luajit-2.0" >> /etc/profile
	source /etc/profile 
	echo "/usr/local/LuaJIT/lib" >> /etc/ld.so.conf && ldconfig 
	sleep 2
fi


echo "正在解压Nginx Lua模块..."
cd /usr/local/src
wget --continue --tries=3 https://sa-os-1251840830.cos.ap-beijing.myqcloud.com/package/lua-nginx-module-0.10.9rc7.tar.gz
if [ "$?" == "0" ] ; then
    tar xf lua-nginx-module-0.10.9rc7.tar.gz
    sleep 2
else
    echo "未能成功下载Nginx Lua模块！！！"
    exit 1;
fi

echo "正在解压ngx_devel_kit模块..."
cd /usr/local/src
wget --continue --tries=3 https://sa-os-1251840830.cos.ap-beijing.myqcloud.com/package/ngx_devel_kit-0.3.0.tar.gz
if [ "$?" == "0" ] ; then
    tar xf ngx_devel_kit-0.3.0.tar.gz 
    sleep 2
else 
    echo "未能成功下载ngx_devel_kit模块！！！"
    exit 1;
fi

echo "正在下载Tengine编译安装包...."
cd /usr/local/src
wget --continue --tries=3 https://sa-os-1251840830.cos.ap-beijing.myqcloud.com/package/tengine-2.3.0.tar.gz
if [ "$?" == "0" ] ; then
    tar xf tengine-2.3.0.tar.gz
    sleep 2
else 
    echo "未能成功下载Tengine安装包！！！"
    exit 1;
fi


#修改源码文件"ngx_http_log_module.c"，解决nginx在记录post数据时,中文字符转成16进制的问题
sed -i '1103s/^/\/\*/' /usr/local/src/tengine-2.3.0/src/http/modules/ngx_http_log_module.c
sed -i '1103s/$/\*\//' /usr/local/src/tengine-2.3.0/src/http/modules/ngx_http_log_module.c
sed -i '1142s/^/\/\*/' /usr/local/src/tengine-2.3.0/src/http/modules/ngx_http_log_module.c
sed -i '1151s/$/\*\//' /usr/local/src/tengine-2.3.0/src/http/modules/ngx_http_log_module.c
sed -i '1151a\        *dst++ = *src++;' /usr/local/src/tengine-2.3.0/src/http/modules/ngx_http_log_module.c

#编译安装Tengine
echo > /usr/local/src/tengine-2.3.0/Tengin_install.log
cd /usr/local/src/tengine-2.3.0 
./configure --prefix=/usr/local/nginx \
--user=root \
--group=root \
--with-http_ssl_module \
--with-http_flv_module \
--with-file-aio \
--with-http_realip_module \
--with-http_addition_module \
--with-http_xslt_module \
--with-http_sub_module \
--with-http_dav_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_secure_link_module \
--with-http_degradation_module \
--with-http_stub_status_module  \
--with-debug \
--with-stream \
--add-module=/usr/local/src/lua-nginx-module-0.10.9rc7/ \
--add-module=/usr/local/src/ngx_devel_kit-0.3.0 

make && make install
if [ $? != 0 ]; then 
	echo "Tenginx编译失败，请检查/usr/local/src/tengine-2.3.0/Tengin_install.log"
	exit 1;
else 
	echo "Tengine编译成功！"
fi

#提前写入nginx.conf
cp /usr/local/nginx/conf/nginx.conf /usr/local/nginx/conf/nginx.conf.bak_`date +%F-%T`
echo > /usr/local/nginx/conf/nginx.conf
cat <<'EOF' > /usr/local/nginx/conf/nginx.conf
user  root;
worker_processes  3;
worker_cpu_affinity 0001 0010 0100;

error_log   /data/logs/error.log error;

pid        /var/run/nginx.pid;
worker_rlimit_nofile  102400;

events {
    use epoll;
    worker_connections  102400;
}



http {
    include       ./mime.types;
    default_type  application/octet-stream;
    server_tokens off;
    log_format  main  '$hostname|$remote_addr|-|[$time_local]|$server_name'
                      '|$request|$request_length|$request_time'
                      '|"$status"|$body_bytes_sent|$bytes_sent|$connection|$connection_requests'
                      '|"$http_user_agent"|"$http_referer"'
                      '|$upstream_addr|$upstream_status|$upstream_response_time|tx-$hostname-$server_addr';

    log_format  main_json  escape=json '{"hostname":"$hostname",'
                           '"remote_addr":"$remote_addr",'
                           '"client_ip":"$http_x_forwarded_for",'
                           '"time_local":"$time_local",'
                           '"server_name":"$server_name",'
                           '"request":"$request",'
                           '"request_length":"$request_length",'
                           '"request_time":"$request_time",'
                           '"status":"$status",'
                           '"body_bytes_sent":"$body_bytes_sent",'
                           '"bytes_sent":"$bytes_sent",'
                           '"connection":"$connection",'
                           '"connection_requests":"$connection_requests",'
                           '"http_user_agent":"$http_user_agent",'
                           '"http_referer":"$http_referer",'
                           '"upstream_addr":"$upstream_addr",'
                           '"upstream_status":"$upstream_status",'
                           '"upstream_response_time":"$upstream_response_time",'
                           '"hostname-server_addr":"$hostname-$server_addr",'
                           '"request_body":"$request_body",'
                           '"http_mycheck":"$http_mycheck",'
                           '"http_Authorization":"$http_Authorization"'                        
                           '}';

    access_log  /data/logs/access.log main;
    access_log  /data/json_logs/access.log main_json;
    client_header_buffer_size    32k;
    large_client_header_buffers  4 32k;
    server_names_hash_bucket_size 128;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   600;
    gzip                on;
    gzip_min_length     1k;
    gzip_buffers        4 16k;
    gzip_types          text/plain application/x-javascript text/css text/javascript application/xml;
    gzip_vary           on;
    fastcgi_intercept_errors on;
   server{
      listen 80 default;
      server_name _;
      return 403;
   }
}
EOF


#启动tengine并加入开机自启
echo > /etc/init.d/nginx
cat <<'EOF' > /etc/init.d/nginx
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemon
#
# chkconfig:   - 85 15
# description:  NGINX is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
# processname: nginx
# config:      /etc/nginx/nginx.conf
# config:      /etc/sysconfig/nginx
# pidfile:     /var/run/nginx.pid

# Source function library.
. /etc/rc.d/init.d/functions

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0

nginx="/usr/local/nginx/sbin/nginx"
prog=$(basename $nginx)

NGINX_CONF_FILE="/usr/local/nginx/conf/nginx.conf"

[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx

lockfile=/var/lock/subsys/nginx

make_dirs() {
   # make required directories
   user=`$nginx -V 2>&1 | grep "configure arguments:.*--user=" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
   if [ -n "$user" ]; then
      if [ -z "`grep $user /etc/passwd`" ]; then
         useradd -M -s /bin/nologin $user
      fi
      options=`$nginx -V 2>&1 | grep 'configure arguments:'`
      for opt in $options; do
          if [ `echo $opt | grep '.*-temp-path'` ]; then
              value=`echo $opt | cut -d "=" -f 2`
              if [ ! -d "$value" ]; then
                  # echo "creating" $value
                  mkdir -p $value && chown -R $user $value
              fi
          fi
       done
    fi
}

start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    make_dirs
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}

stop() {
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}

restart() {
    configtest || return $?
    stop
    sleep 1
    start
}

reload() {
    configtest || return $?
    echo -n $"Reloading $prog: "
    killproc $prog -HUP
    retval=$?
    echo
}

force_reload() {
    restart
}

configtest() {
  $nginx -t -c $NGINX_CONF_FILE
}

rh_status() {
    status $prog
}

rh_status_q() {
    rh_status >/dev/null 2>&1
}

case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart|configtest)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
    condrestart|try-restart)
        rh_status_q || exit 0
            ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2
esac
EOF

#设置Nginx文件的权限
chmod a+x /etc/init.d/nginx

#将nginx加入chkconfig管理列表
chkconfig --add /etc/init.d/nginx
chkconfig nginx on
service nginx restart 
service nginx status

mkdir -p /data/script/cronsc/
cat <<'EOF'> /data/script/cronsc/loop_logs.sh
#!/bin/bash
#
log="/data/logs/access.log"
log1="/data/logs/error.log"
log_json="/data/json_logs/access.log"
newlog1="/data/logs/access.log.0"
newlog2="/data/logs/access.log."$((`date +%Y%m%d%H%M`))
newlog_json="/data/json_logs/access.log."$((`date +%Y%m%d%H%M`))
#newlog2="/data/logs/access.log."$((`date +%Y%m%d%H%M --date="10 min ago"`))
errorlog1="/data/logs/error.log.0"
errorlog2="/data/logs/error.log."$((`date +%Y%m%d%H%M`))
#errorlog2="/data/logs/error.log."$((`date +%Y%m%d%H%M --date="10 min ago"`))
#/bin/mv $newlog1 $newlog2
/bin/mv $log $newlog2
/bin/mv $errorlog1 $errorlog2
/bin/mv $log1 $errorlog1
/bin/mv $log_json $newlog_json

/bin/kill -USR1 `cat /var/run/nginx.pid`
/bin/sh /data/url_analyze.sh $newlog2
find /data/logs/  -name "access.log.2*" -type f -mtime 7 -exec rm -f {} \;
find /data/logs/  -name "error.log.2*" -type f -mtime 7 -exec rm -f {} \;
find /data/json_logs/  -name "access.log.2*" -type f -mtime +2 -exec rm -f {} \;
/bin/date +%s > /var/run/time

EOF

chmod +x /data/script/cronsc/loop_logs.sh


cd /tmp
/usr/bin/crontab  -l > ./conf ; echo "*/5 * * * * /bin/bash       /data/script/cronsc/loop_logs.sh >> /tmp/tmp.txt" >> ./conf && crontab conf && rm -rf ./conf
if [ $? -ne 0 ]; then
    echo "定时计划任务添加失败，请检查"
    exit 1
else
    echo "定时计划任务添加成功"
fi
```

