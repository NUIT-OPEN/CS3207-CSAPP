# Ex1

* 首先将datalab-handout.tar解压到实验环境。
* 然后使用make构建dlc以及btest测试工具。
* 构建完测试工具以后，便可以开始实验。

> 答案很乱 不记得从哪里抄来的了 有一部分是自己写的

## 1. 判断数值是否为零

```c
/* We do not support C11 <threads.h>.  */
/*
 * isZero - returns 1 if x == 0, and 0 otherwise 
 *   Examples: isZero(5) = 0, isZero(0) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 2
 *   Rating: 1
 */
int isZero(int x) {
  return !x;
}
```

可使用的操作符有所限制，且最多使用2个操作符。 做法非常简单，只需要对数值进行逻辑取反即可。 非0数会变成0，而0会变成1。

## 2. 求负数

```c
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  return ~x + 1;
}
```

对于有符号正数取负数，对其进行位取反+1即可。

## 3. 构造魔法数字0xffca3fff

```c
/* 
 * specialBits - return bit pattern 0xffca3fff
 * Legal ops: ! ~ & ^ | + << >>
 * Max ops: 3
 * Rating: 1
 */
int specialBits(void) {
    // 0xffca3fff = 1111 1111 1100 1010 0011 1111 1111 1111
    // 0x28 = 0010 1000
    // 0xd7 = 1101 0111
    // 0xd7 << 14
    return ~(0xd7 << 14);
}
```

* 首先需要对这串魔法数字进行分析，转为二进制可以得到 1111 1111 1100 1010 0011 1111 1111 1111 的结果，找规律可以发现： 从零开始的部分有着一段0010
  1000的数字很显眼，其它数字都为1，意味着可以通过对位取反得到0010 1000的结果，那么构造0010 1000，只需要构造其取反的结果（1101 0111），并左移14位再进行位取反即可。

## 4. 构造高n位数字

```c
/* 
 * upperBits - pads n upper bits with 1's
 *  You may assume 0 <= n <= 32
 *  Example: upperBits(4) = 0xF0000000
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 10
 *  Rating: 1
 */
int upperBits(int n) {
  return ((!!n)<<31)>>(n+(~0));
}
```

本题需要利用有符号数算数右移的特性构造高n位数字。
对n进行两次逻辑取反，可以得到基准数0或者1，然后将基准数左移31位（到最高位），然后利用算数右移的特性构造高n位数字，只需要右移n-1次；由于操作符并不允许使用“-”，所以构造n-1需要使用n+(-1)
的方式实现，在有符号数中，-1的表示为全1，那么对0进行位取反即可。

## 5. 逐位匹配

```c
/* 
 * bitMatch - Create mask indicating which bits in x match those in y
 *            using only ~ and & 
 *   Example: bitMatch(0x7, 0xE) = 0x6
 *   Legal ops: ~ & |
 *   Max ops: 14
 *   Rating: 1
 */
int bitMatch(int x, int y) {
  // 0x7 = 0111 = 0000...0111
  // 0xe = 1110 = 1111...1110
  // 0x6 = 0110 = 0000...0110
  //
  // x & y   = 0000...0111 & 1111...1110 = 0000...0110
  // ~x & ~y = 1111...1000 & 0000...0001 = 0000...0000
  // (x & y) | (~x & ~y) = 0000...0110 | 0000...0000 = 0000...0110 = 6
  return (x & y)|(~x & ~y);
}
```

本题需要计算x与y的按位匹配部分的掩码。 此处需要注意的是，按题目给出的0xE为4位字长的值，手工计算时需要对其进行符号位扩展到int的32位，否则会出现计算出错的情况。 具体计算过程可见代码注释部分。

## 6. 位或运算

```c
/* 
 * bitOr - x|y using only ~ and & 
 *   Example: bitOr(6, 5) = 7
 *   Legal ops: ~ &
 *   Max ops: 8
 *   Rating: 1
 */
int bitOr(int x, int y) {
  // x(6) = 0110
  // y(5) = 0101
  // 7 = 0111
  //
  // ~x(1001) & ~y(1010) = 1000 ~= 0111
  return ~(~x & ~y); // 0110 0101, 1001 1010, 0111
}
```

本题需要使用位取反、位与实现位或运算。 对“x取反”及“y取反”的结果进行位与运算的结果再取反可以得到位或运算的结果。

