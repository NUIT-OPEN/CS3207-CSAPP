## Phase 4

```log
(gdb) disass phase_4
Dump of assembler code for function phase_4:
   0x0000000000001440 <+0>: sub    $0x18,%rsp
   0x0000000000001444 <+4>: mov    %fs:0x28,%rax
   0x000000000000144d <+13>:    mov    %rax,0x8(%rsp)
   0x0000000000001452 <+18>:    xor    %eax,%eax
   0x0000000000001454 <+20>:    mov    %rsp,%rcx
   0x0000000000001457 <+23>:    lea    0x4(%rsp),%rdx
   0x000000000000145c <+28>:    lea    0x162c(%rip),%rsi        # 0x2a8f
   0x0000000000001463 <+35>:    callq  0xef0 <__isoc99_sscanf@plt>
   0x0000000000001468 <+40>:    cmp    $0x2,%eax
   0x000000000000146b <+43>:    jne    0x1478 <phase_4+56>
   0x000000000000146d <+45>:    mov    (%rsp),%eax
   0x0000000000001470 <+48>:    sub    $0x2,%eax
   0x0000000000001473 <+51>:    cmp    $0x2,%eax
   0x0000000000001476 <+54>:    jbe    0x147d <phase_4+61>
   0x0000000000001478 <+56>:    callq  0x18a6 <explode_bomb>
   0x000000000000147d <+61>:    mov    (%rsp),%esi
   0x0000000000001480 <+64>:    mov    $0x9,%edi
   0x0000000000001485 <+69>:    callq  0x1407 <func4>
   0x000000000000148a <+74>:    cmp    %eax,0x4(%rsp)
   0x000000000000148e <+78>:    je     0x1495 <phase_4+85>
   0x0000000000001490 <+80>:    callq  0x18a6 <explode_bomb>
   0x0000000000001495 <+85>:    mov    0x8(%rsp),%rax
   0x000000000000149a <+90>:    xor    %fs:0x28,%rax
   0x00000000000014a3 <+99>:    jne    0x14aa <phase_4+106>
   0x00000000000014a5 <+101>:   add    $0x18,%rsp
   0x00000000000014a9 <+105>:   retq   
   0x00000000000014aa <+106>:   callq  0xe50 <__stack_chk_fail@plt>
End of assembler dump.
```

对phase_4进行单步调试，可以得到输入格式。

```log
(gdb) si
0x0000555555401444 in phase_4 ()
0x000055555540144d in phase_4 ()
0x0000555555401452 in phase_4 ()
0x0000555555401454 in phase_4 ()
0x0000555555401457 in phase_4 ()
0x000055555540145c in phase_4 ()
0x0000555555401463 in phase_4 ()
(gdb) x/s $rsi
0x555555402a8f: "%d %d"
```

结合phase_4的代码分析，可知本次需要输入两位数字。 其中第二位数字-2需要<=2，否则炸弹将爆炸。

```log
   0x000000000000146d <+45>:    mov    (%rsp),%eax
   0x0000000000001470 <+48>:    sub    $0x2,%eax
   0x0000000000001473 <+51>:    cmp    $0x2,%eax
   0x0000000000001476 <+54>:    jbe    0x147d <phase_4+61>
   0x0000000000001478 <+56>:    callq  0x18a6 <explode_bomb>
```

然后将立即数9以及第二位数字作为func4的参数进行调用。

```log
   0x000000000000147d <+61>:    mov    (%rsp),%esi
   0x0000000000001480 <+64>:    mov    $0x9,%edi
   0x0000000000001485 <+69>:    callq  0x1407 <func4>
```

接下来则需要对func4进行分析。

