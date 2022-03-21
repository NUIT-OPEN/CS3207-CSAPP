# Phase 1

```log
(gdb) disass phase_1
Dump of assembler code for function phase_1:
   0x0000000000001204 <+0>: sub    $0x8,%rsp
   0x0000000000001208 <+4>: lea    0x16a1(%rip),%rsi        # 0x28b0
   0x000000000000120f <+11>:    callq  0x179a <strings_not_equal>
   0x0000000000001214 <+16>:    test   %eax,%eax
   0x0000000000001216 <+18>:    jne    0x121d <phase_1+25>
   0x0000000000001218 <+20>:    add    $0x8,%rsp
   0x000000000000121c <+24>:    retq   
   0x000000000000121d <+25>:    callq  0x18a6 <explode_bomb>
   0x0000000000001222 <+30>:    jmp    0x1218 <phase_1+20>
End of assembler dump.
```

根据上述代码分析可见，大致逻辑为对比输入字符串为特定内容，如不相同则跳转至+25引爆炸弹，否则正常返回。

那么需要做的便是通过断点，然后获取该字符串。

```log
(gdb) start
Temporary breakpoint 1 at 0x10aa: file bomb.c, line 37.
Starting program: /home/hsojo/Downloads/bomb<stu_number>/bomb

Temporary breakpoint 1, main (argc=1, argv=0x7fffffffe358) at bomb.c:37
(gdb) b phase_1
Breakpoint 2 at 0x555555401204
(gdb) run
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /home/hsojo/Downloads/bomb<stu_number>/bomb
Welcome to my fiendish little bomb. You have 6 phases with
which to blow yourself up. Have a nice day!
(gdb) si
0x0000555555401208 in phase_1 ()
(gdb) si
0x000055555540120f in phase_1 ()
(gdb) x/s $rsi
0x5555554028b0: "You can Russia from land here in Alaska."
```

操作如上所示，答案为：You can Russia from land here in Alaska.

如果只是为了通过phase_1，还可以通过set $rdi = $rsi来跳过。