## 7. 计算绝对值

```c
/* 
 * absVal - absolute value of x
 *   Example: absVal(-1) = 1.
 *   You may assume -TMax <= x <= TMax
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 10
 *   Rating: 4
 */
int absVal(int x) {
  return (x^(x>>31))+((x>>31)&1);
}
```

## 8. 实现逻辑取反

```c
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 * Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 * Legal ops: ~ & ^ | + << >>
 * Max ops: 12
 * Rating: 4 
 */
int logicalNeg(int x) {
  // -1 = 1111...1111 >>31= 1111...1111 = -1
  //   ~= 0000...0000+1 = 0000...0001 >>31= 0000...0000 = 0
  //  1 = 0000...0001 >>31= 0000...0000 = 0
  //   ~= 1111...1110+1 = 1111...1111 >>31 = 1111...1111 = -1
  //  0 = 0000...0000 >>31= 0000...0000 = 0
  //   ~= 1111...1111+1 = 0000...0000 >>31 = 0000...0000 = 0
  return (x >> 31 | (~x+1)>>31) + 1;
}
```

逻辑取反，非0数返回0，0则返回1。 以代码注释中的-1、1、0为例，非0数或其取反得到的值+1，右移31位，必有一个数是非0值（-1），但只有0，及其取反值+1都为0；那么对该值+1，便可以得到非0返回0，0则返回1的结果。

## 9. 判断数值是否包含奇数位0

```c
/*
 * bitParity - returns 1 if x contains an odd number of 0's
 * Examples: bitParity(5) = 0, bitParity(7) = 1
 * Legal ops: ! ~ & ^ | + << >>
 * Max ops: 20
 * Rating: 4
 */
int bitParity(int x) {
  // 5 = 0101
  // 7 = 0111
  int w16 = (x >> 16)^x;   // w16 = 0x00000000^0x00000007=0x00000007  
  int w8 = (w16 >> 8)^w16; // w8 = 0x00000000^0x00000007=0x00000007
  int w4 = (w8 >> 4)^w8;   // w4 = 0x00000000^0x00000007=0x00000007
  int w2 = (w4 >> 2)^w4;   // w2 = 0x00000001^0x00000007=0x00000006
  int w1 = (w2 >> 1)^w2;   // w1 = 0x00000003^0x00000006=0x00000005
  return w1 & 1; 
}
```

最容易想到的就是线性的方法了，检查每一个位，为1则累加1。最后检查累加值最低位（奇数的判断）是1，则输出1，否则返回0。但可以看到这样的话需要检查的32步，再加上累加操作的32步，总共64步。大于20步。

经过上述思考，可以发现用线性的方法极有可能是要超出20步的。那么最容易降低复杂度的就是O(logN)
的算法。鉴于此，我想到可以用二分的方法试一下。那么，怎么用二分呢？再仔细一看，由于检查的是含有1是否为奇数，并没有要求计算总的数量，所以是否可以用一个操作符来判断奇偶。

怎样的操作数可以判断奇偶，于是想到xor。由于xor在两数都不相同时为1，所以多个位作xor操作，得出为1则说明有奇数个1。并且利用二分的思想分解成两个16位->两个8位……->两个1位，如此不断xor。就得出了最后答案。

## 10. 字节交换

```c
/* 
 * byteSwap - swaps the nth byte and the mth byte
 * Examples: byteSwap(0x12345678, 1, 3) = 0x56341278
 *            byteSwap(0xDEADBEEF, 0, 2) = 0xDEEFBEAD
 * You may assume that 0 <= n <= 3, 0 <= m <= 3
 * Legal ops: ! ~ & ^ | + << >>
 * Max ops: 25
 * Rating: 2
 */
int byteSwap(int x, int n, int m) {
  int n8 = n << 3; // 字节为单位，取得起始位
  int m8 = m << 3; // 字节为单位，取得起始位
  int a = ((x >> n8) & 0xFF) << m8;  // & 0xFF 掩码是 取出内容
  int b = ((x >> m8) & 0xFF) << n8;
  int c = (x & ~(0xFF << m8) & ~(0xFF << n8)); // 将原数据x，待处理位置挖空
  return (a | b | c);
}
```

