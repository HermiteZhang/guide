## 字符编码之Unicode、UTF-8、UTF-16、ASCLL、GBK



### 1 、结论

* Unicode：字符集，使用2字节展示字符，
* UTF-8：Unicode字符编码表，前缀码，兼容ASCll，1-4字节可变长编码
* UTF-16：Unicode字符编码表，不兼容ASCll，2，4字节可变长编码，大部分采用2个字节
* UTF-32：Unicode字符编码表，4字节固定编码，
* ASCll：字符集，使用一个字节表示，共128个字符，不支持中文
* GB系列：国内采用的汉字编码字符集，GB2312<GBK<GB18030

### 2、前置理论

#### 2. 1、计算机相关理论

  * 位\bit\比特位：计算机最小数据单位
  * 字节\byte：1byte = 8bit
  * 字符：字面意思，计算机最终展现的图形单位，根据编码规则的不同，一个字符所占空间也不同，
      *  ASCII码中，一个英文字母（不分大小写）占一个字节的空间，一个中文汉字占两个字节的空间。 
      *  UTF-8编码中，一个英文字符等于一个字节，一个中文等于三个字节。 
      *  Unicode其他编码中，一个英文等于两个字节，一个中文等于两个字节。 
#### 2.2 字符编码学术语

* 码位：
* 码值:
* 码空间:
* 码元:

#### 2.3、通用字符编码（UCS）

​	 是由ISO制定的**ISO 10646**（或称**ISO/IEC 10646**）标准所定义的标准字符集。 现在通用的Unicode采用的是UCS-2标准，新版Unicode使用UCS-4标准

#### 2.4、字符编码

* 【现代编码模型】现代编码模型自底向上分为五个层次：
  - 抽象字符表 ACR (Abstract Character Repertoire)：ASCll，Unicode
  - 编码字符集 CCS (Coded Character Set)： ISO-8859-1 ，GB2312
  - 字符编码表 CEF (Character Encoding Form)：UTF-8
  - 字符编码方案 CES (Character Encoding Schema)：UTF16/32
  - 传输编码语法 TES (Transfer Encoding Syntax)：base64



### 3、Unicode

> 狭义的Unicode指Unicode字符集，又名统一码，是一种抽象字符表（字符集） ，负责为每一个字符分配一个唯一的 码点（ID），广义的Unicode是一个标准，定义了一个字符集以及一系列的编码规则，

#### 3.1、基本理论

---


* 【编码方式-现在通用USC-2】使用16位（2字节）的编码空间， 对应于 UTF-16，理论上一共最多可以表示 2<sup>16</sup>（65536）个字符。基本多文种平面字符编码表示方式为  **U+hhhhh**
* 【编码方式-最新USC-4】 使用32位（4字节）的编码空间，对应UTF-32
* 【实现方式（UTF）】Unicode 的实现方式称为 Unicode转换格式（Unicode Transformation Format，简称为 UTF）。UTF的出现是为了节省空间和解决平台兼容的目的。
  * 节省空间：主要是ASCLL字符（1字节）使用原始 Unicode 编码（2字节）传输，其第一字节的 8 位始终为 0。这就造成了比较大的浪费。对于这种情况，出现了 UTF-8 编码，这是一种变长编码，它将基本 7 位 ASCII 字符仍用 7 位编码表示，占用一个字节（首位补 0）。
  * 平台兼容：即使是相同的原始Unicode 编码，由于每个字符占用了两个字节，不同平台对字节顺序的理解是不一致（如Mac OS上是从低字节开始读取， Windows 上从高字节开始）。这时同一字节流可能会被解释为不同内容，<br>如某字符为十六进制编码 4E59（“乙”） ，按两个字节拆分为 4E 和 59，那么在 Mac OS 会认为 4E59 为 594E（“奎”）。

#### 3.2、拓展概念

---

