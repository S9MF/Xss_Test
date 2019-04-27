### 前言
`xsschop 长亭`是唯一一家正式在WAF产品中使用语义分析方法来进行 XSS 检测的。

具体看下文：
http://vlambda.com/wz_5kn0huYjduP.html

### 菜鸡的尝试
尝试手动正面刚xsschop，让我绝望，然后我就在想在特定情况下，我尝试能不能弹一下。

首先，我这里直接拿了P牛`"利用location来变形我们的XSS Payload"`文章中的测试代码，输出点在`<img src="这里">`

```php
<?php
header('X-XSS-Protection: 0');
$xss = isset($_GET['xss'])?$_GET['xss']:'';
$xss = str_replace(array("(",")","&","\\","<",">","'","`"), '', $xss);
echo "<img src=\"{$xss}\">";
?>
```

这段时间看了乌云知识库，在文章`"XSS挑战第一期Writeup"`了解到了这种姿势，即把()放在url查询部分，定义a变量赋值为location.search，再用location.search以数组键名索引的方式取回来()。
```
http://localhost/2.php?()&xss=" onerror=a=location.search;location="javascript:a"%2b"lert"%2ba[1]%2ba[2] "
```
![1](https://i.loli.net/2019/04/27/5cc45cd3a41fa.png)

但是放到xsschop检测时，可以看见前面的双引号无法闭合，前面的双引号一加，等级 Risk: High ，而且+不能url编码。

![2](https://i.loli.net/2019/04/27/5cc45d3991829.png)

真心给跪了，我主要想问下思路，也不需要大佬直接贴payload，那样可能凉的更快，主要是交流下。
