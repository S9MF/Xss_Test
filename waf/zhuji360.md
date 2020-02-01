### 前言
- - -
360主机卫士是360旗下的服务器安全软件，为站长免费提供网站后门检测、木马查杀，网站补丁、漏洞防护等服务...

![1.png](https://ae01.alicdn.com/kf/Uad2732ca0c4943088ca09f550ff5caccL.png)

(虽然软件几年前就停止更新，官网挂了，但是从当时的评论上看软件还是很不错的。)

### 环境
- - -
360主机卫士Apache版

phpStudy2016(PHP-5.4.45+Apache)

测试代码
```
<?php   
  $input = @$_REQUEST["xss"];
  echo "<div>".$input."</div>"
?>
```
