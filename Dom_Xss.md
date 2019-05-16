### 前言
- - -
每个月都得学点什么，以前对Dom Xss 只有一个模糊的印象，就是看不懂。在xss中，分为反射型，存储型和DOM型XSS，而且难以防范，在[安全小课堂](https://www.secpulse.com/archives/92286.html)中，Camaro师傅就介绍过Dom Xss的优势：
* 避开waf

  因为有些情况Dom Xss的Payload，可以通过`location.hash`，即设置为锚部分从`#`之后的部分，既能让JS读取到该参数，又不让该参数传入到服务器，从而避免waf检测。`location.search`也类似，它可以把部分参数放在`?`之后的部分。

* 长度不限

  这个很重要，关键时候！长度不够，可不是什么小药丸就解决的。
  
* 隐蔽性强

  攻击代码可以具有隐蔽性，持久性。例如使用Cookie和localStorage作为攻击点的DOM-XSS，非常难以察觉，且持续的时间长。
  
![Dom](https://i.loli.net/2019/05/16/5cdcd26779d6f86233.jpg)

[图片来自](https://twitter.com/k2wanko/status/1126621174874529793)

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

这里扩展下，常见的几种伪协议"javascript:","vbscript:","data:"

IE下"vbscript:" 

`#vbscript:msgbox(IE)`
![3](https://i.loli.net/2019/05/13/5cd97dc035b0e48997.jpg)

"data:"

`#data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==`

#### 使用indexOf判断URL参数是否合法 

```javascript
var t = location.search.slice(1)       // 变量t取url中?之后的部分
if(t.indexOf("url=") > -1 && t.indexOf("http") > -1){  // 限定传入url中要带有indexOf的关键词
	var pos = t.indexOf("url=")+4;       // 往后截取
	url = t.slice(pos,t.length);         
	location.href = url                 // 跳转
}
```
indexOf会对url进行判断，是否存在关键字http，两个关键字都满足才能执行下面的部分，如果正常请求 `?url=http://cos.top15.cn` 没问题，我们构造`javascript:alert(1)`，并不会弹窗，正如我们上段所言，需要满足indexOf带有的关键字，所以只要构造`javascript:alert(1)//http`即可完成攻击，有的匹配`indexOf("http://cos.top15.cn")`，看似好像没问题，其实构造`javascript:alert(1)//http://cos.top15.cn`即可绕过，实际上这种使用indexOf来判断跳转来路域名的方法是不负责任，容易被绕过。

![2](https://i.loli.net/2019/05/13/5cd9709f2927273395.jpg)

#### 正则表达式缺陷

![4](https://i.loli.net/2019/05/14/5cda855258fe377201.jpg)

对跳转的url，通过正则进行判断是否合法http(s)，容易忽略元字符`^`(匹配行的开始位置)，加上和不加上，过滤的效果具有天壤之别。因为正则并没有严格限定跳转的url必须是http(s)开头，那么`javascript:alert(1)//http://cos.top15.cn`即可绕过。还有一些正则要求匹配上一些符号，字符串，参数类的加上就可以了，主要得看得懂令人头晕的正则。

#### 取值写入页面或动态执行 
接受url在前端显示，例如名称，地点，标题等，一般标题等都会将`<>`html实体编码，但在上传文件处，文件的的标题之类的，可能不会太重视。

```javascript
<div id="msgboard"></div>
<script>
 let hash = location.hash;
    if (hash.length > 1) {
        let hashValueToUse = unescape(hash.substr(1));
        let msg = "Welcome <b>" + hashValueToUse + "</b>!!";
        document.getElementById("msgboard").innerHTML = msg;
    }
</script>
```
innerHTML属性可以设置或者返回指定元素的HTML内容，此属性使用频繁且极为简单。如上代码变量hash是可控的，

![5](https://i.loli.net/2019/05/16/5cdd1b1df2cdc12587.jpg)