Examples: byteSwap(0x12345678, 1, 3) = 0x56341278 byteSwap(0xDEADBEEF, 0, 2) = 0xDEEFBEAD 显然 0x aa bb cc dd
，其中dd第0字节，cc第1字节，bb第2字节，aa第3字节 You may assume that 0 <= n <= 3, 0 <= m <= 3 Legal ops: ! ~ & ^ | + << >>
Max ops: 25 将需要交换的2个byte分别扣（与运算）出来，再错位放入（或运算)

## 11. 取n位字节

```c
/* 
 * getByte - Extract byte n from word x
 * Bytes numbered from 0 (least significant) to 3 (most significant)
 * Examples: getByte(0x12345678,1) = 0x56
 * Legal ops: ! ~ & ^ | + << >>
 * Max ops: 6
 * Rating: 2
 */
int getByte(int x, int n) {
  int shift = n << 3;
  int xs = x >> shift;
  /* Mask byte */
  return xs & 0xFF;
}
```

Bytes numbered from 0 (least significant) to 3 (most significant)
Examples: getByte(0x12345678,1) = 0x56 Legal ops: ! ~ & ^ | + << >>
Max ops: 6 参考10，很简单，先把起始位置弄出来，然后用掩码0xff 与，把数据抠出来

## 12. 对比大小

```c
/* 
 * isGreater - if x > y  then return 1, else return 0 
 * Example: isGreater(4,5) = 0, isGreater(5,4) = 1
 * Legal ops: ! ~ & ^ | + << >>
 * Max ops: 24
 * Rating: 3
 */
int isGreater(int x, int y) {
    int x_neg = x>>31;
    int y_neg = y>>31;
    return !((x_neg & !y_neg) | ((!(x_neg ^ y_neg)) & (~y + x)>>31)); 
}
```

该题比较两个数字的大小，首先我们明确正数是比负数大的，而负数比正数小 利用该性质我们可以先由该性质判断，其次利用两数相减为正数时返回1性质写出表达式 我们需要注意的是，该地方是不需要考虑溢出的，因为溢出是异号相减的行为

## 13. 判断负数

```c
/* 
 * isNegative - return 1 if x < 0, return 0 otherwise 
 * Example: isNegative(-1) = 1.
 * Legal ops: ! ~ & ^ | + << >>
 * Max ops: 6
 * Rating: 2
 */
int isNegative(int x) {
  return (x>>31) & 0x01;
}
```

只需要对x右移31位，取得符号位，并对1进行与运算，即可得到结果。

## 14. 判断是否为2的n次方

```c
/*
 * isPower2 - returns 1 if x is a power of 2, and 0 otherwise
 * Examples: isPower2(5) = 0, isPower2(8) = 1, isPower2(0) = 0
 * Note that no negative number is a power of 2.
 * Legal ops: ! ~ & ^ | + << >>
 * Max ops: 20
 * Rating: 4
 */
int isPower2(int x) {
  /* Solve using trick: x & (x-1) == 0 only when x==0 or has single 1 bit */
  int xm1 = x + ~0;   //  x -1
  int possible = !(x&xm1);  // 2的幂次，则比特串只有1个1，则x&(x-1) = 0 
  int result = possible & !!x & !(x & (1<<31));  //  !(x & (1<<31)) 排除负数   !!x 排除0 
  return result;
}
```

使用x+mask 为 x-1, x&(x-1) 相当于得到x去掉了最低的为1的那位后的数， 2的次幂数对应2进制应该只有1位1 同时要求x不能为0和负数 例如 4 的比特串 100只有一个1，且按照题意，需要排除0，负数(-1)

## 15. 判断加法是否溢出

```c
/* 
 * addOK - Determine if can compute x+y without overflow
 * Example: addOK(0x80000000,0x80000000) = 0,
 *            addOK(0x80000000,0x70000000) = 1, 
 * Legal ops: ! ~ & ^ | + << >>
 * Max ops: 20
 * Rating: 3
 */
int addOK(int x, int y) {
  int sum = x+y;
  int x_neg = x>>31;
  int y_neg = y>>31;
  int s_neg = sum>>31;
  /* Overflow when x and y have same sign, but s is different */
  return !(~(x_neg ^ y_neg) & (x_neg ^ s_neg));
}
```