* 【字符平面】 Unicode的编码空间使用U+0000到U+10FFFF。共17*2<sup>16</sup>码位来映射字符。Unicode将编码空间划分为17个平面（plane），每个平面包含2<sup>16</sup>个码位。第一个平面称为**基本多语言平面**，其他平面称为**辅助平面**。基本多语言平面内，从U+D800到U+DFFF之间的码位区段是永久保留不映射到Unicode字符。 

### 4、UTF-8

> **UTF-8**（**8-bit Unicode Transformation Format**）是一种针对Unicode的可变长度**字符编码表**，也是一种前缀码

#### 4.1、基本理论

* 【结构】理论上采用1-6个字节的编码空间为每个字符编码，2003年被强制规定最多使用4个字节，虽然Unicode字符现在最多只有21位

* 【实现方式 】 Unicode 和 UTF-8 之间的转换关系表如图，  `x` 字符表示码点占据的位

| 码点的位数 | 码点起值  |  码点终值  | 字节序列 |   Byte 1   |   Byte 2   |   Byte 3   |   Byte 4   |   Byte 5   |   Byte 6   |
| :--------: | :-------: | :--------: | :------: | :--------: | :--------: | :--------: | :--------: | :--------: | :--------: |
|     7      |  U+0000   |   U+007F   |    1     | `0xxxxxxx` |            |            |            |            |            |
|     11     |  U+0080   |   U+07FF   |    2     | `110xxxxx` | `10xxxxxx` |            |            |            |            |
|     16     |  U+0800   |   U+FFFF   |    3     | `1110xxxx` | `10xxxxxx` | `10xxxxxx` |            |            |            |
|     21     |  U+10000  |  U+1FFFFF  |    4     | `11110xxx` | `10xxxxxx` | `10xxxxxx` | `10xxxxxx` |            |            |
|     26     | U+200000  | U+3FFFFFF  |    5     | `111110xx` | `10xxxxxx` | `10xxxxxx` | `10xxxxxx` | `10xxxxxx` |            |
|     31     | U+4000000 | U+7FFFFFFF |    6     | `1111110x` | `10xxxxxx` | `10xxxxxx` | `10xxxxxx` | `10xxxxxx` | `10xxxxxx` |

* 【特性】

  * 完全兼容 ASCII 码（ U+0000~U+007F ）并将ASCll作为自己的子集
  * 每个字节都有标记位集（拥有字节头部标识，如0，110，10）
  * 所有>U+007F的UCS字符被编码为一个多个字节的串。因此，ASCII字节（0x00-0x7F）不可能作为任何其他字符的一部分。
  * XML 及其子集 XHTML 采用 UTF-8 作为标准字集，使用&#nnn;的格式显示特定的字符。nnn 代表该字符的十进制 Unicode 代码。如果采用 十六进制 代码，在编码之前加上x字符即可。但部分旧版本的浏览器可能无法识别十六进制代码。
* UTF-8以字节为编码单元，它的字节顺序在所有系统中都是一様的，没有字节序的问题，因此不需要BOM（字节顺序标记），  
  
*   与其他Unicode编码相比，特别是UTF-16，在UTF-8中ASCII字符占用的空间只有一半，可是在一些字符的UTF-8编码占用的空间就要多出1/3，特别是中文、日文和韩文这样的方块文字 

#### 4.2、转换实例

* 与Unicode字符集转换

  ```text
  
  例：[乙]=U+4E59 (0x4E59)
  按照《Unicode 和 UTF-8 之间的转换关系表》可知「乙」字的码位 U+0x4E59 属于第三行（U+0800~U+FFFF）的范围：
  =========================================================
         4    E    5    9 
      0100 1110 0101 1001 		-二进制转换
  -------------------------------
  1110xxxx 10xxxxxx 10xxxxxx      -模版
  	0100   111001   011001		-按照模版4x,6x,6x转换4e59
  11100100 10111001 10011001      -带入模版
  -------------------------------
  E4       B9        99			-转换成2进制
  ========================================================
  [乙]=U+4E59 = 0xE4B999(UTF-8)
  
  ```

