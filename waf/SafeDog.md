最近在学习xss方面的知识，涉及到waf的测试，在这里做个记录，方面查阅。

### 0x00 搭建环境
- - -
  本地搭建测试waf测试，xss相关防护规则全部开启。
  
 ![1](https://ws1.sinaimg.cn/large/005DAKuvgy1g1sv90art7j30l10c0myc.jpg)
 
### 0x01 Self-Xss绕过
- - -
  测试脚本
  ```php
  <?php   
    $input = @$_REQUEST["xss"];
    echo "<div>".$input."</div>"
  ?>
  ```
首先思路就是一些被waf**遗漏的标签**，暂时不考虑**编码**或者**拼接字符串**这类思路，我们直接拿来测试。   

`<video src=1 onerror=alert(/xss/)>`

  
  
  
  
  
