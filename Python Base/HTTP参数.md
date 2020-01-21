# http header 所有参数详解

## 通用 general

```
Request URL: https://www.baidu.com/Request 
Method: GET
Status Code: 200 OK
Remote Address: 14.215.177.39:443
Referrer Policy: no-referrer-when-downgrade
```

- Request URL 请求地址
- Request Method 请求方式
- Status Code 状态码
- Remote Address 远程ip和端口号
- Referrer Policy  请求来源 
  - 我们知道，在页面引入图片、JS 等资源，或者从一个页面跳到另一个页面，都会产生新的 HTTP 请求，浏览器一般都会给这些请求头加上表示来源的 Referrer 字段。Referrer 在分析用户来源时很有用，有着广泛的使用。但 URL 可能包含用户敏感信息，如果被第三方网站拿到很不安全（例如之前不少 Wap 站把用户 SESSION ID 放在 URL 中传递，第三方拿到 URL 就可以看到别人登录后的页面）。之前浏览器会按自己的默认规则来决定是否加上 Referrer。需要详细讲讲 它包含了九种策略。 
    1. 空字符串默认为no-referrer-when-downgrade
    2. no-referrer 从字面意思就可以理解，不传递 Referrer 报头的值。
    3. no-referrer-when-downgrade 当发生降级（比如从 https:// 跳转到 http:// ）时，不传递 Referrer 报头。但是反过来的话不受影响。通常也会当作浏览器的默认安全策略。
    4. same-origin 同源，即当协议、域名和端口（如果有一方指定的话）都相同，才会传递 Referrer。
    5. origin 将当前页面过滤掉参数及路径部分，仅将协议、域名和端口（如果有的话）当作 Referrer。
    6. strict-origin 类似于 origin，但是不能降级
    7. origin-when-cross-origin 跨域时（协议、域名和端口只有一个不同）和 origin 模式相同，否则 Referrer 还是传递当前页的全路径。
    8. strict-origin-when-cross-origin 与 origin-when-cross-origin 类似，但不能降级。
    9. unsafe-url 任意情况下，都发送当前页的全部地址到 Referrer，最宽松和不安全的策略。

