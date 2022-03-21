# Phase 3

```log
(gdb) disass phase_3
Dump of assembler code for function phase_3:
   0x0000000000001293 <+0>: sub    $0x28,%rsp
   0x0000000000001297 <+4>: mov    %fs:0x28,%rax
   0x00000000000012a0 <+13>:    mov    %rax,0x18(%rsp)
   0x00000000000012a5 <+18>:    xor    %eax,%eax
   0x00000000000012a7 <+20>:    lea    0xf(%rsp),%rcx
   0x00000000000012ac <+25>:    lea    0x10(%rsp),%rdx
   0x00000000000012b1 <+30>:    lea    0x14(%rsp),%r8
   0x00000000000012b6 <+35>:    lea    0x1649(%rip),%rsi        # 0x2906
   0x00000000000012bd <+42>:    callq  0xef0 <__isoc99_sscanf@plt>
   0x00000000000012c2 <+47>:    cmp    $0x2,%eax
   0x00000000000012c5 <+50>:    jle    0x12e6 <phase_3+83>
   0x00000000000012c7 <+52>:    cmpl   $0x7,0x10(%rsp)
   0x00000000000012cc <+57>:    ja     0x13d8 <phase_3+325>
   0x00000000000012d2 <+63>:    mov    0x10(%rsp),%eax
   0x00000000000012d6 <+67>:    lea    0x1643(%rip),%rdx        # 0x2920
   0x00000000000012dd <+74>:    movslq (%rdx,%rax,4),%rax
   0x00000000000012e1 <+78>:    add    %rdx,%rax
   0x00000000000012e4 <+81>:    jmpq   *%rax
   0x00000000000012e6 <+83>:    callq  0x18a6 <explode_bomb>
   0x00000000000012eb <+88>:    jmp    0x12c7 <phase_3+52>
   0x00000000000012ed <+90>:    mov    $0x79,%eax
   0x00000000000012f2 <+95>:    cmpl   $0x4c,0x14(%rsp)
   0x00000000000012f7 <+100>:   je     0x13e2 <phase_3+335>
   0x00000000000012fd <+106>:   callq  0x18a6 <explode_bomb>
   0x0000000000001302 <+111>:   mov    $0x79,%eax
   0x0000000000001307 <+116>:   jmpq   0x13e2 <phase_3+335>
   0x000000000000130c <+121>:   mov    $0x65,%eax
   0x0000000000001311 <+126>:   cmpl   $0x156,0x14(%rsp)
   0x0000000000001319 <+134>:   je     0x13e2 <phase_3+335>
   0x000000000000131f <+140>:   callq  0x18a6 <explode_bomb>
   0x0000000000001324 <+145>:   mov    $0x65,%eax
   0x0000000000001329 <+150>:   jmpq   0x13e2 <phase_3+335>
   0x000000000000132e <+155>:   mov    $0x68,%eax
   0x0000000000001333 <+160>:   cmpl   $0x186,0x14(%rsp)
   0x000000000000133b <+168>:   je     0x13e2 <phase_3+335>
   0x0000000000001341 <+174>:   callq  0x18a6 <explode_bomb>
   0x0000000000001346 <+179>:   mov    $0x68,%eax
   0x000000000000134b <+184>:   jmpq   0x13e2 <phase_3+335>
   0x0000000000001350 <+189>:   mov    $0x70,%eax
   0x0000000000001355 <+194>:   cmpl   $0x39,0x14(%rsp)
   0x000000000000135a <+199>:   je     0x13e2 <phase_3+335>
   0x0000000000001360 <+205>:   callq  0x18a6 <explode_bomb>
   0x0000000000001365 <+210>:   mov    $0x70,%eax
   0x000000000000136a <+215>:   jmp    0x13e2 <phase_3+335>
   0x000000000000136c <+217>:   mov    $0x72,%eax
   0x0000000000001371 <+222>:   cmpl   $0xac,0x14(%rsp)
   0x0000000000001379 <+230>:   je     0x13e2 <phase_3+335>
   0x000000000000137b <+232>:   callq  0x18a6 <explode_bomb>
   0x0000000000001380 <+237>:   mov    $0x72,%eax
   0x0000000000001385 <+242>:   jmp    0x13e2 <phase_3+335>
   0x0000000000001387 <+244>:   mov    $0x78,%eax
   0x000000000000138c <+249>:   cmpl   $0x97,0x14(%rsp)
   0x0000000000001394 <+257>:   je     0x13e2 <phase_3+335>
   0x0000000000001396 <+259>:   callq  0x18a6 <explode_bomb>
   0x000000000000139b <+264>:   mov    $0x78,%eax
   0x00000000000013a0 <+269>:   jmp    0x13e2 <phase_3+335>
   0x00000000000013a2 <+271>:   mov    $0x73,%eax
   0x00000000000013a7 <+276>:   cmpl   $0x1b7,0x14(%rsp)
   0x00000000000013af <+284>:   je     0x13e2 <phase_3+335>
   0x00000000000013b1 <+286>:   callq  0x18a6 <explode_bomb>
   0x00000000000013b6 <+291>:   mov    $0x73,%eax
   0x00000000000013bb <+296>:   jmp    0x13e2 <phase_3+335>
   0x00000000000013bd <+298>:   mov    $0x74,%eax
   0x00000000000013c2 <+303>:   cmpl   $0x192,0x14(%rsp)
   0x00000000000013ca <+311>:   je     0x13e2 <phase_3+335>
   0x00000000000013cc <+313>:   callq  0x18a6 <explode_bomb>
   0x00000000000013d1 <+318>:   mov    $0x74,%eax
   0x00000000000013d6 <+323>:   jmp    0x13e2 <phase_3+335>
   0x00000000000013d8 <+325>:   callq  0x18a6 <explode_bomb>
   0x00000000000013dd <+330>:   mov    $0x65,%eax
   0x00000000000013e2 <+335>:   cmp    %al,0xf(%rsp)
   0x00000000000013e6 <+339>:   je     0x13ed <phase_3+346>
   0x00000000000013e8 <+341>:   callq  0x18a6 <explode_bomb>
   0x00000000000013ed <+346>:   mov    0x18(%rsp),%rax
   0x00000000000013f2 <+351>:   xor    %fs:0x28,%rax
   0x00000000000013fb <+360>:   jne    0x1402 <phase_3+367>
   0x00000000000013fd <+362>:   add    $0x28,%rsp
   0x0000000000001401 <+366>:   retq   
   0x0000000000001402 <+367>:   callq  0xe50 <__stack_chk_fail@plt>
--Type <RET> for more, q to quit, c to continue without paging--
End of assembler dump.
```

