### 1.python函数的一些注意事项

python中的类型属于对象，对象有不同的类型而变量没有。

可更改的对象：字符串，元组，数字；

可变类型：列表，字典；

```C
*不可变类型：变量赋值 a=5 后再赋值 a=10，这里实际是新生成一个 int 值对象 10，再让 a 指向它，而 5 被丢弃，不是改变 a 的值，相当于新生成了 a。
*可变类型：变量赋值 la=[1,2,3,4] 后再赋值 la[2]=5 则是将 list la 的第三个元素值更改，本身la没有动，只是其内部的一部分值被修改了。
//Python函数的参数传递：
不可变类型：类似 C++ 的值传递，如整数、字符串、元组。如 fun(a)，传递的只是 a 的值，没有影响 a 对象本身。如果在 fun(a) 内部修改 a 的值，则是新生成一个 a 的对象。
可变类型：类似 C++ 的引用传递，如 列表，字典。如 fun(la)，则是将 la 真正的传过去，修改后 fun 外部的 la 也会受影响
```

而对于不可变类型，可以通过在函数内使用global关键字的方式强制让函数内的局部变量变为全局变量。

### 2.数据容器

#### 2.1列表

```python
变量定义方式：
ListA = ['a1','b2',666,True]
//列表内的元素可以是不同的数据类型
//空列表：ListB = list()
```

```python
列表嵌套：
ListA = [[1,2,3],[4,5,6]]
列表下标：
1.从左到右分别为0,1,2,3……
2.从右到左分别为-1，-2，-3，……
```

```python
列表的功能：
1.有关方法
函数：num =add(1,2)
方法:student = Student()  num = student.add(1,2)
2.具体方法
查找元素下标：
ListA = [1,2,'abc',True]
index = ListA.index(2)
print(f"2在列表中的下标索引是：{index}")
插入元素：
ListA.insert(position,payload)
添加单个元素到列表尾部：
ListA.append(payload)
添加新的列表到原列表末尾：
ListA.extend([1,2,3]/ListB)
将列表中某元素移除并赋值给另一个变量：
Element = ListA.pop(2)
//此时原ListA中下标为2的元素将从列表中被移除
移除某元素：
ListA.remove(True)
清除列表:
ListA.clear()
计算某变量出现的次数：
ListA.count(payload)
输出元素数量：
len(ListA)
```

```python
列表反转：
1.使用reverse
a.sort(reverse=True)#reverse为关键字参数，先排序再反转
a.reverse() #reverse()为方法，把a反转
//
newlist = list(reversed(oldlist))#使用反转迭代器并进行list转换
newlist = sorted(oldlist,reverse = True)#sort在原来的列表上排序，sorted则是对旧列表排序后输出到新列表
//切片
listNode = [1,2,3,4,5]
li = listNode[::-1]
print(li)
#[a:b:c]a是左边界，b是右边界,c是步长（负数表示从右向左）
//递归
listNode = [1,2,3,4,5]
new=[]
head=listNode
while head!=None:
 new.append(head.val)
 head=head.next
new.reverse()
print(new)
def getLists(self,listNode):

 if listNode is None:
   return []
 l = self.getLists(listNode.next)
   return l + [listNode.val]
```

```python
列表元素拼接字符串：
letters_str=''.join(letters)#''之间为连接字符，join参数为列表名称
#参考文档：https://stackabuse.com/python-convert-list-to-string/
```

```python
字符的大小写转换：
captitalize()#首字母大小转换
casefold()#所有字母强制小写
title()#返回标题化的字符串。所有的单词都是以大写开始，其余字母均小写；所有用空格、标点符号隔开的相连字母，可视为一个单词。
swapcase()#大小写字母翻转
upper()#强制大写
lower()#强制小写
```

#### 2.2元组

元组中的元素不可以被修改（不同于列表）

```python
//元组定义使用小括号，用逗号隔开各个数据。各数据的数据类型可以不同。
空元组定义：
TupleA = ()
TupleA = tuple()
//元组的嵌套和列表类似。
```

```
元组方法
1.查找元素：
tupleA.index(payload)
2.计算某一元素出现次数（同列表）
3.计算数组长度（同列表）
```

#### 2.3字符串

