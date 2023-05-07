---
title: Nginx如何提供安全保护
date: 2021-02-16 15:23:41
update: 2021-6-24 15:47:34
tags:
  - Nginx
categories:
  - Nginx
description: 'Nginx如何提供安全保护'
keywords: 'Nginx'
top_img: /assert/index-image.png
cover: false
---

## <center>Nginx如何提供安全保护</center>

Nginx作为一款高性能的Web服务器，除了提供高效的HTTP请求处理能力外，还具备一些安全保护功能。例如：

1.  访问控制：Nginx可以基于IP地址、用户代理、HTTP请求方法、请求路径等等来控制访问。例如，我们可以使用`allow`和`deny`指令来限制IP地址的访问
    
2.  HTTPS支持：Nginx可以使用SSL/TLS协议来加密HTTP通信，以防止数据被窃取或篡改。我们可以使用Nginx模块`ngx_http_ssl_module`来配置HTTPS
    
3.  反向代理：Nginx可以作为反向代理服务器，将请求转发到后端服务器上。通过反向代理，我们可以隐藏后端服务器的IP地址和端口号，从而增加安全性
    
4.  Web应用防火墙（WAF）：Nginx可以使用第三方模块或插件来提供基本的WAF功能，例如防止SQL注入、跨站点脚本攻击（XSS）和代码注入等等

## 配置HTTPS

HTTPS是一种基于SSL/TLS协议的加密通信协议，它能够防止数据被窃取或篡改。在Nginx上配置HTTPS，需要以下步骤：

1.  获取SSL证书和私钥：我们可以使用一些工具，如Let's Encrypt、Certbot等等，来获取SSL证书和私钥。
    
2.  配置SSL证书和私钥：我们需要在Nginx配置文件中指定证书和私钥的路径，例如：

```bash
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    # other configuration
}
```

3.  配置SSL协议和密码套件：我们可以使用Nginx的`ssl_protocols`和`ssl_ciphers`指令来配置SSL协议和密码套件，例如：

```bash
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-A
```

4.  配置SSL会话缓存：我们可以使用Nginx的`ssl_session_cache`指令来配置SSL会话缓存，以提高SSL握手的性能和安全性。例如：

```bash
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```

这将在共享内存中创建一个10MB的SSL会话缓存，并将会话超时设置为10分钟

通过以上配置，我们可以使Nginx支持HTTPS，并增加网站的安全性

## 反向代理

Nginx可以作为反向代理服务器，将请求转发到后端服务器上。通过反向代理，我们可以隐藏后端服务器的IP地址和端口号，从而增加安全性

反向代理的配置主要包括两个部分：1. 反向代理服务器的基本配置 2. 反向代理服务器的具体规则

1.  反向代理服务器的基本配置：

```bash
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend-server:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

上述配置表示，反向代理服务器会监听80端口，当收到请求时，将请求转发到后端服务器`http://backend-server:8080`。同时，反向代理服务器会在HTTP请求头中添加`Host`、`X-Real-IP`和`X-Forwarded-For`等信息，以便后端服务器获取客户端的真实IP地址和其他信息

2.  反向代理服务器的具体规则：

除了基本配置外，我们还可以根据具体的业务需求，为反向代理服务器配置不同的规则。例如，我们可以使用Nginx的`location`指令来匹配请求路径，并根据匹配结果执行相应的规则。例如：

```bash
location /admin/ {
    proxy_pass http://backend-server:8080;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    # other configuration
}
```

上述配置表示，当请求路径以`/admin/`开头时，反向代理服务器会将请求转发到后端服务器，并在HTTP请求头中添加`Host`、`X-Real-IP`和`X-Forwarded-For`等信息。我们还可以添加其他的配置，例如请求限制、缓存控制、错误处理等等

通过以上配置，我们可以使用Nginx作为反向代理服务器，隐藏后端服务器的IP地址和端口号，从而增加网站的安全性。下面，我们将介绍如何使用WAF来防御常见的Web攻击

## Web应用防火墙（WAF）

Web应用防火墙（WAF）是一种特殊的防火墙，可以检测和防御Web应用程序中的攻击。WAF通常基于规则引擎或机器学习等技术，可以检测并拦截SQL注入、跨站脚本攻击、命令注入、文件包含、远程代码执行等常见的Web攻击

Nginx也提供了基本的WAF功能，可以使用Nginx的`ngx_http_limit_req_module`和`ngx_http_limit_conn_module`模块来限制HTTP请求的速率和连接数，以防止DoS和DDoS攻击。同时，我们还可以使用第三方的Nginx模块，例如ModSecurity等，来增强Nginx的WAF功能

下面，我们将介绍如何使用Nginx的`ngx_http_limit_req_module`和`ngx_http_limit_conn_module`模块来防御DoS和DDoS攻击

## 防御DoS和DDoS攻击

DoS（拒绝服务攻击）和DDoS（分布式拒绝服务攻击）是一种常见的Web攻击，可以通过向目标服务器发送大量的请求或恶意流量，导致目标服务器无法正常工作

