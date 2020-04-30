---
title: CORS
date: 2020-04-30 09:25:16

tags:

- 跨域
- nginx

---



#### 什么是CORS

跨域资源共享(CORS) 是一种机制，当一个资源从与该资源本身所在的服务器不同的域、协议或端口请求一个资源时，资源会发起一个跨域 HTTP 请求。出于安全原因，浏览器限制从脚本内发起的跨源HTTP请求。 例如，XMLHttpRequest和Fetch API遵循同源策略。 这意味着使用这些API的Web应用程序只能从加载应用程序的同一个域请求HTTP资源，除非响应报文包含了正确CORS响应头。

跨域资源共享（ CORS ）机制允许 Web 应用服务器进行跨域访问控制，从而使跨域数据传输得以安全进行。现代浏览器支持在 API 容器中（例如 XMLHttpRequest 或 Fetch ）使用 CORS，以降低跨域 HTTP 请求所带来的风险。


#### 什么情况下需要 CORS ？


跨域资源共享标准（ cross-origin sharing standard ）允许在下列场景中使用跨域 HTTP 请求：

- 由 `XMLHttpRequest` 或 `Fetch` 发起的跨域 HTTP 请求；
- Web 字体 (CSS 中通过 @font-face 使用跨域字体资源)；
- WebGL；
- canvas的 drawImage 将 Images/video 画面绘制到 canvas

#### CORS流程
跨域资源共享标准新增了一组 HTTP 首部字段，允许服务器声明哪些源站通过浏览器有权限访问哪些资源。另外，规范要求，对那些可能对服务器数据产生副作用的 HTTP 请求方法（特别是 GET 以外的 HTTP 请求，或者搭配某些 MIME 类型的 POST 请求），浏览器必须首先使用 `OPTIONS` 方法发起一个预检请求（`preflight request`），从而获知服务端是否允许该跨域请求。服务器确认允许之后，才发起实际的 HTTP 请求。在预检请求的返回中，服务器端也可以通知客户端，是否需要携带身份凭证（包括 Cookies 和 HTTP 认证相关数据）。

##### 简单请求
某些请求不会触发 CORS 预检请求。本文称这样的请求为“**简单请求**”。若请求满足所有下述条件，则该请求可视为“简单请求”：

- 使用下列方法之一：
GET
HEAD
POST

- Fetch 规范定义了对 CORS 安全的首部字段集合，不得人为设置该集合之外的其他首部字段。该集合为：
Accept
Accept-Language
Content-Language
Content-Type （需要注意额外的限制）
DPR
Downlink
Save-Data
Viewport-Width
Width

- Content-Type 的值仅限于下列三者之一：
text/plain
multipart/form-data
application/x-www-form-urlencoded

- 请求中的任意 XMLHttpRequestUpload 对象均没有注册任何事件监听器；XMLHttpRequestUpload 对象可以使用 XMLHttpRequest.upload 属性访问。
- 请求中没有使用 ReadableStream 对象。

##### 预检请求
“需预检的请求”（preflight）要求必须首先使用 OPTIONS   方法发起一个预检请求到服务器，以获知服务器是否允许该实际请求。"预检请求“的使用，可以避免跨域请求对服务器的用户数据产生未预期的影响。

当请求满足下述任一条件时，即应首先发送预检请求：

- 使用了下面任一 HTTP 方法：
PUT
DELETE
CONNECT
OPTIONS
TRACE
PATCH

- 人为设置了对 CORS 安全的首部字段集合之外的其他首部字段。该集合为：
Accept
Accept-Language
Content-Language
Content-Type (需要注意额外的限制)
DPR
Downlink
Save-Data
Viewport-Width
Width
- Content-Type 的值不属于下列之一:
application/x-www-form-urlencoded
multipart/form-data
text/plain

- 请求中的XMLHttpRequestUpload 对象注册了任意多个事件监听器。
- 请求中使用了ReadableStream对象。

##### 附带身份凭证的请求
 XMLHttpRequest 的 withCredentials 标志设置为 true，从而向服务器发送 Cookies
 
** 附带身份凭证的请求与通配符**
对于附带身份凭证的请求，服务器不得设置 Access-Control-Allow-Origin 的值为“*”。

这是因为请求的首部中携带了 Cookie 信息，如果 Access-Control-Allow-Origin 的值为“*”，请求将会失败。而将 Access-Control-Allow-Origin 的值设置为 http://foo.example，则请求将成功执行。


#### HTTP 响应首部字段

`Access-Control-Allow-Origin:  <origin> | *`

其中，origin 参数的值指定了允许访问该资源的外域 URI。对于不需要携带身份凭证的请求，服务器可以指定该字段的值为通配符，表示允许来自所有域的请求。

`Access-Control-Expose-Headers:  X-My-Custom-Header, X-Another-Custom-Header`

在跨域访问时，XMLHttpRequest对象的getResponseHeader()方法只能拿到一些最基本的响应头，Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma，如果要访问其他头，则需要服务器设置本响应头。如上面例子，浏览器就能够通过getResponseHeader访问`X-My-Custom-Header`和 `X-Another-Custom-Header` 响应头了。