```python
字符串的下标索引方式和列表相同。
字符串方法：
1.strA.replace(A,B)
把A替换为B，原字符串不变，生成一个新的字符串
2.strA.split("X")
按照输入字符作为分隔点把字符串分隔，并输出一个列表
3.strA.strip("X")
去除前后的指定字符串，并把结果输入到新字符串中
4.strA.count("x")/len(strA)
```

```python
字符串的遍历：
while index <len(my_str):
    print(my_str[index])
    index++
#
for i in my_str:
    print(i)
```

#### 2.4 序列

内容连续有序可用下标索引的数据容器（包括列表 元组 字符串等）

```
切片：
序列[起始：结束：步长]
```

#### 2.5 集合

特点：无序，不重复；（不可以使用下标索引）

遍历只能使用for不能使用while

```python
空集合：set()
添加元素:
my_set.add("")
去除元素：
my_set.remove("")
取出元素:
my_set.pop("X")
清空集合：
my_set.clear("")
取差集：
set1.difference(set2)
#得到一个新集合
消除差集：
set1.difference_update(set2)
#在set1中删除和set2一样的元素
合并元素：
set1.union(set2)
```

#### 2.6 字典

字典是键值对作为元素,使用{}

```python
#空字典
my_dict = {}
my_dict = dict()
#字典嵌套
（Key不可以是字典但Value可以）
#添加/更新元素（取决于Key是否存在）
my_dict["Key"] = Value
#删除元素：把某个值取出来赋给新的变量，字典中删除原键值对
Value1 = my_dict.pop("Key")
#获取key
keys = my_dict.keys()
#遍历value
for key in keys:
    printf(f"{my_dict(key)}")
```

#### 2.7 数据容器的通用操作

遍历：

都可以使用for循环；列表元组字符串支持while，集合字典不支持（无法下标索引）

其他操作：

len()计算长度/max()最大元素/min()最小元素 

通用转换：

list()/str()/tuple()/set()

#字典转列表时会把Value丢弃，将Key作为元素存入列表

通用排序：

sorted(docker_name,[reverse=True])#倒序排序

### 3.函数

#### 3.1 函数参数

位置参数：调用函数时根据函数定义的参数位置来传参

```python
def fx(a,b,c):
    return ……
fx(100,200,300)
#传递的参数和定义的参数的顺序与个数必须一致
```

关键字参数：通过“键=值”的形式调用参数。

位置参数必须在关键字参数的前面，但关键字参数是无序的。

```python
def fx(a,b,c):
    return ……
fx(100,b=200,c=300)
```

缺省参数：用于定义函数，为参数提供默认值，调用函数时可以不传该类参数的默认值（位置参数必须出现在默认参数之前，包括函数定义和调用）

```python
def fx(a,b,c='x'):
    return ……
fx(100,200)
fx(400,500,'y')
#缺省参数在传值中会被覆盖，若没有新值则使用默认值
```

不定长参数（一个*）：

传进的参数不限制数量与数据类型，且会被合并为一个元组（即位置传递）

关键字传递（两个*）：传参方式为键值对，数量不受限，参数会组成为字典。

```python
def fx(*args):
def fx(**args):
```

函数作为参数的传递：

```python
def test_func(compute):
    result = compute(1,2)
    return result
def compute(x,y):
    return x+y
test_func(compute)
#这是一种计算逻辑方式的传递，而不是数据的传递
```

#### 3.2 匿名函数

匿名函数只能使用一次。

```python
#lambda 参数:函数体(一行代码)
test_func(lambda x,y:x+y)
```

### 4.文件操作

#### 4.1基本操作

```python
#打开文件
open(name,mode,encoding)
f = open('temp.txt','r',encoding="UTF-8")
#读取文件内容
f.read("x")#X是读取的字节数
f.readlines()#读取文件的全部行，封装到列表中
#关闭文件
f.close()
time.sleep('x')#x是睡眠等待时间
#打开文件
with open(……) as f:
    for line in f:
        print(f"{line}")
```

```python
f=open("filename","w")#写文件权限
f=open("filename","a")#追加内容权限
f.write("payload")#写入内容
f.flush()#文件内容刷新
#直接调用write后添加的内容会进入内存的buffer area，只有flush之后才会真正写入文件
#f.close()包含了flsuh的刷新功能
#f.write()当文件不存在的时候会创建新文件
```

