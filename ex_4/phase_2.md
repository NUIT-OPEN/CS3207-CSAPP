# Phase 2

首先使用gcc对main.o及phase2.o完成链接，运行lb2，可以发现输出为一串不可视字符。

```log
root@51266e23cd7a:~/linkLab<stu_number># gcc -o lb2 main.o phase2.o -no-pie
root@51266e23cd7a:~/linkLab<stu_number># ./lb2 

root@51266e23cd7a:~/linkLab<stu_number># 
```

对链接后的文件反编译，可得知

```c
int do_phase2()
{
  myFunc();
  return puts((const char *)0x155);
}

__int64 myFunc()
{
  __int64 result; // rax
  signed int i; // [rsp+14h] [rbp-4h]

  for ( i = 0; i <= 255; ++i )
  {
    result = i;
    g_myCharArray[i] += i % 32;
  }
  return result;
}
```

输出内容为g_myCharArray，且每个字符会加上 i % 32 的偏移，所以要输出学号的话，相应字符串需要减去对应的偏移。

```log
Symbol table '.symtab' contains 13 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS phase2.c
     2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1 
     3: 0000000000000000     0 SECTION LOCAL  DEFAULT    3 
     4: 0000000000000000     0 SECTION LOCAL  DEFAULT    5 
     5: 0000000000000000     0 SECTION LOCAL  DEFAULT    7 
     6: 0000000000000000     0 SECTION LOCAL  DEFAULT    8 
     7: 0000000000000000     0 SECTION LOCAL  DEFAULT    6 
     8: 0000000000000020   256 OBJECT  GLOBAL DEFAULT  COM g_myCharArray
     9: 0000000000000000    76 FUNC    GLOBAL DEFAULT    1 myFunc
    10: 000000000000004c    41 FUNC    GLOBAL DEFAULT    1 do_phase2
    11: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND puts
    12: 0000000000000000     8 OBJECT  GLOBAL DEFAULT    3 phase
```

分析符号表可知g_myCharArray为弱符号，那么意味着可以通过定义强符号将变量覆盖。

```log
000000000040056f <do_phase2>:
  40056f:       55                      push   %rbp
  400570:       48 89 e5                mov    %rsp,%rbp
  400573:       48 83 ec 10             sub    $0x10,%rsp
  400577:       bf 60 10 60 00          mov    $0x601060,%edi
  40057c:       e8 a2 ff ff ff          callq  400523 <myFunc>
  400581:       48 c7 45 f8 d5 10 60    movq   $0x6010d5,-0x8(%rbp)
  400588:       00 
  400589:       48 8b 45 f8             mov    -0x8(%rbp),%rax
  40058d:       48 89 c7                mov    %rax,%rdi
  400590:       e8 5b fe ff ff          callq  4003f0 <puts@plt>
  400595:       90                      nop
  400596:       c9                      leaveq
  400597:       c3                      retq
  400598:       0f 1f 84 00 00 00 00    nopl   0x0(%rax,%rax,1)
```

结合汇编语句可以分析出，g_myCharArray在myFunc处理过程中，输入以及输出是不对称的，0x601060为g_myCharArray的起始位置，作为输出传入puts时，位置是0x6010d5；所以要输出学号，则需要构造一个长度为256的字符数组，学号字符串的起始索引为0xd5-0x60=117。

根据前面分析的规则，可以构造出以下字符数组：

```c
char g_myCharArray[256] = {
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    0, 0, 0, 0, 0, 29, 27, 27, 25, 28, 24, 26, 20, 23, 20, 19,
    0
};
```

最后编译执行，完成之。

```log
root@51266e23cd7a:~/linkLab<stu_number># gcc -c phase2_patch.c && gcc -o lb2 main.o phase2.o phase2_patch.o -no-pie
root@51266e23cd7a:~/linkLab<stu_number># ./lb2 
<stu_number>
root@51266e23cd7a:~/linkLab<stu_number># 
```