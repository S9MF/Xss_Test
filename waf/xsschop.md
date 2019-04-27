### 前言
- - -
[xsschop](https://xsschop.chaitin.cn/) 是长亭XSS语义分析引擎的Demo站，[详细介绍](https://mp.weixin.qq.com/s/32w0eRS4_Ko3ItYRD7rPUg)长亭是唯一一家正式在WAF产品中使用语义分析方法来进行 XSS 检测的，我们可以通过这个Demo站来测试学习xss知识。

#### 简单的尝试
- - -
`<script>alert(1)</script>`   

![1](https://i.loli.net/2019/04/27/5cc4159cae029.jpg)

把括号换成反引号，这里科普下在ES6中模板字符串用反引号标识``(`)``，``有人说alert(1)等同于alert`1`，``这两种方式确实都可以弹框，但是当你要`document.cookie`的时候。

用括号的。

![2](https://i.loli.net/2019/04/27/5cc415c7ecbf2.jpg)   

用反引号的。

![3](https://i.loli.net/2019/04/27/5cc415dce27e4.jpg)

查阅文档模板字符串即反引号的作用，是允许嵌入表达式的字符串字面量，我个人理解是如果你要用反引号执行属性或者对象是不行的。

继续回归正题，我们测试用反引号。

![4](https://i.loli.net/2019/04/27/5cc4160a42b39.jpg)

可以看到现在的风险是中等的，然后我觉得alert不行，那就换个函数prompt，confirm依然是中等。

当时我深感无奈，就在反引号中加了`?`问号。

![5](https://i.loli.net/2019/04/27/5cc4187ba305d.png)

竟然过了???

![6](https://i.loli.net/2019/04/27/5cc418ad3223f.png)
