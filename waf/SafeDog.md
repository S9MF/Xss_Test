最近在学习xss方面的知识，涉及到waf的测试，在这里做个记录，方面查阅。

### 0x00 搭建环境
  本地搭建测试waf测试，xss相关防护规则全部开启。
  
 ![1](https://ws1.sinaimg.cn/large/005DAKuvgy1g1sv90art7j30l10c0myc.jpg)
 
 ### 0x01 Self-Xss绕过
  测试脚本
  ```php
  <?php   
    $input = @$_GET["xss"];
    echo "<div>".$input."</div>"
  ?>
  ```
  
  
  
  
