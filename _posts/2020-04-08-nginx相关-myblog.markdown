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


# 正文

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











### 安装 

[https://juejin.im/post/5ea931866fb9a043815146fb](https://juejin.im/post/5ea931866fb9a043815146fb)



```bash
yum install nginx 
```



来安装 Nginx，然后我们在命令行中 `nginx -v` 就可以看到具体的 Nginx 版本信息，也就安装完毕了。



主要关注的文件夹有两个：

 

* `/etc/nginx/conf.d/` 文件夹，是我们进行子配置的配置项存放处，`/etc/nginx/nginx.conf` 主配置文件会默认把这个文件夹中所有子配置项都引入；

* `/usr/share/nginx/html/` 文件夹，通常静态文件都放在这个文件夹，也可以根据你自己的习惯放其他地方；







### 配置



#### location

1. `=` 精确匹配路径，用于不含正则表达式的 uri 前，如果匹配成功，不再进行后续的查找；
2. `^~` 用于不含正则表达式的 uri； 前，表示如果该符号后面的字符是最佳匹配，采用该规则，不再进行后续的查找；
3. `~` 表示用该符号后面的正则去匹配路径，区分大小写；
4. `~*` 表示用该符号后面的正则去匹配路径，不区分大小写。跟 `~` 优先级都比较低，如有多个location的正则能匹配的话，则使用正则表达式最长的那个；

如果 uri 包含正则表达式，则必须要有 `~` 或 `~*` 标志。



-----



* “location / {} ” :  是普通location , 遵循最大前缀匹配, 如果后面还有正则匹配, 如果正则匹配到了,正则匹配就会覆盖此配置; 
* “location = / {} ” :  用的是"="号, 是精确匹配, 只能匹配到  http://host:port/ 请求;



#### 校验配置



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







### 反向代理

 安全及权限。可以看出，使用反向代理后，用户端将无法直接通过请求访问真正的内容服务器，而必须首先通过Nginx。可以通过在Nginx层上将危险或者没有权限的请求内容过滤掉，从而保证了服务器的安全。 



----



在前端服务地址为 `fe.sherlocked93.club` 的页面请求 `be.sherlocked93.club` 的后端服务导致的跨域，可以这样配置：

```
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



```
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



###  负载均衡 



  例如一网站的内容被部署在若干台服务器上，可以把这些机子看成一个集群，那么Nginx可以将接收到的客户端请求“均匀地”分配到这个集群中所有的服务器上（内部模块提供了多种负载均衡算法），从而实现服务器压力的负载均衡。此外，nginx还带有健康检查功能（服务器心跳检查），会定期轮询向集群里的所有服务器发送健康检查请求，来检查集群中是否有服务器处于异常状态，一旦发现某台服务器异常，那么在以后代理进来的客户端请求都不会被发送到该服务器上（直到后面的健康检查发现该服务器恢复正常），从而保证客户端访问的稳定性。







###  rewrite  



```
    #请求跨域，这里约定代理请求url path是以/apis/开头
    location ^~/apis/ {
        # 这里重写了请求，将正则匹配中的第一个()中$1的path，拼接到真正的请求后面，并用break停止后续匹配
        rewrite ^/apis/(.*)$ /$1 break;
        proxy_pass https://www.kaola.com/;
    }  

```





###  **适配PC与移动环境** 





**境** 现在很多网站都存在PC站和H5站两个站点，因此根据用户的浏览环境自动切换站点是很常见的需求。Nginx可以通过内置变量$http_user_agent，获取到请求客户端的userAgent，从而知道用户处于移动端还是PC，进而控制重定向到H5站还是PC站。 以笔者本地为例，[pc端站点是mysite-base.com](http://xn--pcmysite-base-qr11aj07i537axka.com)，[H5端是mysite-base-H5.com](http://xn--H5mysite-base-H5-yi57a459t.com)。



pc端Nginx配置如下：



```
    location / {
        # 移动、pc设备适配
        if ($http_user_agent ~* '(Android|webOS|iPhone|iPod|BlackBerry)') {
            set $mobile_request '1';
        }
        if ($mobile_request = '1') {
            rewrite ^.+ http://mysite-base-H5.com;
        }
    }  

```



### 动态匹配（请求过滤）



>通常在开发环境或者测试环境的时候呢我们修改了代码，因为浏览器缓存，可能不会生效，需要手动清除缓存，才能看到修改后的效果，这里我们做一个配置让浏览器不缓存相关的资源。
>
>

```
location ~* \.(js|css|png|jpg|gif)$ {
    add_header Cache-Control no-store;
}
```



`~* \.(js|css|png|jpg|gif)$` 是匹配以相关文件类型然后单独处理。 `add_header` 是给请求的响应加上一个头信息`Cache-Control no-store`，告知浏览器禁用缓存，每次都从服务器获取 







###  开启 gzip 压缩

[网页GZIP压缩检测](http://tool.chinaz.com/gzips/)



gzip 是一种常用的网页压缩技术，传输的网页经过 gzip 压缩之后大小通常可以变为原来的一半甚至更小（官网原话），更小的网页体积也就意味着带宽的节约和传输速度的提升，特别是对于访问量巨大大型网站来说，每一个静态资源体积的减小，都会带来可观的流量与带宽的节省。



使用 gzip 不仅需要 Nginx 配置，浏览器端也需要配合，需要在请求消息头中包含 `Accept-Encoding: gzip`（IE5 之后所有的浏览器都支持了，是现代浏览器的默认设置）。一般在请求 html 和 css 等静态资源的时候，支持的浏览器在 request 请求静态资源的时候，会加上 `Accept-Encoding: gzip` 这个 header，表示自己支持 gzip 的压缩方式，Nginx 在拿到这个请求的时候，如果有相应配置，就会返回经过 gzip 压缩过的文件给浏览器，并在 response 相应的时候加上 `content-encoding: gzip` 来告诉浏览器自己采用的压缩方式（因为浏览器在传给服务器的时候一般还告诉服务器自己支持好几种压缩方式），浏览器拿到压缩的文件后，根据自己的解压方式进行解析。



先来看看 Nginx 怎么进行 gzip 配置，和之前的配置一样，为了方便管理，还是在 `/etc/nginx/conf.d/` 文件夹中新建配置文件 `gzip.conf` ：





```
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





#### Webpack 的 gzip 配置

[https://juejin.im/post/5ea931866fb9a043815146fb#heading-22](https://juejin.im/post/5ea931866fb9a043815146fb#heading-22)



```js
// vue-cli3 的 vue.config.js 文件
const CompressionWebpackPlugin = require('compression-webpack-plugin')

module.exports = {
  // gzip 配置
  configureWebpack: config => {
    if (process.env.NODE_ENV === 'production') {
      // 生产环境
      return {
        plugins: [new CompressionWebpackPlugin({
          test: /\.js$|\.html$|\.css/,    // 匹配文件名
          threshold: 10240,               // 文件压缩阈值，对超过10k的进行压缩
          deleteOriginalAssets: false     // 是否删除源文件
        })]
      }
    }
  },
  ...
}

```





那么为啥这里 Nginx 已经有了 gzip 压缩，Webpack 这里又整了个 gzip 呢，因为如果全都是使用 Nginx 来压缩文件，会耗费服务器的计算资源，如果服务器的 `gzip_comp_level` 配置的比较高，就更增加服务器的开销，相应增加客户端的请求时间，得不偿失。



如果压缩的动作在前端打包的时候就做了，把打包之后的高压缩等级文件作为静态资源放在服务器上，Nginx 会优先查找这些压缩之后的文件返回给客户端，**相当于把压缩文件的动作从 Nginx 提前给 Webpack 打包的时候完成，节约了服务器资源，所以一般推介在生产环境应用 Webpack 配置 gzip 压缩**。



### 图片防盗链



```
server {
  listen       80;        
  server_name  *.sherlocked93.club;
  
  # 图片防盗链
  location ~* \.(gif|jpg|jpeg|png|bmp|swf)$ {
    valid_referers none blocked server_names ~\.google\. ~\.baidu\. *.qq.com; 
    # 只允许本机 IP 外链引用，感谢 @木法传 的提醒，将百度和谷歌也加入白名单
    if ($invalid_referer){
      return 403;
    }
  }
}

```





## 配合vue





### vue-router History 模式



[https://router.vuejs.org/zh/guide/essentials/history-mode.html#%E5%90%8E%E7%AB%AF%E9%85%8D%E7%BD%AE%E4%BE%8B%E5%AD%90](https://router.vuejs.org/zh/guide/essentials/history-mode.html#后端配置例子)



`vue-router` 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。

如果不想要很丑的 hash，我们可以用路由的 **history 模式**，这种模式充分利用 `history.pushState` API 来完成 URL 跳转而无须重新加载页面。

```js
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```





不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 `http://oursite.com/user/id` 就会返回 404，这就不好看了。



```nginx
location / {
  try_files $uri $uri/ /index.html;
}
```

####  



当用户请求 `http://localhost/example` 时，这里的 `$uri` 就是 `/example`。 



try_files 会到硬盘里尝试找这个文件。如果存在名为 `/$root/example`（其中 `$root` 是项目代码安装目录）的文件，就直接把这个文件的内容发送给用户。 显然，目录中没有叫 example 的文件。然后就看 `$uri/`，增加了一个 `/`，也就是看有没有名为 `/$root/example/` 的目录。 



又找不到，就会 fall back 到 try_files 的最后一个选项 /index.html，发起一个内部 “子请求”，也就是相当于 nginx 发起一个 HTTP 请求到 `http://localhost/index.html`。 







## 最佳实践



* 为了使 Nginx 配置更易于维护，建议为每个服务创建一个单独的配置文件，存储在 `/etc/nginx/conf.d` 目录，根据需求可以创建任意多个独立的配置文件。

* 独立的配置文件，建议遵循以下命名约定 `<服务>.conf`，比如域名是 `sherlocked93.club`，那么你的配置文件的应该是这样的 `/etc/nginx/conf.d/sherlocked93.club.conf`，如果部署多个服务，也可以在文件名中加上 Nginx 转发的端口号，比如 `sherlocked93.club.8080.conf`，如果是二级域名，建议也都加上 `fe.sherlocked93.club.conf`。

* 常用的、复用频率比较高的配置可以放到 `/etc/nginx/snippets` 文件夹，在 Nginx 的配置文件中需要用到的位置 include 进去，以功能来命名，并在每个 snippet 配置文件的开头注释标明主要功能和引入位置，方便管理。比如之前的 `gzip`、`cors` 等常用配置，我都设置了 snippet。

* Nginx 日志相关目录，内以 `域名.type.log` 命名（比如 `be.sherlocked93.club.access.log` 和 `be.sherlocked93.club.error.log` ）位于 `/var/log/nginx/` 目录中，为每个独立的服务配置不同的访问权限和错误日志文件，这样查找错误时，会更加方便快捷。







## 问题





### 地址匹配

[https://blog.coding.net/blog/tips-in-configuring-Nginx-location](https://blog.coding.net/blog/tips-in-configuring-Nginx-location)



```
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
        ##
        
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



