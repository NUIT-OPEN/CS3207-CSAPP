# Ex3

* AttackLab，网上也有教程，看不懂就bilibili吧

> 注意：每个人的实验数值不一样，此处仅提供过程参考

## 起步

由题目意思可知，ctarget启动时会调用getbuf函数，该函数存在缓冲区溢出漏洞，通过输入不同的内容可以实施任意代码执行的攻击。

首先需要对getbuf函数进行分析，得知缓冲区大小。
```log
0000000000401823 <getbuf>:
  401823:       48 83 ec 38             sub    $0x38,%rsp
  401827:       48 89 e7                mov    %rsp,%rdi
  40182a:       e8 94 02 00 00          callq  401ac3 <Gets>
  40182f:       b8 01 00 00 00          mov    $0x1,%eax
  401834:       48 83 c4 38             add    $0x38,%rsp
  401838:       c3                      retq
```
由objdump的结果可知，在使用gets获取输入前，会在rsp初始化0x38长度（56）字节的空间；

* 返回地址（8 Bytes）
  * %rsp+56
* 临时变量（38 Bytes）
  * %rsp