相加溢出只能发生在符号相同的两个数之间。 当 x > 0 且 y > 0 但 x+y < 0时，发生正溢出 当x<0 且 y < 0 但 x+y >= 0 时，发生负溢出
而对于异号情况，永远不会溢出，对，你没有看错，永远。不信你试试，反推我。 那么，异号情况 x^y = 1，要保证结果不溢出，即输出1.则 (x^y)。 现在还有 同号情况，所以表达式应该有 ！(~(x^y) & ? )，这个 ?
就表示同号情况。 ? 当左边同号，即~(x^y)为1，而 x+y和x异号，此时 ?是1，即同号相加结果异号，产生溢出，导致输出0。 相加前符号位相同，相加后的结果符号位与x和y不同，anemic代表加法溢出

## 16. 判断减法是否溢出

```c
/* 
 * subtractionOK - Determine if can compute x-y without overflow
 * Example: subtractionOK(0x80000000,0x80000000) = 1,
 *            subtractionOK(0x80000000,0x70000000) = 0, 
 * Legal ops: ! ~ & ^ | + << >>
 * Max ops: 20
 * Rating: 3
 */
int subtractionOK(int x, int y) {
    int x_neg = (x >> 31) & 1;
    int y_neg = (y >> 31) & 1;
    int sum = x + (~y + 1);  // x - y
    int sum_neg = (sum >> 31) & 1;
    return !(x_neg^y_neg) | !(x_neg^sum_neg);
}
```

只要x与y,x-y的符号位不同时就会溢出。(-++负减正得正，+--),即 subtractionOK(-1,-1) = 1,因为 -1 –(-1) = 0，变化符号了 根据上题思路
该题判断减法是否溢出，减法是否溢出，第一个取决的便是两个数字的符号，我们可以发现 同号相减是不会溢出的，此时输出1，而异号相减溢出时结果的符号可能会与被减数的符号相反，造成溢出，输出0。 x – y = x + (~y + 1)

## 17. 奇数位置1

```c
/* 
 * oddBits - return word with all odd-numbered bits set to 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 8
 *   Rating: 2
 */
int oddBits(void) {
  int x=0xaa;
  x|=x<<8;
  x|=x<<16;
  return x;
}
```

## 18. 替换第n位字节

```c
/* 
 * replaceByte(x,n,c) - Replace byte n in x with c
 *   Bytes numbered from 0 (LSB) to 3 (MSB)
 *   Examples: replaceByte(0x12345678,1,0xab) = 0x1234ab78
 *   You can assume 0 <= n <= 3 and 0 <= c <= 255
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 10
 *   Rating: 3
 */
int replaceByte(int x, int n, int c) {
  int mask_=0xff<<(n<<3);
  c<<=(n<<3);
  return (x&(~mask_))|c;
}
```

## 19. rotateLeft

```c
/* 
 * rotateLeft - Rotate x to the left by n
 *   Can assume that 0 <= n <= 31
 *   Examples: rotateLeft(0x87654321,4) = 0x76543218
 *   Legal ops: ~ & ^ | + << >> !
 *   Max ops: 25
 *   Rating: 3 
 */
int rotateLeft(int x, int n) {
  int mask=(~0)+(1<<n);
  int r=(x>>(32+(~n)+1))&mask;
  return ((x<<n)&(~mask))|r;
}
```

## 20. 求浮点数绝对值

```c
/* 
 * floatAbsVal - Return bit-level equivalent of absolute value of f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representations of
 *   single-precision floating point values.
 *   When argument is NaN, return argument..
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 10
 *   Rating: 2
 */
unsigned floatAbsVal(unsigned uf) {
  if((uf & 0x7f800000) >> 23 == 255 && uf<<9)
  return uf;
  return uf & 0x7fffffff;
}
```

## 21. 求浮点数是否相等

```c
/* 
 * floatIsEqual - Compute f == g for floating point arguments f and g.
 *   Both the arguments are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representations of
 *   single-precision floating point values.
 *   If either argument is NaN, return 0.
 *   +0 and -0 are considered equal.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 25
 *   Rating: 2
 */
int floatIsEqual(unsigned uf, unsigned ug) {
    if(!(uf&0x7fffffff)&& !(ug&0x7fffffff)) return 1;
    if((uf&0x7fffffff)>0x7f800000) return 0;
    if((ug&0x7fffffff)>0x7f800000) return 0;
    return uf==ug;
}
```