* 与XMl转换：

### 5、UTF-16

> **UTF-16**是Unicode字符编码五层次模型的第三层：字符编码表（Character Encoding Form，也称为"storage format"）的一种实现方式。广义的 UTF-16也可以是第四层（包括字节序），字符大部分采用16位进行编码。

#### 5.1、基本理论

* 【结构】 使用2-4个字节的编码空间，为每个字符编码，是一种变长表示 。

* 【实现方式】

  * **规则1**、【U+0000 ~ U+D7FF】 与Unicode编码一致（ 基本多语言平面 ）

  * **规则2**、【U+D800 ~ U+DFFF 】不用解析，在这个区间内Unicode编码不代表任何东西

  * **规则3**、【U+E000 ~ U+FFFF】与Unicode编码一致（ 基本多语言平面 ）

  * **规则4**、【U+10000 ~ U+10FFFF】 辅助平面 代理对，有相应的算法解析，解析后将生成两个落在U+D800 ~ U+DFFF区间内的字符，此时占用空间为4个字节，具体方法是：

    1. 码位减去 `0x10000`，得到的值的范围为20比特长的 `0...0xFFFFF`。
    
    2. 高位的10比特的值（加上 `0xD800` 得到高位代理（high surrogate）。由于高位代理比低位代理的值要小，所以为了避免混淆使用，Unicode标准现在称高位代理为**前导代理**（lead surrogates）。

    3. 低位的10比特的值被加上 `0xDC00` 得到低位代理（low surrogate）又称**后尾代理**（trail surrogates）。
    
#### 5.2 、转换实例

    ```text
    
    重点解析规则4，将Unicode编码转换成UTF-16
    
    例：将U+64321（16进制）转成UTF-16编码，满足规则4
    U+64321
       6    4    3    2    1    --16进制
    ‭0110 0100 00 11 0010 0001‬   --2进制
     -‭ 1 0000 00 00 0000 0000‬   --减去0x10000【步骤1】
    ------------------------------
    0101 0100 00 11 0010 0001   --等于0x54321（20位）
    0101 0100 00                --高10位(Vh)
                 11 0010 0001   --低10位（Vl）
    --------------------------------
    Vh +0xD800
    =0xD950                     --【步骤2】
    
    Vl +0xDC00 
    =0xDF21                     --【步骤3】
    ---------------------------------
    U+64321 = 0xD950，0xDF21 (UTF-16，两个字符存储，都落点在第一平面的保留区间)
    			
    ```

#### 5.3、拓展概念

* 【与USC-2的关系】UTF-16可看成是UCS-2的父集。

  在没有辅助平面字符（surrogate code points）前，UTF-16与UCS-2所指的是同一的意思。但当引入辅助平面字符后，就称为UTF-16了。现在若有软件声称自己支持UCS-2编码，那其实是暗指它不能支持在UTF-16中超过2字节的字集。对于小于0x10000的UCS码，UTF-16编码就等于UCS码。

* 【编码模式】

  UTF-16以字符作为编码单元，所以会存在字节序的问题，目前UTF-16的大尾序和小尾序存储形式都在使用。通用平台中mac采用大尾序，linunx和windows采用小尾序，因此在UTF-16文件的开首，都会放置一个U+FEFF字符作为BOM（UTF-16 LE以 `FF FE` 代表，UTF-16 BE以 `FE FF` 代表），以显示这个文本文件是以UTF-16编码，其中U+FEFF字符在UNICODE中代表的意义是 `ZERO WIDTH NO-BREAK SPACE`，顾名思义，它是个没有宽度也没有断字的空白。

   以下的例子有四个字符在不同编码下的区别：


