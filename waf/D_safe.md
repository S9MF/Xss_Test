### 前言
- - -
今天又换了款waf，因为waf要IIS环境，我发现我是个手残党，一看就懂，操作就废，搭建个IIS环境没成功。后来有朋友说我买的服务器有重置系统，那里可以选择ASP/.NET环境，今天搭建好了。

![1](https://ws1.sinaimg.cn/large/006Xzox4ly1g1ky4we8ghj306o06oq34.jpg)

### 提醒
- - -
本文分两部分，前部分是总结新学的姿势，后半部分测试Waf。

### 数组方式
- - -
前两篇文章，我们利用js里的对象成员方法也可以用数组的形式的表示，以此构造了许多payload，在数组内将敏感函数拼接，以此来绕过。以`top`对象为演示的，在Javascrip中，可以连接数组的函数有其他可以补充的。

#### map()函数
- - -
map函数可以返回一个数组`[1].map`，而且我们在使用`map`函数的时候往往会传入一个函数，如果我们传递一个`alert`函数，那么将触发xss。
```php
[1].map(alert)
```
![2](https://ws1.sinaimg.cn/large/005DAKuvgy1g200cpnu7bj30lk03z0sp.jpg)

类似的数组操作函数不在少数，我所知的就有**find**，**every**，**filter**，**forEach**，**findIndex**。它们和**map**函数都有一个共同的特点，可以返回数组，而且在使用的同时还以可以传入一个函数，这就为我们构造payload提供更多的选择。

我们思考一下，在那些情况下我们可以使用，其实满多，可以先看个demo。

```php
<img src=1 onerror=[1].filter(alert)>
```
成功弹窗

![3](https://ws1.sinaimg.cn/large/005DAKuvgy1g203d50hfzj30ql094aal.jpg)

那么如何更进一步呢，我们先思考下面这个例子。

```php
<img src=1 onerror=['ale'%2b'rt'].map(top['ev'%2b'al'])[0]['valu'%2b'eOf']()(/xss/)>
```
将`alert`函数以数组的方式拼接保存，通过嵌套`top`对象拆分带入`eval`函数，`valueOf`方法将返回值`/xss/`，成功弹窗。

![4](https://ws1.sinaimg.cn/large/005DAKuvgy1g204i32laqj30qb09mgm7.jpg)

从上面的例子中，我们不难看出，javascrip的这类对象方法不在少数，如果我们要寻找其他类似函数，首先满足`返回数组`，或者`字符串`，再能够带入我们的`函数`。

### function函数
- - -
在javascript，定义函数的方式有两种：一种是函数声明，另一种就是函数表达式。

函数表达式的基本特征是没有函数名。

![5](https://ws1.sinaimg.cn/large/005DAKuvgy1g204yvqam0j30fe05wglo.jpg)