`Access-Control-Max-Age: <delta-seconds>`
该头指定了preflight请求的结果能够被缓存多久，delta-seconds 参数表示preflight请求的结果在多少秒内有效。

`Access-Control-Allow-Credentials: true`
该头指定了当浏览器的credentials设置为true时是否允许浏览器读取response的内容。当用在对preflight预检测请求的响应中时，它指定了实际的请求是否可以使用credentials。请注意：简单 GET 请求不会被预检。

`Access-Control-Allow-Methods: <method>[, <method>]*`

该字段用于预检请求的响应。其指明了实际请求所允许使用的 HTTP 方法。

`Access-Control-Allow-Headers: <field-name>[, <field-name>]*`

该首部字段用于预检请求的响应。其指明了实际请求中允许携带的首部字段。

#### HTTP 请求首部字段
以下是可用于发起跨域请求的首部字段。这些首部字段无须手动设置。 

`Origin: <origin>`
此字段表明预检请求或实际请求的源站。值为源站 URI，不包含任何路径信息，只是服务器名称。在所有访问控制请求中，Origin 字段总是被发送。
有时候将该字段的值设置为空字符串是有用的，例如，当源站是一个 data URL 时。

`Access-Control-Request-Method: <method>`
此字段用于预检请求。其作用是，将实际请求所使用的 HTTP 方法告诉服务器。

`Access-Control-Request-Headers: <field-name>[, <field-name>]*`
此字段用于预检请求。其作用是，将实际请求所携带的首部字段告诉服务器。

####  常见服务器端nginx的cors配置

```lua
location / {
	set $origin '';
	if ($http_origin ~ "^https://www.test.com$") {
		set $origin $http_origin;
	}
	if ($http_origin ~ "^https://test.com$") {
		set $origin $http_origin;
	}
	if ($request_method = OPTIONS ) {
		add_header 'Access-Control-Allow-Origin' $origin;
		add_header 'Access-Control-Allow-Methods' 'GET,POST,OPTIONS';
		add_header 'Access-Control-Max-Age' '1728000';
		add_header 'Content-Length' '0';
		add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';
		add_header 'Content-Type' 'text/plain';
		return 204;
	}
	add_header 'Access-Control-Allow-Origin' $origin;
	add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
	add_header 'Access-Control-Allow-Credentials' 'true';
	add_header 'Access-Control-Max-Age' '1728000';
	add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';
	add_header 'Access-Control-Expose-Headers' 'Connection,Content-Encoding';
}
```
#### Apache CORS

限制对某些URI的访问
最有效的方法之一，利用Apache rewrite, 环境变量，还有headers使Access-Control-Allow-* 对某些特定的URI生效，比如，以无认证信息形式利用GET跨域请求api(.*).json。

```
RewriteRule ^/api(.*)\.json$ /api$1.json [CORS=True]
Header set Access-Control-Allow-Origin "*" env=CORS
Header set Access-Control-Allow-Methods "GET" env=CORS
Header set Access-Control-Allow-Credentials "false" env=CORS
```

####  后端PHP的CORS snippets
```php
<?php 
if($_SERVER['REQUEST_METHOD'] == "GET"){
    header('Content-Type: text/plain');
    echo "This HTTP resource is designed to handle POSTed XML input from arunranga.com and not be retrieved with GET";
   
}
elseif($_SERVER['REQUEST_METHOD'] == "OPTIONS"){
    // 告诉客户端我们支持来自 test.com 的请求并且预请求有效期将仅有20天
    if($_SERVER['HTTP_ORIGIN'] == "https://test.com"){
		header('Access-Control-Allow-Origin: https://test.com');
		header('Access-Control-Allow-Methods: POST, GET, OPTIONS');
		header('Access-Control-Allow-Headers: X-TOKEN');
		header('Access-Control-Max-Age: 1728000');
		header("Content-Length: 0");
		header("Content-Type: text/plain");
   		//exit(0);
    }
    else{
		header("HTTP/1.1 403 Access Forbidden");
		header("Content-Type: text/plain");
		echo "You cannot repeat this request";
    }
}
elseif($_SERVER['REQUEST_METHOD'] == "POST") {
    /* 通过首先获得XML传送过来的blob来处理POST请求，然后做一些处理, 最后将结果返回客户端
    */
    if($_SERVER['HTTP_ORIGIN'] == "https://test.com") {
            $postData = file_get_contents('php://input');
            $document = simplexml_load_string($postData);
            
            // 对POST过来的数据进行一些处理

            $ping = $_SERVER['HTTP_X_TOKEN'];
           
                       
            header('Access-Control-Allow-Origin: https://test.com');
            header('Content-Type: text/plain');
            echo // 处理之后的一些响应
    }
    else
        die("POSTing Only Allowed from arunranga.com");
}
else
    die("No Other Methods Allowed");

?>
```

