### 前言
- - -
每个月都得学点什么，以前对Dom Xss 只有一个模糊的印象，就是看不懂。在xss中，分为反射型，存储型和DOM型XSS，而且难以防范，在[安全小课堂](https://www.secpulse.com/archives/92286.html)中，Camaro师傅就介绍过Dom Xss的优势：
* 避开waf

  因为有些情况Dom Xss的Payload，可以通过`location.hash`，即设置为锚部分从`#`之后的部分，既能让JS读取到该参数，又不让该参数传入到服务器，从而避免waf检测。`location.search`也类似，它可以把部分参数放在`?`之后的部分。

* 长度不限

  这个很重要，关键时候！长度不够，可不是什么小药丸就解决的。
  
* 隐蔽性强

  攻击代码可以具有隐蔽性，持久性。例如使用Cookie和localStorage作为攻击点的DOM-XSS，非常难以察觉，且持续的时间长。

### 常见场景
- - -
 
#### 跳转
在很多场景下，业务需要实现页面跳转，常见的使用，`location.href()` `location.replace()` `location.assign()`这些方法通过Javascript实现跳转。我们第一时间可能想到的是限制不严导致任意URL跳转漏洞，而DOM XSS与此似乎“八竿子打不着”，实际上跳转部分参数可控，可能导致Dom xss。

首先我们来看个简单的例子:
```javascript
var hash = location.hash;
if(hash){
	var url = hash.substring(1);
	location.href = url;
}
```
变量hash为可控部分，并带入url中，变量hash控制的是#之后的部分，那么可以使用伪协议`#javascript:alert(1)`。

![1](https://i.loli.net/2019/05/13/5cd8e4b81191751510.jpg)

有些代码会使用indexOf()来对跳转的url进行判断是否合法。
```javascript
var t = location.search.slice(1)       // 变量t取url中?之后的部分
if(t.indexOf("url=") > -1 && t.indexOf("https") > -1){  // 限定传入url中要带有indexOf的关键词
	var pos = t.indexOf("url=")+4;       // 往后截取
	url = t.slice(pos,t.length);         
	location.href = url                 // 跳转
}
```
正常进行跳转`?url=https://www.baidu.com`