```python
#单个异常的捕获
try:
    "可能产生异常的代码"
except:
    "如果出现异常执行的代码"
```

### 5.对象

#### 5.1类与对象

```python
#设计类
class Student:
    name=None
#创建类中的对象
stu1 = Student()
stu2 = Student()
#对象属性赋值
stu1.name = "issac"
stu2.name = "angel"
```

在类的定义过程中，可以包含类的属性：定义在类中的变量（即成员变量）和类的行为：定义在类中的函数（即成员函数），被称为方法。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151205247.png)

####  5.2 构造方法

```python
__init__()#构造方法
#在创建类对象的时候会自动执行，将传入参数自动传递给__init__方法
class Student:
    name = None
    age = None
    tel = None
    #这一部分的初始化可以直接省略
def __init__(self,name,age,tel):
    self.name = name
    self.age = age
    self.tel =tel
stu = Student("issac",18,12345678)
```

#### 5.3魔术方法

##### 5.3.1  str方法

```python
class Student:
    def __init__(self,name,age):
        self.name = name
        self.age = age
def __str__(self):
    return f"Student类对象,name:{self.name},age:{self.age}"
#将类的实例变为字符串
```

##### 5.3.2 lt/le方法

```python
def __lt__(self,other):
    return self.age<other.age
#比较大小（小于/大于）
__le__(self)#比较大小（大于等于，小于等于）
```

##### 5.3.3 eq方法

```python
#比较运算符（相等）
def __eq__(self,other):
    return self.age==other.age

```

#### 5.4 封装

私有成员分为方法和变量，定义方式是两个下划线开头。

```python
__variable = None #私有成员变量
def __Fx(self):   #私有成员方法 
    ……
```

私有成员方法不能直接被类对象使用；私有变量无法赋值也无法获取值。但类中的其他成员可以访问私有成员。

#### 5.5 继承

##### 5.5.1 单继承

```python
class SonClass(FatherClass):
    pass
```

##### 5.5.2 多继承

```python
class SonClass(FatherClass1,FatherClass2,FatherClass3,……):
    pass
```

##### 5.5.3 复写

```python
#在子类中可以将父类内部的成员复写
class father:
    index = 1
class son(father):
    index =2
print(son.index)
```

##### 5.5.4 父类成员调用

```python
#父类名调用和super()调用
class FatherClass:
    variableA=1
    def Fx(self):
        print(f"The index is {FatherClass.variableA}")
class SonClass(FatherClass):
    variableB=2
    def Fx(self):
        print(f"The last index is {FatherClass.variableA}") #父类名调用成员变量
        FatherClass.Fx(self)#父类名调用成员方法
        print(f"The last index is {super().variableA}") #super()调用成员变量
        super().Fx()#super()父类名调用成员方法
        print(SonClass.variableB)
temp=SonClass()
temp.Fx()
        

```

#### 5.6 类型注解

```python
#变量:类型
var:float = 3.14
###
class Student:
    pass
stu:Student = Student()
###
my_list:list[int]=[1,2,3]
my_tuple:tuple[str,int,bool]=("str",6,False)#第1，2，……，n个元素的类型
my_set:set[int]={1,2,3,4}
my_dict:dict[str,int]={"str": 0}#键值对中键与值的类型
#在注释中进行类型注解
var_1 = random.randint(1,10)#type:int
#形参的类型注解
def func(x:int,y:str):
    pass
#返回值的类型注解
def func(……) ->int:
    pass
#Union联合注释
from typing import union
my_list:list[Union[str,int]]= [1,2,3,"str"]
my_dict[str,union[str,int]] = {"name":"issac","age":18}
def func(data:union[int,str])->union[int,str]:
    pass

```

#### 5.7 多态

完成某个行为时，使用不同的对象会得到不同的状态

```python
class Animal:
    def speak(self):
        pass
class Dog(Animal):
    def speak(self):
        print("doge")
class Cat(Animal):
    def speak(self):
        print("meow")
def make_noise(animal:Animal):
    animal.speak()
dog=Dog()
cat=Cat()

make_noise(dog)
nake_noise(cat)
```

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151205248.png)

父类决定了方法，但由子类自行决定如何实现这些方法，这被称作抽象类（接口）

抽象类：含有抽象方法的类

抽象方法：方法体内的实现为空（pass）