|       编码名称  |    编码次序  |    BOM   |   朱  |  ,   |  聿   |  𪚥   |
| :------------------: | :-------------: | :---: | :---: | :---: | ----- | ----------- |
|      UTF-16 LE       | 小尾序，不含BOM |       | 31 67 | 2C 00 | 7F 80 | 69 D8 A5 DE |
|      UTF-16 BE       | 大尾序，不含BOM |       | 67 31 | 00 2C | 80 7F | D8 69 DE A5 |
|      UTF-16 LE       | 小尾序，包含BOM | FF FE | 31 67 | 2C 00 | 7F 80 | 69 D8 A5 DE |
|      UTF-16 BE       | 大尾序，包含BOM | FE FF | 67 31 | 00 2C | 80 7F | D8 69 DE A5 |



### 6、UTF-32

> **UTF-32（32-bit Unicode Transformation Format**）是一种针对Unicode的固定长度**字符编码表**，同UTF-16广义的UTF-32也可以是第四层（包括字节序）， UTF-32中的每个32位值代表一个Unicode码位，并且与该码位的数值完全一致 



### 7、ASCll码

> ascll 码由电报码发展而来， 是基于拉丁字母的一套电脑编码系统。它主要用于显示现代英语

特征

* 一共定义了128个字符，包括33个控制字符、95个可显示字符

* 使用8-bit（一个字节）表示一个字符

*  只能显示26个基本拉丁字母、阿拉伯数字和英式标点符号 




### 8、GBK（汉字内码扩展规范）

>**汉字内码扩展规范**，称**GBK**，全名为**《汉字内码扩展规范(GBK)》1.0版**，由中华人民共和国全国信息技术标准化技术委员会1995年12月1日制订, GBK共收录21886个汉字和图形符号，其中汉字（包括部首和构件）21003个，图形符号883个。GBK的**K**为“扩展”的汉语拼音（kuòzhǎn）第一个声母。英文全称Chinese Internal Code Extension Specification。GBK 只为“技术规范指导性文件”，不属于国家标准

#### 8.1 GBK,GB2312,GB18030

* GB2312 标准共收录 6763 个汉字,采用双个字节展示，并对所收汉字进行了“分区”处理，每区含有 94 个汉字／符号，16-55区采用拼音排序，56-87区采用部首/笔划排序。这种表示方式也称为区位码。GB 2312 的编码范围为 2121H-777EH，与 ASCII 有重叠，通行方法是将 GB 码两个字节的最高位置 1 以示区别。
* GBK 共收入 21886 个汉字和图形符号，采用双字节展示，向下兼容gb2312
* GB18030，兼容GBK，GB2312，是1，2，4变长编码





### 附录

#### 1、ASCll码表

