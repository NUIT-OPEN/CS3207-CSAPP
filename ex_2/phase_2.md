# Phase 2

```log
(gdb) disass phase_2
Dump of assembler code for function phase_2:
   0x0000000000001224 <+0>: push   %rbp
   0x0000000000001225 <+1>: push   %rbx
   0x0000000000001226 <+2>: sub    $0x28,%rsp
   0x000000000000122a <+6>: mov    %fs:0x28,%rax
   0x0000000000001233 <+15>:    mov    %rax,0x18(%rsp)
   0x0000000000001238 <+20>:    xor    %eax,%eax
   0x000000000000123a <+22>:    mov    %rsp,%rsi
   0x000000000000123d <+25>:    callq  0x18cc <read_six_numbers>
   0x0000000000001242 <+30>:    cmpl   $0x0,(%rsp)
   0x0000000000001246 <+34>:    jne    0x124f <phase_2+43>
   0x0000000000001248 <+36>:    cmpl   $0x1,0x4(%rsp)
   0x000000000000124d <+41>:    je     0x1254 <phase_2+48>
   0x000000000000124f <+43>:    callq  0x18a6 <explode_bomb>
   0x0000000000001254 <+48>:    mov    %rsp,%rbx
   0x0000000000001257 <+51>:    lea    0x10(%rbx),%rbp
   0x000000000000125b <+55>:    jmp    0x1266 <phase_2+66>
   0x000000000000125d <+57>:    add    $0x4,%rbx
   0x0000000000001261 <+61>:    cmp    %rbp,%rbx
   0x0000000000001264 <+64>:    je     0x1277 <phase_2+83>
   0x0000000000001266 <+66>:    mov    0x4(%rbx),%eax
   0x0000000000001269 <+69>:    add    (%rbx),%eax
   0x000000000000126b <+71>:    cmp    %eax,0x8(%rbx)
   0x000000000000126e <+74>:    je     0x125d <phase_2+57>
   0x0000000000001270 <+76>:    callq  0x18a6 <explode_bomb>
   0x0000000000001275 <+81>:    jmp    0x125d <phase_2+57>
   0x0000000000001277 <+83>:    mov    0x18(%rsp),%rax
   0x000000000000127c <+88>:    xor    %fs:0x28,%rax
   0x0000000000001285 <+97>:    jne    0x128e <phase_2+106>
   0x0000000000001287 <+99>:    add    $0x28,%rsp
   0x000000000000128b <+103>:   pop    %rbx
   0x000000000000128c <+104>:   pop    %rbp
   0x000000000000128d <+105>:   retq   
   0x000000000000128e <+106>:   callq  0xe50 <__stack_chk_fail@plt>
```

根据上述代码分析可得，输入的第一位数字为0，第二位数字为1，后面的几位数字等于前两位数字相加，如此类推，答案为：0 1 1 2 3 5

```log
Welcome to my fiendish little bomb. You have 6 phases with
which to blow yourself up. Have a nice day!
You can Russia from land here in Alaska.
Phase 1 defused. How about the next one?
0 1 1 2 3 5
That's number 2.  Keep going!
```
