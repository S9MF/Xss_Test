### 介绍
- - -
奇安信网站卫士是奇安信旗下为网站提供免费加速和防护的云服务平台。为站长免费提供了网站加速,智能高防DNS,防DDOS,防CC,防黑客和网站永久在线等服务，是百万站长的共同...

### 前言
- - -
本来不认识奇安信网站卫士的，那天chabug群，有位师傅发了张图，他去云锁官网测试SQL绕过，结果发现云锁官网用了`qianxin-waf`
![3.jpg](https://ae01.alicdn.com/kf/H68e5c883f9dc410daf4169a869d192f6Y.jpg)

![1.png](https://ae01.alicdn.com/kf/Hae1a6b931c164611ac860e308dd5e44dQ.png)

## 测试
- - -
既然来了测试下，先从容易满足的点出发，我就假设有个参数`?xss=`后面不用考虑闭合之类的，毕竟实战遇到的点各有不同。

首先来看个鸡肋而有趣的例子在推特看到的。

`<x oncopy=y=prompt,y``>z` `<x onpaste=y=prompt,y``>z`

![4.png](https://ae01.alicdn.com/kf/U64ba93f45194444b8dd25e8906db9238l.png)

`oncopy 事件`在用户拷贝元素上的内容时触发(反引号可以替换为括号)，所以触发的方法是按下 `CTRL + C`

`onpaste 事件`在用户向元素中粘贴文本时触发，所以触发的方法是按下 `CTRL + V`

![5.png](https://ae01.alicdn.com/kf/U126f5615b33d43caab65c5f42051f03bk.png)

再来看一个`<x oncut=y=prompt,y``>z`这个例子触发是`CTRL + X`以上3个例子就是`复制粘贴剪切` ━━∑(￣□￣*|||━━

比较实用的`onmouseover事件`被拦截了，还有

![7.png](https://ae01.alicdn.com/kf/Uafa9f3e5e9654b3cb61d886f8c70b7afU.png)
