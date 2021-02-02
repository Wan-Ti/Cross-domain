# Cross-domain

## 一：所谓的同源策略
源：window.origin或location.origin可以得到当前源</br>
源=协议+域名+端口号</br>

如果两个URL的</br>
协议+域名+端口号完全一致的话，这两个URL就是同源的</br>

同源策略定义：
浏览器规定：如果JS运行在源A里，那么就只能获取源A的数据+不能获取源B的数据，即不允许跨域</br>
目的：为了保护用户隐私


Ajax通信的一个主要限制就是浏览器的同源策略，XHR只能访问同一个域中的接口或资源。为了能实现跨域，出现了不少解决方案，主要介绍两种跨域手段</br>
<b>1：-----CORS</b></br>
只要在服务器的响应报文中指定一个跨源首部，就能绕过同源限制。这是最简单的一种解决方案,不用修改任何脚本代码。下面是这个跨源首部的两种设置方式，如果是星号，就表示服务器接受任何外部请求；如果指定了一个源信息（包含协议，域名和端口），那么服务器就只接受与这个源信息匹配的请求</br>
![](https://user-gold-cdn.xitu.io/2020/2/26/1707d30e800d7717?w=999&h=212&f=png&s=43760)
```
Access-Control-Allow-Origin:*
Access-Control-Allow-Origin:http://wanti.com
```
默认情况下，用CORS方式实现的跨域请求是不会携带凭据（如cookie等）的。如果要携带，就需要将XHR2对象的withCredentials属性设置未true,如下面代码所示：
```
var xhr=new  XMLHttpRequest();
xhr.open("POST","http://www.wanti.com/ajax/cors.php");
xhr.withCredentials=true;
xjr.send(null);
```
此时跨域首部也不能再用星号表示，需要设置具体的源信息，并且还要增加一个响应首部(Access-Control-Allow-Credentials),用来表示服务器接收带凭据的请求。
```
Access-Control-Allow-Origin:http://wanti.com
Access-Control-Allow-Credentials:true
```
如上所示，其实用XHR发起的请求，服务器都收到了，只是服务器在将响应返回给当前页面时，浏览器把响应拦截了。因此上面所做的一切都是为了让浏览器放行，使得当前页面能够顺利收到服务器的请求。

<b>2：-----JSONP</b></br>
JSONP是一种借助``` <script>```元素实现的跨域技术，它不会使用XHR对象，之所以能跨域，主要是因为```<script>```元素有以下两个特点
* (1):它的src属性能够访问任何URL的资源，不会受同源策略的执行
* (2):如果访问的资源包含JavaSCript代码，那么在下载后会自动执行。

JSONP就是基于这两点，再与服务器配合来实现跨域请求，它的执行步骤可分为一下6步
* (1):定义一个回调函数
* (2):用DOM方法动态的创建一个```<script>```元素
* (3)指定要请求的URL，并且将回调函数的名称作为一个参数传递过去
* (4)将```<script>```元素插入到当前文档中，请求开始
* (5)服务器接收传递过来的参数，然后将回调函数和数据以调用的形式输出
* (6)当```<script>```元素接收到响应中的脚本代码后，就会自动执行他们。

![](https://user-gold-cdn.xitu.io/2020/2/28/170892f01498c2ab?w=1083&h=381&f=png&s=53009)

![](https://user-gold-cdn.xitu.io/2020/2/28/170892f964c106a6?w=1281&h=408&f=png&s=84507)
除了``` <script>```元素能发起跨域请求之外，还有```<form>```, ```<img>```,```<iframe>```等元素也能发起跨域请求。像那些统计页面信息的第三方脚本，在获取相关数据后，会将这些参数作为URL的查询字符串，再通过图像的方式来发送跨域请求，把数据传递给服务器，具体做法如下
```
var img=new Image();
img.src="..."
```

