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