通过断点单步调试可知，输入的参数格式如下：

```log
(gdb) si
0x00005555554012ac in phase_3 ()
0x00005555554012b1 in phase_3 ()
0x00005555554012b6 in phase_3 ()
0x00005555554012bd in phase_3 ()
(gdb) x/s $rsi
0x555555402906: "%d %c %d"
```

分别是数字、字符、数字，其对应的保存位置如下所示。

```log
   0x00000000000012a7 <+20>:    lea    0xf(%rsp),%rcx  # char_2
   0x00000000000012ac <+25>:    lea    0x10(%rsp),%rdx  # int_1
   0x00000000000012b1 <+30>:    lea    0x14(%rsp),%r8  # int_3

   0x00000000000012c7 <+52>:    cmpl   $0x7,0x10(%rsp)  # int_1 - 7
   0x00000000000012cc <+57>:    ja     0x13d8 <phase_3+325>  # int_1 > 7, bomb
   0x00000000000012d2 <+63>:    mov    0x10(%rsp),%eax  # eax = int_1
   0x00000000000012d6 <+67>:    lea    0x1643(%rip),%rdx        # 0x2920
   0x00000000000012dd <+74>:    movslq (%rdx,%rax,4),%rax
   0x00000000000012e1 <+78>:    add    %rdx,%rax
   0x00000000000012e4 <+81>:    jmpq   *%rax
```

根据上述代码分析可得该switch语句包含8种情况，其中第一个数字 > 7则跳转到爆炸点，所以第一个数字的范围在0-7。

确认第一个数字后继续进行单步调试，输入0，会跳转到下面的位置

```log
   0x00000000000012ed <+90>:    mov    $0x79,%eax
   0x00000000000012f2 <+95>:    cmpl   $0x4c,0x14(%rsp)
   0x00000000000012f7 <+100>:   je     0x13e2 <phase_3+335>
   0x00000000000012fd <+106>:   callq  0x18a6 <explode_bomb>
```

根据分析，在第1个数字为0的情况下，可得出第3个数字为0x4c（76），跳转到下一处，发现会对第2个字符变量进行对比。

```log
   0x00000000000013e2 <+335>:   cmp    %al,0xf(%rsp)
   0x00000000000013e6 <+339>:   je     0x13ed <phase_3+346>
   0x00000000000013e8 <+341>:   callq  0x18a6 <explode_bomb>
```

单步调试并输出al寄存器可以得出al = 121，由于是字符变量，所以值为ASCII码，结果为y。

最终答案为：0 y 76