```log
Dump of assembler code for function func4:
   0x0000000000001407 <+0>: mov    $0x0,%eax
   0x000000000000140c <+5>: test   %edi,%edi
   0x000000000000140e <+7>: jle    0x1417 <func4+16>
   0x0000000000001410 <+9>: mov    %esi,%eax
   0x0000000000001412 <+11>:    cmp    $0x1,%edi
   0x0000000000001415 <+14>:    jne    0x1419 <func4+18>
   0x0000000000001417 <+16>:    repz retq 
   0x0000000000001419 <+18>:    push   %r12
   0x000000000000141b <+20>:    push   %rbp
   0x000000000000141c <+21>:    push   %rbx
   0x000000000000141d <+22>:    mov    %esi,%r12d
   0x0000000000001420 <+25>:    mov    %edi,%ebx
   0x0000000000001422 <+27>:    lea    -0x1(%rdi),%edi
   0x0000000000001425 <+30>:    callq  0x1407 <func4>
   0x000000000000142a <+35>:    lea    (%rax,%r12,1),%ebp
   0x000000000000142e <+39>:    lea    -0x2(%rbx),%edi
   0x0000000000001431 <+42>:    mov    %r12d,%esi
   0x0000000000001434 <+45>:    callq  0x1407 <func4>
   0x0000000000001439 <+50>:    add    %ebp,%eax
   0x000000000000143b <+52>:    pop    %rbx
   0x000000000000143c <+53>:    pop    %rbp
   0x000000000000143d <+54>:    pop    %r12
   0x000000000000143f <+56>:    retq   
End of assembler dump.
```

结合IDA进行分析可得出下列伪代码

```c
__int64 __fastcall func4(int a1, __int64 a2)
{
  __int64 result; // rax
  int v3; // ebp

  result = 0LL;
  if ( a1 > 0 )
  {
    result = (unsigned int)a2;
    if ( a1 != 1 )
    {
      v3 = func4((unsigned int)(a1 - 1), a2) + a2;
      result = v3 + (unsigned int)func4((unsigned int)(a1 - 2), (unsigned int)a2);
    }
  }
  return result;
}
```

可知func4的计算规则如下所示：

* func4(0, n) = 0
* func4(1, n) = n
* func4(2, n) = func4(1, n) + n + func4(0, n)
* func4(m, n) = func4(m-1, n) + n + func4(m-2, n)

再对func4返回后的phase_4程序进行分析

```log
   0x000000000000148a <+74>:    cmp    %eax,0x4(%rsp)
   0x000000000000148e <+78>:    je     0x1495 <phase_4+85>
   0x0000000000001490 <+80>:    callq  0x18a6 <explode_bomb>
   0x0000000000001495 <+85>:    mov    0x8(%rsp),%rax
   0x000000000000149a <+90>:    xor    %fs:0x28,%rax
   0x00000000000014a3 <+99>:    jne    0x14aa <phase_4+106>
   0x00000000000014a5 <+101>:   add    $0x18,%rsp
   0x00000000000014a9 <+105>:   retq   
```

可知第一个数字需要与func4的返回值相等，才可以正常返回，否则炸弹会爆炸。

那么结果就很显然了，只需要编程，并将结果计算出来即可。

这里可以直接使用之前由IDA反编译得出的代码，只需要定义__int64类型即可。

```c
#include <stdio.h>

typedef long __int64;

__int64 __fastcall func4(int a1, __int64 a2) {
    __int64 result; // rax
    int v3; // ebp

    result = 0LL;
    if (a1 > 0) {
        result = (unsigned int) a2;
        if (a1 != 1) {
            v3 = func4((unsigned int) (a1 - 1), a2) + a2;
            result = v3 + (unsigned int) func4((unsigned int) (a1 - 2), (unsigned int) a2);
        }
    }
    return result;
}

int main() {
    printf("%ld", func4(9, 4));
    return 0;
}
```

由前面的分析可知，第二位数字需要满足 n – 2 <= 2，也就是n <= 4都可。

所以取4作为func4的参数进行计算，可以得出func4(9, 4) = 352

最终答案为：352 4