|  二进制   |                  十进制                  | 十六进制 |                     图形                      |
| :-------: | :--------------------------------------: | :------: | :-------------------------------------------: |
| 0010 0000 |  [32](https://zh.wikipedia.org/wiki/32)  |    20    | ([space](https://zh.wikipedia.org/wiki/空格)) |
| 0010 0001 |  [33](https://zh.wikipedia.org/wiki/33)  |    21    |   [!](https://zh.wikipedia.org/wiki/惊叹号)   |
| 0010 0010 |  [34](https://zh.wikipedia.org/wiki/34)  |    22    |   ["](https://zh.wikipedia.org/wiki/双引号)   |
| 0010 0011 |  [35](https://zh.wikipedia.org/wiki/35)  |    23    |    [#](https://zh.wikipedia.org/wiki/井號)    |
| 0010 0100 |  [36](https://zh.wikipedia.org/wiki/36)  |    24    |     [$](https://zh.wikipedia.org/wiki/$)      |
| 0010 0101 |  [37](https://zh.wikipedia.org/wiki/37)  |    25    |   [%](https://zh.wikipedia.org/wiki/百分比)   |
| 0010 0110 |  [38](https://zh.wikipedia.org/wiki/38)  |    26    |    [&](https://zh.wikipedia.org/wiki/%26)     |
| 0010 0111 |  [39](https://zh.wikipedia.org/wiki/39)  |    27    |   ['](https://zh.wikipedia.org/wiki/单引号)   |
| 0010 1000 |  [40](https://zh.wikipedia.org/wiki/40)  |    28    |    [(](https://zh.wikipedia.org/wiki/括號)    |
| 0010 1001 |  [41](https://zh.wikipedia.org/wiki/41)  |    29    |    [)](https://zh.wikipedia.org/wiki/括號)    |
| 0010 1010 |  [42](https://zh.wikipedia.org/wiki/42)  |    2A    |    [*](https://zh.wikipedia.org/wiki/星号)    |
| 0010 1011 |  [43](https://zh.wikipedia.org/wiki/43)  |    2B    |    [+](https://zh.wikipedia.org/wiki/加号)    |
| 0010 1100 |  [44](https://zh.wikipedia.org/wiki/44)  |    2C    |    [,](https://zh.wikipedia.org/wiki/逗号)    |
| 0010 1101 |  [45](https://zh.wikipedia.org/wiki/45)  |    2D    |    [-](https://zh.wikipedia.org/wiki/减号)    |
| 0010 1110 |  [46](https://zh.wikipedia.org/wiki/46)  |    2E    |    [.](https://zh.wikipedia.org/wiki/句点)    |
| 0010 1111 |  [47](https://zh.wikipedia.org/wiki/47)  |    2F    |    [/](https://zh.wikipedia.org/wiki/斜線)    |
| 0011 0000 |  [48](https://zh.wikipedia.org/wiki/48)  |    30    |     [0](https://zh.wikipedia.org/wiki/0)      |
| 0011 0001 |  [49](https://zh.wikipedia.org/wiki/49)  |    31    |     [1](https://zh.wikipedia.org/wiki/1)      |
| 0011 0010 |  [50](https://zh.wikipedia.org/wiki/50)  |    32    |     [2](https://zh.wikipedia.org/wiki/2)      |
| 0011 0011 |  [51](https://zh.wikipedia.org/wiki/51)  |    33    |     [3](https://zh.wikipedia.org/wiki/3)      |
| 0011 0100 |  [52](https://zh.wikipedia.org/wiki/52)  |    34    |     [4](https://zh.wikipedia.org/wiki/4)      |
| 0011 0101 |  [53](https://zh.wikipedia.org/wiki/53)  |    35    |     [5](https://zh.wikipedia.org/wiki/5)      |
| 0011 0110 |  [54](https://zh.wikipedia.org/wiki/54)  |    36    |     [6](https://zh.wikipedia.org/wiki/6)      |
| 0011 0111 |  [55](https://zh.wikipedia.org/wiki/55)  |    37    |     [7](https://zh.wikipedia.org/wiki/7)      |
| 0011 1000 |  [56](https://zh.wikipedia.org/wiki/56)  |    38    |     [8](https://zh.wikipedia.org/wiki/8)      |
| 0011 1001 |  [57](https://zh.wikipedia.org/wiki/57)  |    39    |     [9](https://zh.wikipedia.org/wiki/9)      |
| 0011 1010 |  [58](https://zh.wikipedia.org/wiki/58)  |    3A    |    [:](https://zh.wikipedia.org/wiki/冒号)    |
| 0011 1011 |  [59](https://zh.wikipedia.org/wiki/59)  |    3B    |    [;](https://zh.wikipedia.org/wiki/分号)    |
| 0011 1100 |  [60](https://zh.wikipedia.org/wiki/60)  |    3C    |   [<](https://zh.wikipedia.org/wiki/小於號)   |
| 0011 1101 |  [61](https://zh.wikipedia.org/wiki/61)  |    3D    |    [=](https://zh.wikipedia.org/wiki/等号)    |
| 0011 1110 |  [62](https://zh.wikipedia.org/wiki/62)  |    3E    |   [>](https://zh.wikipedia.org/wiki/大於號)   |
| 0011 1111 |  [63](https://zh.wikipedia.org/wiki/63)  |    3F    |    [?](https://zh.wikipedia.org/wiki/问号)    |
| 0100 0000 |  [64](https://zh.wikipedia.org/wiki/64)  |    40    |     [@](https://zh.wikipedia.org/wiki/@)      |
| 0100 0001 |  [65](https://zh.wikipedia.org/wiki/65)  |    41    |     [A](https://zh.wikipedia.org/wiki/A)      |
| 0100 0010 |  [66](https://zh.wikipedia.org/wiki/66)  |    42    |     [B](https://zh.wikipedia.org/wiki/B)      |
| 0100 0011 |  [67](https://zh.wikipedia.org/wiki/67)  |    43    |     [C](https://zh.wikipedia.org/wiki/C)      |
| 0100 0100 |  [68](https://zh.wikipedia.org/wiki/68)  |    44    |     [D](https://zh.wikipedia.org/wiki/D)      |
| 0100 0101 |  [69](https://zh.wikipedia.org/wiki/69)  |    45    |     [E](https://zh.wikipedia.org/wiki/E)      |
| 0100 0110 |  [70](https://zh.wikipedia.org/wiki/70)  |    46    |     [F](https://zh.wikipedia.org/wiki/F)      |
| 0100 0111 |  [71](https://zh.wikipedia.org/wiki/71)  |    47    |     [G](https://zh.wikipedia.org/wiki/G)      |
| 0100 1000 |  [72](https://zh.wikipedia.org/wiki/72)  |    48    |     [H](https://zh.wikipedia.org/wiki/H)      |
| 0100 1001 |  [73](https://zh.wikipedia.org/wiki/73)  |    49    |     [I](https://zh.wikipedia.org/wiki/I)      |
| 0100 1010 |  [74](https://zh.wikipedia.org/wiki/74)  |    4A    |     [J](https://zh.wikipedia.org/wiki/J)      |
| 0100 1011 |  [75](https://zh.wikipedia.org/wiki/75)  |    4B    |     [K](https://zh.wikipedia.org/wiki/K)      |
| 0100 1100 |  [76](https://zh.wikipedia.org/wiki/76)  |    4C    |     [L](https://zh.wikipedia.org/wiki/L)      |
| 0100 1101 |  [77](https://zh.wikipedia.org/wiki/77)  |    4D    |     [M](https://zh.wikipedia.org/wiki/M)      |
| 0100 1110 |  [78](https://zh.wikipedia.org/wiki/78)  |    4E    |     [N](https://zh.wikipedia.org/wiki/N)      |
| 0100 1111 |  [79](https://zh.wikipedia.org/wiki/79)  |    4F    |     [O](https://zh.wikipedia.org/wiki/O)      |
| 0101 0000 |  [80](https://zh.wikipedia.org/wiki/80)  |    50    |     [P](https://zh.wikipedia.org/wiki/P)      |
| 0101 0001 |  [81](https://zh.wikipedia.org/wiki/81)  |    51    |     [Q](https://zh.wikipedia.org/wiki/Q)      |
| 0101 0010 |  [82](https://zh.wikipedia.org/wiki/82)  |    52    |     [R](https://zh.wikipedia.org/wiki/R)      |
| 0101 0011 |  [83](https://zh.wikipedia.org/wiki/83)  |    53    |     [S](https://zh.wikipedia.org/wiki/S)      |
| 0101 0100 |  [84](https://zh.wikipedia.org/wiki/84)  |    54    |     [T](https://zh.wikipedia.org/wiki/T)      |
| 0101 0101 |  [85](https://zh.wikipedia.org/wiki/85)  |    55    |     [U](https://zh.wikipedia.org/wiki/U)      |
| 0101 0110 |  [86](https://zh.wikipedia.org/wiki/86)  |    56    |     [V](https://zh.wikipedia.org/wiki/V)      |
| 0101 0111 |  [87](https://zh.wikipedia.org/wiki/87)  |    57    |     [W](https://zh.wikipedia.org/wiki/W)      |
| 0101 1000 |  [88](https://zh.wikipedia.org/wiki/88)  |    58    |     [X](https://zh.wikipedia.org/wiki/X)      |
| 0101 1001 |  [89](https://zh.wikipedia.org/wiki/89)  |    59    |     [Y](https://zh.wikipedia.org/wiki/Y)      |
| 0101 1010 |  [90](https://zh.wikipedia.org/wiki/90)  |    5A    |     [Z](https://zh.wikipedia.org/wiki/Z)      |
| 0101 1011 |  [91](https://zh.wikipedia.org/wiki/91)  |    5B    |                       [                       |
| 0101 1100 |  [92](https://zh.wikipedia.org/wiki/92)  |    5C    |                       \                       |
| 0101 1101 |  [93](https://zh.wikipedia.org/wiki/93)  |    5D    |                       ]                       |
| 0101 1110 |  [94](https://zh.wikipedia.org/wiki/94)  |    5E    |   [^](https://zh.wikipedia.org/wiki/脱字符)   |
| 0101 1111 |  [95](https://zh.wikipedia.org/wiki/95)  |    5F    |    [_](https://zh.wikipedia.org/wiki/底線)    |
| 0110 0000 |  [96](https://zh.wikipedia.org/wiki/96)  |    60    |   [`](https://zh.wikipedia.org/wiki/重音符)   |
| 0110 0001 |  [97](https://zh.wikipedia.org/wiki/97)  |    61    |     [a](https://zh.wikipedia.org/wiki/A)      |
| 0110 0010 |  [98](https://zh.wikipedia.org/wiki/98)  |    62    |     [b](https://zh.wikipedia.org/wiki/B)      |
| 0110 0011 |  [99](https://zh.wikipedia.org/wiki/99)  |    63    |     [c](https://zh.wikipedia.org/wiki/C)      |
| 0110 0100 | [100](https://zh.wikipedia.org/wiki/100) |    64    |     [d](https://zh.wikipedia.org/wiki/D)      |
| 0110 0101 | [101](https://zh.wikipedia.org/wiki/101) |    65    |     [e](https://zh.wikipedia.org/wiki/E)      |
| 0110 0110 | [102](https://zh.wikipedia.org/wiki/102) |    66    |     [f](https://zh.wikipedia.org/wiki/F)      |
| 0110 0111 | [103](https://zh.wikipedia.org/wiki/103) |    67    |     [g](https://zh.wikipedia.org/wiki/G)      |
| 0110 1000 | [104](https://zh.wikipedia.org/wiki/104) |    68    |     [h](https://zh.wikipedia.org/wiki/H)      |
| 0110 1001 | [105](https://zh.wikipedia.org/wiki/105) |    69    |     [i](https://zh.wikipedia.org/wiki/I)      |
| 0110 1010 | [106](https://zh.wikipedia.org/wiki/106) |    6A    |     [j](https://zh.wikipedia.org/wiki/J)      |
| 0110 1011 | [107](https://zh.wikipedia.org/wiki/107) |    6B    |     [k](https://zh.wikipedia.org/wiki/K)      |
| 0110 1100 | [108](https://zh.wikipedia.org/wiki/108) |    6C    |     [l](https://zh.wikipedia.org/wiki/L)      |
| 0110 1101 | [109](https://zh.wikipedia.org/wiki/109) |    6D    |     [m](https://zh.wikipedia.org/wiki/M)      |
| 0110 1110 | [110](https://zh.wikipedia.org/wiki/110) |    6E    |     [n](https://zh.wikipedia.org/wiki/N)      |
| 0110 1111 | [111](https://zh.wikipedia.org/wiki/111) |    6F    |     [o](https://zh.wikipedia.org/wiki/O)      |
| 0111 0000 | [112](https://zh.wikipedia.org/wiki/112) |    70    |     [p](https://zh.wikipedia.org/wiki/P)      |
| 0111 0001 | [113](https://zh.wikipedia.org/wiki/113) |    71    |     [q](https://zh.wikipedia.org/wiki/Q)      |
| 0111 0010 | [114](https://zh.wikipedia.org/wiki/114) |    72    |     [r](https://zh.wikipedia.org/wiki/R)      |
| 0111 0011 | [115](https://zh.wikipedia.org/wiki/115) |    73    |     [s](https://zh.wikipedia.org/wiki/S)      |
| 0111 0100 | [116](https://zh.wikipedia.org/wiki/116) |    74    |     [t](https://zh.wikipedia.org/wiki/T)      |
| 0111 0101 | [117](https://zh.wikipedia.org/wiki/117) |    75    |     [u](https://zh.wikipedia.org/wiki/U)      |
| 0111 0110 | [118](https://zh.wikipedia.org/wiki/118) |    76    |     [v](https://zh.wikipedia.org/wiki/V)      |
| 0111 0111 | [119](https://zh.wikipedia.org/wiki/119) |    77    |     [w](https://zh.wikipedia.org/wiki/W)      |
| 0111 1000 | [120](https://zh.wikipedia.org/wiki/120) |    78    |     [x](https://zh.wikipedia.org/wiki/X)      |
| 0111 1001 | [121](https://zh.wikipedia.org/wiki/121) |    79    |     [y](https://zh.wikipedia.org/wiki/Y)      |
| 0111 1010 | [122](https://zh.wikipedia.org/wiki/122) |    7A    |     [z](https://zh.wikipedia.org/wiki/Z)      |
| 0111 1011 | [123](https://zh.wikipedia.org/wiki/123) |    7B    |    [{](https://zh.wikipedia.org/wiki/括號)    |
| 0111 1100 | [124](https://zh.wikipedia.org/wiki/124) |    7C    |   [\|](https://zh.wikipedia.org/wiki/豎線)    |
| 0111 1101 | [125](https://zh.wikipedia.org/wiki/125) |    7D    |    [}](https://zh.wikipedia.org/wiki/括號)    |
| 0111 1110 | [126](https://zh.wikipedia.org/wiki/126) |    7E    |                       ~                       |

#### 2、Unicode字符平面表

|     平面     | 始末字符值          | 中文名称                      | 英文名称                                         |
| :----------: | :------------------ | :---------------------------- | :----------------------------------------------- |
|   0号平面    | U+0000 - U+FFFF     | **基本多文种平面**            | Basic Multilingual Plane，简称**BMP**            |
|   1号平面    | U+10000 - U+1FFFF   | **多文种补充平面**            | Supplementary Multilingual Plane，简称**SMP**    |
|   2号平面    | U+20000 - U+2FFFF   | **表意文字补充平面**          | Supplementary Ideographic Plane，简称**SIP**     |
|   3号平面    | U+30000 - U+3FFFF   | **表意文字第三平面**          | Tertiary Ideographic Plane，简称**TIP**          |
| 4 ~ 13号平面 | U+40000 - U+DFFFF   | （尚未使用）                  |                                                  |
|   14号平面   | U+E0000 - U+EFFFF   | **特别用途补充平面**          | Supplementary Special-purpose Plane，简称**SSP** |
|   15号平面   | U+F0000 - U+FFFFF   | 保留作为**私人使用区（A区）** | Private Use Area-A，简称**PUA-A**                |
|   16号平面   | U+100000 - U+10FFFF | 保留作为**私人使用区（B区）** | Private Use Area-B，简称**PUA-B**                |