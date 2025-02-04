---
layout:     post
title:      "nginx相关"
date:       2020-04-08 17:40:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 后端
---

> “Yeah It's on. ”

[ https://juejin.im/post/5bacbd395188255c8d0fd4b2 ]( https://juejin.im/post/5bacbd395188255c8d0fd4b2 )

[https://juejin.im/post/5ea931866fb9a043815146fb](https://juejin.im/post/5ea931866fb9a043815146fb)

[快速掌握 Nginx 2 大核心用法](https://juejin.cn/post/7250710949140480057)



> “Nginx是一款轻量级的HTTP服务器，采用事件驱动的异步非阻塞处理方式框架，这让其具有极好的IO性能，时常用于服务端的反向代理和负载均衡。”

Nginx更长于底层服务器端资源的处理（静态资源处理转发、反向代理，负载均衡等），Node.js更擅长于上层具体业务逻辑的处理。两者可以实现完美组合，助力前端开发。

 但实际上，Nginx种看似简单的配置，实则学问深深。在Nginx实现一个同样的功能，不同的配置编写写法，效率上可能差上好几倍。而这些完全是在建立在对Nginx原理的深入理解和常年的配置运维经验上，哪怕是你们公司的后端都可能对Nginx的了解并不深入 

常见场景：

- 静态资源服务器
- 动态匹配
- 反向代理
- Gzip 压缩
- 负载均衡

nginx模块一般被分成三大类：handler、filter和upstream



# 基础

[https://juejin.im/post/5ea931866fb9a043815146fb](https://juejin.im/post/5ea931866fb9a043815146fb)

```bash
yum install nginx 
```

来安装 Nginx，然后我们在命令行中 `nginx -v` 就可以看到具体的 Nginx 版本信息，也就安装完毕了。

主要关注的文件夹有两个： 

* `/etc/nginx/conf.d/` 文件夹，是我们进行子配置的配置项存放处，`/etc/nginx/nginx.conf` 主配置文件会默认把这个文件夹中所有子配置项都引入；

* `/usr/share/nginx/html/` 文件夹，通常静态文件都放在这个文件夹，也可以根据你自己的习惯放其他地方；



## root

oot指令用于设置服务器的根目录，即服务器上文件资源的存放位置。

当客户端请求访问服务器上的文件时，nginx会根据请求的文件路径查找该文件。如果在请求的文件路径前面添加了root指令所设置的根目录路径，则nginx将会从根目录开始查找文件。

例如，如果root指令设置为"/var/www/html"，而客户端请求访问的文件路径为"/index.html"，那么nginx将会从"/var/www/html/index.html"路径下查找对应的文件。



## index

index指令用于指定默认的文件名或者文件名的列表，当客户端请求一个目录时，Nginx会尝试依次查找配置的文件名列表中的文件，并返回找到的第一个文件作为默认页面。如果请求的目录中没有配置的文件，则返回403 Forbidden错误。

index指令的语法为：

```
index file1 file2 ...;
```

具体含义如下：

- file1, file2, ...：表示要配置的文件名或者文件名列表，可以配置多个，以空格分隔。

例如，可以使用以下配置：

```
index index.html index.php;
```

这表示当客户端请求一个目录时，Nginx会首先尝试查找index.html文件，如果找不到，则查找index.php文件，并返回找到的第一个文件作为默认页面。



## proxy_pass

在nginx中，proxy_pass用于将请求转发给其他服务器。其作用如下：

1. 反向代理：当客户端通过nginx访问某个URL时，nginx将会使用proxy_pass指令将请求转发给其他服务器，并将服务器的响应返回给客户端。这样可以隐藏真实的服务器地址，提高安全性。
2. 负载均衡：nginx支持负载均衡，proxy_pass可以将请求平均分发给多个后端服务器，从而提高系统的性能和可用性。
3. 高可用性：当后端服务器出现故障时，proxy_pass可以根据配置的策略将请求转发给其他可用的后端服务器，保证系统的高可用性。
4. 缓存：nginx可以作为缓存服务器，使用proxy_pass将请求转发给缓存服务器，并将缓存的响应返回给客户端，从而加快响应速度和减轻后端服务器的压力。

总之，proxy_pass是nginx中一个非常强大的指令，可以实现反向代理、负载均衡、高可用性和缓存等功能。



## rewrite  

在Nginx中，rewrite是用于重写或修改URL的指令。它允许将一个URL重写为另一个URL，或者使用正则表达式匹配URL，并对匹配的部分进行替换。

```nginx
    #请求跨域，这里约定代理请求url path是以/apis/开头
    location ^~/apis/ {
        # 这里重写了请求，将正则匹配中的第一个()中$1的path，拼接到真正的请求后面，并用break停止后续匹配
        rewrite ^/apis/(.*)$ /$1 break;
        proxy_pass https://www.kaola.com/;
    }  
```

## upstream

在 Nginx 中，upstream 是用于定义负载均衡的服务器集群的模块。它的作用是将传入的请求分发给一组服务器，以达到提高系统的可用性和性能的目的。

upstream 模块允许你按照一定的算法（如轮询、IP 哈希、最少连接数等）将请求分发到后端服务器。通过设置多个服务器，可以实现负载均衡，提高系统的并发处理能力，防止单点故障，并优化系统的性能和可靠性。

upstream 在 Nginx 的配置文件中进行定义，可以指定一组服务器的地址和端口，并指定合适的负载均衡算法。Nginx 在接收到客户端请求后，根据指定的算法选择一个合适的后端服务器，并将请求转发给该服务器处理。

当后端服务器发生故障或不可用时，upstream 模块会进行自动的健康检查，并将故障服务器排除在负载均衡的范围之外，以确保请求只会被发送到可用的服务器上。

```nginx
upstream nest-server {
  server 192.168.1.6:3000;
  server 192.168.1.6:3001;
}

server {
  // ...

  location ^~ /api {
    proxy_pass nest-server;
  }
}
```



## try_files

nginx try_files 是一个指令，用于指定服务器如何处理静态文件。它用于在请求的URL与实际服务器上的文件路径之间进行映射和匹配。

try_files 主要功能如下：

1. 根据指定的路径尝试访问服务器上的文件或目录。可以指定多个路径，按照顺序尝试。
2. 当访问的文件或目录不存在时，可以指定一个后备的路径或处理方式。
3. 可以通过设置错误处理页面来处理请求的URL找不到的情况。

通过 try_files 指令，可以在请求中自动处理静态文件和目录的访问，提高服务器的性能和效率。它对于处理静态资源的网站非常有用，可以有效地优化用户的访问体验。

## location

1. `=` 精确匹配路径，用于不含正则表达式的 uri 前，如果匹配成功，不再进行后续的查找；
2. `^~` 用于不含正则表达式的 uri； 前，表示如果该符号后面的字符是最佳匹配，采用该规则，不再进行后续的查找；
3. `~` 表示用该符号后面的正则去匹配路径，区分大小写；
4. `~*` 表示用该符号后面的正则去匹配路径，不区分大小写。跟 `~` 优先级都比较低，如有多个location的正则能匹配的话，则使用正则表达式最长的那个；

如果 uri 包含正则表达式，则必须要有 `~` 或 `~*` 标志。

优先级是 精确匹配（=） > 高优先级前缀匹配（^~） > 正则匹配（～ ~*） > 普通前缀匹配

* “location / {} ” :  是普通location , 遵循最大前缀匹配, 如果后面还有正则匹配, 如果正则匹配到了,正则匹配就会覆盖此配置; 
* “location = / {} ” :  用的是"="号, 是精确匹配, 只能匹配到  http://host:port/ 请求;



## worker_processes

> 一般设置为默认值 auto

nginx 中的 worker_processes 指定了 Nginx 主进程的工作进程数目。这个参数决定了 Nginx 可以同时处理的连接数。通常情况下，这个数目应该设置为服务器的 CPU 核心数。

如果设置为 1，那么 Nginx 主进程将负责接收和处理所有的连接请求。这会降低 Nginx 的并发处理能力，但在低负载情况下可能是足够的。

如果设置为大于 1 的值，Nginx 主进程将创建多个工作进程，每个工作进程都能独立地接收和处理连接请求。这样可以提高 Nginx 的并发处理能力。每个工作进程将独立地监听端口并处理连接请求，通过负载均衡算法将请求分发给工作进程。



## events

```nginx
events {
  worker_connections 1024;
}
```

events模块的作用是定义与客户端的连接相关的参数和事件处理方法。

具体来说，events模块的作用包括以下几个方面：

1. 定义连接的最大数量：通过设置`worker_connections`参数，可以限制客户端连接的最大数量。当客户端连接达到这个限制时，新的连接将被拒绝。
2. 定义事件处理模型：通过设置`use`参数，可以选择Nginx使用哪种事件处理模型。常见的事件处理模型包括`epoll`、`kqueue`和`select`。
3. 定义连接超时时间：通过设置`client_timeout`参数，可以定义客户端连接的超时时间。当客户端连接在指定的时间内没有活动时，连接将被关闭。
4. 定义多个worker进程之间的事件处理方式：通过设置`multi_accept`参数，可以指定多个worker进程同时接受新的连接，而不是一个进程一个进程地接受。

总之，events模块的作用是定义Nginx与客户端连接相关的参数，以及选择事件处理模型，以提高Nginx的性能和可靠性。



## conf

在nginx中，有两个常见的配置文件：

1. nginx.conf：这是nginx主要的配置文件，用于全局配置nginx服务器的行为和设置。它包括了整个nginx服务器的基本配置信息，如worker_processes、pid等，还可以通过include指令来引入其他配置文件。
2. conf.d/default.conf：这是nginx默认的虚拟主机配置文件。它可以被用作默认服务器，当没有其他的虚拟主机配置文件匹配请求时，将会使用这个文件中的配置作为默认配置。

区别：

- 位置：nginx.conf是nginx的主配置文件，位于nginx的安装目录下，而default.conf是虚拟主机配置文件，位于conf.d目录下。
- 功能：nginx.conf包括了全局配置和引入其他配置文件的指令，而default.conf包含了虚拟主机的具体配置。
- 使用场景：nginx.conf在整个nginx服务器的运行中起主要作用，而default.conf是用来配置默认的虚拟主机。





### 校验配置

```bash
nginx -t
```

如果看到的是下面这种就是成功了

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

### 重新加载

向主进程发送信号，重新加载配置文件，热重启

```bash
nginx -s reload
```

这样就可以做到不停服务，平滑的更新 nginx 的配置文件。这样做的好处就是客户体验好，比如我正在数据传输，如果你这时重启 nginx，可能就会造成数据丢失。这个时候，我们就可以温柔的采用 nginx -s reload 命令加载配置文件。





## gzip 压缩

1. 打开 Nginx 配置文件。一般位于 `/etc/nginx/nginx.conf` 或者 `/etc/nginx/conf.d/default.conf`。

2. 在 http 配置块中添加如下行，开启 gzip 压缩功能：

   ```bash
   gzip on;
   ```

   这会启用服务器端的 gzip 压缩功能。

3. 配置 gzip 的压缩级别和压缩类型。添加如下行：

   ```bash
   gzip_comp_level n;
   gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
   ```

   其中 `n` 是压缩级别，可以是一个介于 1 和 9 之间的数字。数字越大，压缩率越高，但也会占用更多的 CPU 资源。压缩类型列表中指定了要压缩的 MIME 类型。

4. 重启 Nginx 服务，使配置生效。

   ```
   sudo systemctl restart nginx
   ```

现在你的 Nginx 服务器应该已经开启了 gzip 压缩功能。

---





```nginx
# /etc/nginx/conf.d/gzip.conf

gzip on; # 默认off，是否开启gzip
gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;

# 上面两个开启基本就能跑起了，下面的愿意折腾就了解一下
gzip_static on;
gzip_proxied any;
gzip_vary on;
gzip_comp_level 6;
gzip_buffers 16 8k;
# gzip_min_length 1k;
gzip_http_version 1.1;

```

* **gzip_types**：要采用 gzip 压缩的 MIME 文件类型，其中 text/html 被系统强制启用；
* **gzip_static**：默认 off，该模块启用后，Nginx 首先检查是否存在请求静态文件的 gz 结尾的文件，如果有则直接返回该 `.gz` 文件内容；
* **gzip_proxied**：默认 off，nginx做为反向代理时启用，用于设置启用或禁用从代理服务器上收到相应内容 gzip 压缩；
* **gzip_vary**：用于在响应消息头中添加 `Vary：Accept-Encoding`，使代理服务器根据请求头中的 `Accept-Encoding` 识别是否启用 gzip 压缩；
* **gzip_comp_level**：gzip 压缩比，压缩级别是 1-9，1 压缩级别最低，9 最高，级别越高压缩率越大，压缩时间越长，建议 4-6；
* **gzip_buffers**：获取多少内存用于缓存压缩结果，16 8k 表示以 8k*16 为单位获得；
* **gzip_min_length**：允许压缩的页面最小字节数，页面字节数从header头中的 `Content-Length` 中进行获取。默认值是 0，不管页面多大都压缩。建议设置成大于 1k 的字节数，小于 1k 可能会越压越大；
* **gzip_http_version**：默认 1.1，启用 gzip 所需的 HTTP 最低版本；







## 反向代理

 安全及权限。可以看出，使用反向代理后，用户端将无法直接通过请求访问真正的内容服务器，而必须首先通过Nginx。可以通过在Nginx层上将危险或者没有权限的请求内容过滤掉，从而保证了服务器的安全。 

在前端服务地址为 `fe.sherlocked93.club` 的页面请求 `be.sherlocked93.club` 的后端服务导致的跨域，可以这样配置：

```nginx
server {
  listen 9001;
  server_name fe.sherlocked93.club;

  location / {
    proxy_pass be.sherlocked93.club;
  }
}
```

这样就将对前一个域名 `fe.sherlocked93.club` 的请求全都代理到了 `be.sherlocked93.club`，前端的请求都被我们用服务器代理到了后端地址下，绕过了跨域。

这里对静态文件的请求和后端服务的请求都以 `fe.sherlocked93.club` 开始，不易区分，所以为了实现对后端服务请求的统一转发，通常我们会约定对后端服务的请求加上 `/apis/` 前缀或者其他的 path 来和对静态资源的请求加以区分，此时我们可以这样配置：

```nginx
# 请求跨域，约定代理后端服务请求path以/apis/开头
location ^~/apis/ {
    # 这里重写了请求，将正则匹配中的第一个分组的path拼接到真正的请求后面，并用break停止后续匹配
    rewrite ^/apis/(.*)$ /$1 break;
    proxy_pass be.sherlocked93.club;
  
    # 两个域名之间cookie的传递与回写
    proxy_cookie_domain be.sherlocked93.club fe.sherlocked93.club;
}
```

这样，静态资源我们使用 `fe.sherlocked93.club/xx.html`，动态资源我们使用 `fe.sherlocked93.club/apis/getAwo`，浏览器页面看起来仍然访问的前端服务器，绕过了浏览器的同源策略，毕竟我们看起来并没有跨域。

也可以统一一点，直接把前后端服务器地址直接都转发到另一个 `server.sherlocked93.club`，只通过在后面添加的 path 来区分请求的是静态资源还是后端服务，看需求了。



## 负载均衡 

[https://docs.nginx.com/nginx/admin-guide/load-balancer/tcp-udp-load-balancer/](https://docs.nginx.com/nginx/admin-guide/load-balancer/tcp-udp-load-balancer/)

 例如一网站的内容被部署在若干台服务器上，可以把这些机子看成一个集群，那么Nginx可以将接收到的客户端请求“均匀地”分配到这个集群中所有的服务器上（内部模块提供了多种负载均衡算法），从而实现服务器压力的负载均衡。此外，nginx还带有健康检查功能（服务器心跳检查），会定期轮询向集群里的所有服务器发送健康检查请求，来检查集群中是否有服务器处于异常状态，一旦发现某台服务器异常，那么在以后代理进来的客户端请求都不会被发送到该服务器上（直到后面的健康检查发现该服务器恢复正常），从而保证客户端访问的稳定性。

一共有 4 种负载均衡策略：

- 轮询：默认方式。
- weight：在轮询基础上增加权重，也就是轮询到的几率不同。
- ip_hash：按照 ip 的 hash 分配，保证每个访客的请求固定访问一个服务器，解决 session 问题。
- fair：按照响应时间来分配，这个需要安装 nginx-upstream-fair 插件。

举个例子：

通过 `proxy_pass` 与 `upstream` 即可实现最为简单的负载均衡。如下配置会对流量均匀地导向 `172.168.0.1`，`172.168.0.2` 与 `172.168.0.3` 三个服务器

```nginx
http {
  upstream backend {
      server 172.168.0.1;
      server 172.168.0.2;
      server 172.168.0.3;
  }

  server {
      listen 80;
      location / {
          proxy_pass http://backend;
      }
  }
}
```

加权轮询：

```nginx
upstream backend {
  server 172.168.0.1 weight=8;
  server 172.168.0.2 weight=1;
  server 172.168.0.3 weight=1;
}
```

ip_hash：

对每次的 IP 地址进行 Hash，进而选择合适的节点，如此，每次用户的流量请求将会打在固定的服务器上，利于缓存，也更利于 AB 测试等。

```nginx
upstream backend {
  server 172.168.0.1;
  server 172.168.0.2;
  server 172.168.0.3;
  ip_hash;
}
```

least_conn:

选择连接数最少的服务器节点优先负载

```nginx
upstream backend {
  server 172.168.0.1;
  server 172.168.0.2;
  server 172.168.0.3;
  least_conn;
}
```





## **适配PC端与移动端** 

[https://juejin.im/post/5f0d19b6f265da230f2848c3?utm_source=gold_browser_extension](https://juejin.im/post/5f0d19b6f265da230f2848c3?utm_source=gold_browser_extension)

如果 PC 端和移动端是一套代码则不会出现这个问题。**「这个问题出现在 PC 端和移动端是两套代码，却共用一个域名。」**

使用 `nginx` 配置如下，根据 UA 判断是否移动端，而走不同的逻辑 (判断UA是否移动端容易出问题)

```nginx
location / {
    // 默认 PC 端
    root /usr/local/website/web;

    # 判断 UA，访问移动端
    if ( $http_user_agent ~* "(Android|webOS|iPhone|iPad|BlackBerry)" ){
        root /usr/local/website/mobile;
    }

    index index.html index.htm;
}  

```

[SEO Impact of Vary: User-Agent header](https://webmasters.stackexchange.com/questions/134561/seo-impact-of-vary-user-agent-header)

[google mobile-sites-mobile-first-indexing](https://developers.google.com/search/docs/crawling-indexing/mobile/mobile-sites-mobile-first-indexing)

Vary：User-Agent 标头用于告诉浏览器、CDN 等（用于缓存目的）内容因用户代理而异（特别是对于移动和桌面版本）

[https://zhangzifan.com/vary-user-agent.html](https://zhangzifan.com/vary-user-agent.html)

Nginx 环境网站配置 Vary:User-Agent

```nginx
add_header Vary "User-Agent";
```











## 动态匹配（请求过滤）

>通常在开发环境或者测试环境的时候呢我们修改了代码，因为浏览器缓存，可能不会生效，需要手动清除缓存，才能看到修改后的效果，这里我们做一个配置让浏览器不缓存相关的资源。
>

```nginx
location ~* \.(js|css|png|jpg|gif)$ {
    add_header Cache-Control no-store;
}
```

`~* \.(js|css|png|jpg|gif)$` 是匹配以相关文件类型然后单独处理。 `add_header` 是给请求的响应加上一个头信息`Cache-Control no-store`，告知浏览器禁用缓存，每次都从服务器获取 



## 图片防盗链

```nginx
server {
  listen       80;        
  server_name  *.sherlocked93.club;
  
  # 图片防盗链
  location ~* \.(gif|jpg|jpeg|png|bmp|swf)$ {
    valid_referers none blocked server_names ~\.google\. ~\.baidu\. *.qq.com; 
    # 只允许本机 IP 外链引用，将百度和谷歌也加入白名单
    
    if ($invalid_referer){
      return 403;
    }
    
  }
}

```







# 最佳实践

* 为了使 Nginx 配置更易于维护，建议为每个服务创建一个单独的配置文件，存储在 `/etc/nginx/conf.d` 目录，根据需求可以创建任意多个独立的配置文件。

* 独立的配置文件，建议遵循以下命名约定 `<服务>.conf`，比如域名是 `sherlocked93.club`，那么你的配置文件的应该是这样的 `/etc/nginx/conf.d/sherlocked93.club.conf`，如果部署多个服务，也可以在文件名中加上 Nginx 转发的端口号，比如 `sherlocked93.club.8080.conf`，如果是二级域名，建议也都加上 `fe.sherlocked93.club.conf`。

* 常用的、复用频率比较高的配置可以放到 `/etc/nginx/snippets` 文件夹，在 Nginx 的配置文件中需要用到的位置 include 进去，以功能来命名，并在每个 snippet 配置文件的开头注释标明主要功能和引入位置，方便管理。比如之前的 `gzip`、`cors` 等常用配置，我都设置了 snippet。

* Nginx 日志相关目录，内以 `域名.type.log` 命名（比如 `be.sherlocked93.club.access.log` 和 `be.sherlocked93.club.error.log` ）位于 `/var/log/nginx/` 目录中，为每个独立的服务配置不同的访问权限和错误日志文件，这样查找错误时，会更加方便快捷。





# 问题



## 地址匹配

[https://blog.coding.net/blog/tips-in-configuring-Nginx-location](https://blog.coding.net/blog/tips-in-configuring-Nginx-location)

```nginx
        location / {
            root   html;
            index  index.html index.htm;
        }
        location = /helloworld {
                return 602;
        }
        location /helloworld {
                return 603;
        }
        
        ## 生产环境中如下两个 location 在另外一个文件中，通过 include 包含进来
        location /ii {
                return 604;
        }
        location ~ /ii/[^\/]+/[^\/]+ {
                return 605;
        }
        
        location ~ ^/helloworld/(scripts|styles|images).* {
                return 606;
        }
```

`http://localhost/helloworld/ii/hello/world` ==> 605 不符合预期，预期为【603】

为何我一个以 /helloworld 开头的 URL 会被匹配到 605 这个以 /ii 开头的 location 里面来。在当时的生产环境中，以 /ii 的配置统一放在另外一个文件中，这里是很难直观的察觉出来这个 /ii 跟访问的 URL 里面的 /ii 的关系。

最终选择了`~ "/ii/[^\/]+/[^\/]+"`这个作为最终的匹配项。

到这里问题就完全展现出来了，我们本来的意思，是要以 /ii 开头，后面有两个或者更多的 / 分割的 URL 模型才匹配，但是这里的正则表达式匹配写的不够精准，导致了匹配错误。正则表达式没有限制必须从开头匹配，所以才会匹配到 /helloworld/ii/hello/world 这样的 URL 。

解决办法就是在这个正则表达式前面加上 ^ 来强制 URL 必须以 /ii 开头才能匹配.

由
`/ii/[^\/]+/[^\/]+`
变成
`^/ii/[^\/]+/[^\/]+`

总结：

**location 尽量精确就尽量精确，否则出现问题的时候，非常难以查找**





## [Return 301 vs Rewrite](https://stackoverflow.com/questions/30165746/nginx-return-301-vs-rewrite)

[https://stackoverflow.com/questions/30165746/nginx-return-301-vs-rewrite](https://stackoverflow.com/questions/30165746/nginx-return-301-vs-rewrite)

return 301用于返回一个HTTP 301重定向响应。这意味着当一个客户端请求一个URL时，服务器会返回一个永久重定向的响应，告诉客户端去请求另一个URL。这通常用于将一个网站的旧URL重定向到一个新的URL，以确保旧URL的搜索排名、链接和书签等能够保留并重定向到新URL（为了 SEO）。

```nginx
location /old-url {
    return 301 http://example.com/new-url;
}
```

rewrite则用于重新写入URL，通常用于内部重定向，而不是返回一个重定向响应。rewrite通过修改URL路径或查询字符串来处理请求。这可以用于根据某些条件将请求转发到不同的后端服务器或处理逻辑。重写后的URL将被发送给客户端，但是客户端不会意识到URL已经被重写过。

````nginx
location /old-url {
    rewrite ^/old-url$ /new-url last;
}
````