![img](https://ss2.baidu.com/6ONYsjip0QIZ8tyhnq/it/u=2141135441,3730658937&fm=173&app=25&f=JPEG?w=639&h=365&s=C512643289DE7CC8087141C90200B0B2)

传递方式

- Referrer-Policy 报头推荐的方式，直接在 Referrer-Policy 报头中设置。

Referrer-Policy: origin;

- Meta通过指定 name 值为 referrer 的 meta 标签，也可以达到相同的效果：

``content 可以是上面的指定的值，也可以是下面这几种旧的指令值，会自动作相应的转换，但不推荐这些旧的指令值：

Legacy Referrernever no-referrerdefault no-referrer-when-downgradealways unsafe-urlorigin-when-crossorigin origin-when-cross-origin

标签属性a 和 link 标签可以通过属性 rel 指定 noreferrer，仅对当前链接有效；a、area、link、iframe 和 img 还可以通过 referrerpolicy 指定仅针对当前链接的设置。请求头Request HeadersAccept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,/;q=0.8Accept-Encoding: gzip, deflate, brAccept-Language: zh-CN,zh;q=0.9Cache-Control: no-cacheConnection: keep-aliveCookie:Host: www.baidu.comPragma: no-cacheReferer:Upgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36accept (客户端能接收的资源类型)accept-encoding gzip通常效率最高， 使用最为广泛

gzip　　表明实体采用GNU zip编码 JPEG这类文件用gzip压缩的不够好。compress 表明实体采用Unix的文件压缩程序deflate　　表明实体是用zlib的格式压缩的identity　　表明没有对实体进行编码。当没有Content-Encoding header时， 就默认为这种情况

Accept-Language 作用： 浏览器申明自己接收的语言。 语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等等；Cache-Control常见值有private、no-cache、max-age、must-revalidate等，默认为private。

打开新窗口值为private、no-cache、must-revalidate，那么打开新窗口访问时都会重新访问服务器。

而如果指定了max-age值（单位为秒），那么在此值内的时间里就不会重新访问服务器

例如：Cache-control: max-age=5(表示当访问此网页后的5秒内再次访问不会去服务器)

在地址栏回车值为private或must-revalidate则只有第一次访问时会访问服务器，以后就不再访问。

值为no-cache，那么每次都会访问。

值为max-age，则在过期之前不会重复访问。

按后退按扭

值为private、must-revalidate、max-age，则不会重访问，

值为no-cache，则每次都重复访问

按刷新按扭

无论为何值，都会重复访问

Connection: keep-alive 当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的TCP连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的连接 Connection: close 代表一个Request完成后，客户端和服务器之间用于传输HTTP数据的TCP连接会关闭， 当客户端再次发送Request，需要重新建立TCP连接。cookie Cookie是用来存储一些用户信息以便让服务器辨别用户身份的（大多数需要登录的网站上面会比较常见），比如cookie会存储一些用户的用户名和密码，当用户登录后就会在客户端产生一个cookie来存储相关信息，这样浏览器通过读取cookie的信息去服务器上验证并通过后会判定你是合法用户，从而允许查看相应网页。当然cookie里面的数据不仅仅是上述范围，还有很多信息可以存储是cookie里面，比如sessionid等。host 作用: 请求报头域主要用于指定被请求资源的Internet主机和端口号，它通常从HTTP URL中提取出来的Pragma: no-cache可以应用到http 1.0 和http 1.1,而Cache-Control: no-cache只能应用于http 1.1.Referer 浏览器向web服务器发送请求的时候，referer用来告诉服务器从哪个页面链接过来的。Upgrade-Insecure-Requests 自动将网页上所有加载外部资源的 HTTP 链接换成 HTTPS 协议User-Agent 客户端使用的操作系统和浏览器的名称和版本响应头以百度为例子 Bdpagetype: 2Bdqid: 0xc2391ffe00056xxxCache-Control: privateConnection: Keep-AliveContent-Encoding: gzipContent-Type: text/html;charset=utf-8Date: Sun, 23 Sep 2018 09:00:15 GMTExpires: Sun, 23 Sep 2018 09:00:15 GMTServer: BWS/1.1Set-Cookie: BDSVRTM=234; path=/Set-Cookie: BD_HOME=1; path=/Set-Cookie: H_PS_PSSID=1464_2691; path=/; domain=.baidu.comStrict-Transport-Security: max-age=172800Transfer-Encoding: chunkedX-Ua-Compatible: IE=Edge,chrome=1Bdqid 估计是我的百度账号Cache-Control 见上面Connection 见上面Content-Encoding工作原理是这样子的，浏览器发送请求时，通过 Accept-Encoding 带上自己支持的内容编码格式列表；服务端从中挑选一种用来对正文进行编码，并通过 Content-Encoding 响应头指明选定的格式；浏览器拿到响应正文后，依据 Content-Encoding 进行解压。Content-Type常见的媒体格式有

text/html ： HTML格式text/plain ：纯文本格式text/xml ： XML格式image/gif ：gif图片格式image/jpeg ：jpg图片格式image/png：png图片格式以application开头的媒体格式类型：application/xhtml+xml ：XHTML格式application/xml ： XML数据格式application/atom+xml ：Atom XML聚合格式application/json ： JSON数据格式application/pdf ：pdf格式application/msword ： Word文档格式application/octet-stream ： 二进制流数据（如常见的文件下载）application/x-www-form-urlencoded ： 中默认的encType，form表单数据被编码为key/value格式发送到服务器（表单默认的提交数据的格式）

另外一种常见的媒体格式是上传文件之时使用的：

multipart/form-data ： 需要在表单中进行文件上传时，就需要使用该格式

Date 请求发送的日期和时间Expires 给出的日期/时间后，被响应认为是过时。如Expires:TSun, 23 Sep 2018 09:00:15 GMT 需和Last-Modified结合使用。用于控制请求文件的有效时间，当请求数据在有效期内时客户端浏览器从缓存请求数据而不是服务器端.当缓存中数据失效或过期，才决定从服务器更新数据。Server web服务器软件名称set-cookie 设置Http CookieStrict-Transport-Security全称HTTP Strict-Transport-Security 简称 HSTS

max-age是必选参数，是一个以秒为单位的数值，它代表着HSTS Header的过期时间，通常设置为1年，即31536000秒。includeSubDomains是可选参数，如果包含它，则意味着当前域名及其子域名均开启HSTS保护。preload是可选参数，只有当你申请将自己的域名加入到浏览器内置列表的时候才需要使用到它。关于浏览器内置列表，下文有详细介绍。在没有HSTS保护的情况下，当浏览器发现当前网站的证书出现错误，或者浏览器和服务器之间的通信不安全，无法建立HTTPS连接的时候，浏览器通常会警告用户，但是却又允许用户继续不安全的访问。但是,对于启用了浏览器HSTS保护的网站，如果浏览器发现当前连接不安全，它将仅仅警告用户，而不再给用户提供是否继续访问的选择，从而避免后续安全问题的发生.Transfer-Encodingtransfer-encoding的可选值有：chunked,identity ;transfer-encoding的可选值有：chunked,identity，从字面意义可以理解，前者指把要发送传输的数据切割成一系列的块数据传输，后者指传输时不做任何处理，自身的本质数据形式传输。举个例子，如果我们要传输一本“红楼梦”小说到服务器，chunked方式就会先把这本小说分成一章一章的，然后逐个章节上传，而identity方式则是从小说的第一个字按顺序传输到最后一个字结束。X-Ua-CompatibleX-UA-Compatible是IE8的一个专有属性，它告诉IE8采用何种IE版本去渲染网页，在html的标签中使用。X-Ua-Compatible: IE=Edge,chrome=1IE=edge告诉IE使用最新的引擎渲染网页，chrome=1则可以激活Chrome FrameChrome Frame可以让旧版IE浏览器使用Chrome的WebKit渲染引擎处理网页，因此旧版IE用户可以体验到包括HTML5在内的众多现代网页技术。