Nginx提供了`ngx_http_limit_req_module`和`ngx_http_limit_conn_module`模块，可以限制HTTP请求的速率和连接数，从而有效地防御DoS和DDoS攻击

1.  限制HTTP请求的速率：

```bash
http {
    limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;

    server {
        listen 80;
        server_name example.com;

        location / {
            limit_req zone=one burst=5;
            proxy_pass http://backend-server:8080;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```

上述配置表示，Nginx会创建一个名为`one`的共享内存区域，用于存储请求速率限制的相关信息。我们可以使用`limit_req_zone`指令来配置共享内存区域的大小和请求速率。在具体的反向代理规则中，我们可以使用`limit_req`指令来限制请求速率。例如，上述配置表示每秒最多处理10个请求，并且允许最多5个请求的突发

2.  限制HTTP连接数：

```bash
http {
    limit_conn_zone $binary_remote_addr zone=one:10m;

    server {
        listen 80;
        server_name example.com;

        location / {
            limit_conn one 10;
            proxy_pass http://backend-server:8080;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```

上述配置表示，Nginx会创建一个名为`one`的共享内存区域用于存储连接数限制的相关信息。我们可以使用`limit_conn_zone`指令来配置共享内存区域的大小。在具体的反向代理规则中，我们可以使用`limit_conn`指令来限制连接数。例如，上述配置表示每个IP地址最多允许10个并发连接

需要注意的是，如果配置不当，使用`ngx_http_limit_req_module`和`ngx_http_limit_conn_module`模块也有可能对正常的请求造成影响，从而影响Web应用程序的性能。因此，在配置时需要根据实际情况进行调整，并进行充分的测试和验证

除了以上的基本防御措施，我们还可以使用第三方的Nginx模块来增强Nginx的WAF功能。其中，ModSecurity是一种广泛使用的开源Web应用程序防火墙，可以检测并拦截Web应用程序中的各种攻击

下面，我们将介绍如何使用Nginx和ModSecurity来增强网站的安全性


## 增强网站的安全性

1.  安装ModSecurity：

```bash
cd /usr/src

wget https://www.modsecurity.org/tarball/3.0.4/modsecurity-3.0.4.tar.gz

tar -zxvf modsecurity-3.0.4.tar.gz

cd modsecurity-3.0.4

./configure

make

make install
```

2.  配置ModSecurity：

```bash
vi /usr/local/modsecurity/etc/modsecurity.conf

SecRuleEngine On
SecRequestBodyAccess On
SecResponseBodyAccess On
SecDebugLog /var/log/modsec_debug.log
SecAuditLog /var/log/modsec_audit.log
SecAuditLogType Serial
SecAuditLogFormat JSON
SecRule REQUEST_HEADERS:User-Agent "@rx (?:[^\x20\x09]|[\x20\x09][^\x20\x09])*" \
    "id:10001,\
    phase:2,\
    block,\
    msg:'Invalid User-Agent',\
    log,\
    status:400,\
    chain"
SecRule REQUEST_HEADERS:User-Agent "@rx (?:mozilla|chrome|safari)" \
    "id:10002,\
    phase:2,\
    pass,\
    t:none,\
    nolog,\
    chain"
SecRule REQUEST_HEADERS:User-Agent "!@rx (?:mozilla|chrome|safari)" \
    "id:10003,\
    phase:2,\
    block,\
    msg:'Invalid User-Agent',\
    log,\
    status:400"
```

上述配置表示，启用ModSecurity的规则引擎，并设置请求和响应体的访问权限。同时，我们还可以配置调试日志和审计日志的输出路径和格式。最后，我们定义了三条规则，用于检测并拦截恶意的User-Agent请求头。具体来说，第一条规则检测请求头中是否存在非法字符，如果存在则拦截请求；第二条规则检测请求头中是否包含常见的浏览器User-Agent信息，如果包含则放行；第三条规则检测请求头中是否包含非法的User-Agent信息，如果包含则拦截请求

3.  配置Nginx和ModSecurity：

```bash
vi /etc/nginx/nginx.conf

load_module modules/ngx_http_modsecurity_module.so;
modsecurity on;
modsecurity_rules_file /usr/local/modsecurity/etc/modsecurity.conf;
```

上述配置表示，加载ngx_http_modsecurity_module模块，并启用ModSecurity功能。同时，我们还指定了ModSecurity规则文件的路径

4.  测试安全性：

为了测试我们的安全防护措施是否有效，我们可以使用一些常见的Web应用程序攻击工具，例如SQL注入工具和XSS工具，来尝试攻击我们的Web应用程序。如果我们的安全防护措施能够有效地拦截这些攻击，那么我们的Web应用程序就可以更加安全地运行了


## 结论

在本文中，我们介绍了如何使用Nginx来实现网站安全，并通过示例代码详细说明了Nginx的基本防御措施和如何使用ModSecurity来增强Nginx的WAF功能。同时，我们也强调了在配置Nginx安全防护措施时需要充分测试和验证，以确保不会对Web应用程序的性能造成影响。最后，我们建议Web开发人员和运维人员定期关注安全漏洞和最新的安全防护措施，以保障Web应用程序的安全性
