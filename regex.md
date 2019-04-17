### RE
学点正则总有好处的。

### 正则标志`/g /i /m`

`/g` 表示该表达式将用来在输入字符串中查找`所有`可能的匹配，返回的结果可以是`多个`。

![1](https://ws1.sinaimg.cn/large/005DAKuvgy1g260lci2ocj306e05ydfp.jpg)

如果不加`/g`最多只会匹配`一个`

![2](https://ws1.sinaimg.cn/large/005DAKuvgy1g260jxgiyqj307y05zdfp.jpg)

`/i`  表示匹配的时候不区分`大小写`

不加`/i`

![3](https://ws1.sinaimg.cn/large/005DAKuvgy1g260ol46a5j305t05s745.jpg)

加了`/i`

![4](https://ws1.sinaimg.cn/large/005DAKuvgy1g260plgpbpj306a04omwz.jpg)

`/m` 表示`多行`匹配，什么是多行匹配呢？就是匹配换行符两端的潜在匹配。影响正则中的^$符号

不加`/m`，`^`属于元字符，后面会讲。

![5](https://ws1.sinaimg.cn/large/005DAKuvgy1g260s9mgd8j306z051web.jpg)

加了`/m`

![6](https://ws1.sinaimg.cn/large/005DAKuvgy1g260trt4v7j305u04rjr7.jpg)

### 正则表达式之元字符
- - -
1. ^匹配行的开始位置。

如果s9不是作为行开头的字符串，则它不会被匹配。

![7](https://ws1.sinaimg.cn/large/005DAKuvgy1g260wkkmo8j30600563yc.jpg)

2. $匹配行的结尾位置


### 参考
* https://www.cnblogs.com/william-lin/p/3480231.html
* http://www.cnblogs.com/zhuzhenwei918/p/6196661.html

