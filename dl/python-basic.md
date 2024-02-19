---
title: 'Python 基础语法.'
date: 2022-09-15 10:15:16
tags: [python]
published: true
hideInList: true
feature: 
isTop: false
---

<meta name="referrer" content="no-referrer"/>



# 1. 概述
本文目标：尝试找到所有编程语言的共性，以此来快速入门各类编程语言的基础语法。本文将以C++,Java,Python,Golang四种编程语言为样例，进行讲解。  

编程语言的定义：编程语言（programming language）可以简单的理解为一种计算机和人都能识别的语言。 一种计算机语言让程序员能够准确地**定义计算机所需要使用的数据**，并精确地定义在**不同情况下**所应当采取的**行动**。

因此，各个语言的本质是围绕**数据**展开的。如何`定义数据`、`存储数据`、`操控数据`就是每个编程语言的本质目标。当然其中最复杂，内容最多的部分就是如何**操控数据**，它又包括`操控内存数据`与`操纵外存数据` 。对于**操控内存数据**,又包括`操控基本类型数据`，`操控数据存储的结构`，`操控数据的逻辑实现`，`操纵方法的封装`（自定义封装与引用封装）。对于**操控外存数据**，又分`读外存文件到内存`，`以及写内存数据到外存`，而外存文件又分文本文件和二进制文件，但这里不单独再分类。

## main函数与hello world
这里首先给出不同语言的hello world格式  

**python**
python 是解释型语言，可以没有main函数就能直接输出。但是也可以有main函数。

 1. python不用加分号；但是需要用缩进<Tab按键>来规定作用域

```python 
# 不写main直接输出
print("hello world")
# main函数里输出
if __name__ =="__main__":
	print("hello world")
```



# 2. 定义数据
数据包括基本数据类型与定义类的对象。其实所有数据的定义，都是定义类的对象，但是其他类的对象数据都是基本数据类型的组合，所以单独拿出来说了。    

  所有类的定义的语法基本都如下图所示：

> 类的名字 类的对象的名字 = 类的一个对象/类的构造方法
> eg. 
> Int num = 1  或者Int num = Int(1) 

其实更加常见的一个定义数据分类是定义常量和定义变量进行分类，后文也会提到。

## 2.1 定义基本数据类型
基本数据类型包括：整数(int),浮点数(double/float)，字符串(string),布尔值(bool)。  

所谓基本数据类型，就是某个语言中最常见、最常使用的、且可作为构成其他类的基本元素的类。  

当然不同语言上，有的还会对基本类型还会做进一步的细分。  


**python**    

注意：python里，一个变量可以通过赋值指向不同类型的对象。  

单变量定义
```python
counter = 100          # 整型变量
miles   = 1000.0       # 浮点型变量
name    = "runoob"     # 字符串
condition = True #布尔
```
注意生成的字符串由特殊的种类：原始字符串、格式化字符串、跨行字符串

```python
生成原始字符串：
str_r = r"Hello\1"

生成格式化字符串：
name = "Runoob"
str_f = f"Hello {name}"  # 替换变量

跨行字符串(三引号):
para_str = """这是一个多行字符串的实例
多行字符串可以使用制表符
TAB ( \t )。
也可以使用换行符 [ \n ]。
"""

```

多变量定义(Python3 支持 int、float、bool、complex（复数）)
```python
a, b, c, d = 20, 5.5, True, 4+3j #多变量定义
<class 'int'> <class 'float'> <class 'bool'> <class 'complex'>
```

查询变量类型的方法：type(),isinstance()

```python
type(a)
isinstance(a, int)
# type()不会认为子类是一种父类类型。
# isinstance()会认为子类是一种父类类型。
```


## 2.2 定义类的对象数据
python  

```python
p1 = MyClass()
```


# 3.存储数据

一般高级编程语言都有如下数据结构：数组、集合、映射

本部分**只讨论生成**一个数据结构的方式：  

 1. 由空的结构 被赋值 或 添加生成(最常见)
 2. 由本身的数据结构截取生成而来
 3. 由其他数据结构转换而来(可能是强转，也可能是其他数据结构的私有方法生成的)

## 3.1数组
**python**  

注意：python中`推导式`机制，可以从一个数据序列构建另一个新的数据序列的结构体。Python 支持各种数据结构的推导式：
列表(list)推导式
字典(dict)推导式
集合(set)推导式
元组(tuple)推导式

List（列表）：可变
```python
 1. 由空的结构 被赋值 或 添加生成(最常见)
list1 = ['Google', 'Runoob', 1997, 2000] #赋值
list2 = [] 
for i in range(10):
	list2.append(i)#末尾添加apeend
list2.insert(index,value)# 指定位置添加
list3 = [ i for i in range(10) if i%2==0] #列表生成式、又叫推导式 

 2. 由本身的数据结构截取生成而来
 list4 = list3[1:]
 list4 = list3[:-1]
 
 3. 由其他数据结构转换而来(可能是强转，也可能是其他数据结构的私有方法生成的)
 set1 = {'Google', 'Runoob', 1997}
 dict1 = {1:"q",2:"w"}
 list5 = list(set1) #强转
 list6,list7 = dict1.keys(),dict2.values()#由字典的键值对生成
```

Tuple（元组）：不可变
```python
 1. 由空的结构 被赋值 或 添加生成(最常见)
tup1 = ('Google', 'Runoob', 1997, 2000)
tup2 = "a", "b", "c", "d"   #  不需要括号也可以
tup3 = (expression for item in Sequence if conditional ) #推导式生成
 2. 由本身的数据结构截取生成而来
 tup3 = tup1 + tup2 #元组中的元素值是不允许修改的，但我们可以对元组进行连接组合

```

## 3.2 集合
**python**   

set(集合):可变
可以使用大括号 `{ }` 或者 `set() `函数创建集合，注意：创建一个空集合必须用 `set() `而不是 { }，因为 `{ } `是用来创建一个空字典。

```python
 1. 由空的结构 被赋值 或 添加生成(最常见)
set1 = set()#空结构
for i in range(10):
	set1.add(i)
set2 = ( i*2 for i in range(10) )#推导式生成的

# 还有一个update方法，也可以添加元素，且参数可以是列表，元组，字典等，语法格式如下：
>>> set3 = set(("Google", "Runoob", "Taobao"))
>>> set3.update({1,3})
>>> print(set3)
{1, 3, 'Google', 'Taobao', 'Runoob'}
>>> set3.update([1,4],[5,6])  
>>> print(set3)
{1, 3, 4, 5, 6, 'Google', 'Taobao', 'Runoob'}

 2. 由其他数据结构转换而来(可能是强转，也可能是其他数据结构的私有方法生成的)
set4 = set(list1)
```

## 3.3 映射
dict(字典)：可变
```python
 1. 由空的结构 被赋值 或 添加生成(最常见)
tinydict = {'Name': 'Runoob', 'Age': 7, 'Class': 'First'}
emptyDict = {}# 使用大括号 {} 来创建空字典
tinydict['Age'] = 8               # 更新 Age
tinydict['School'] = "菜鸟教程"  # 添加信息
testdict = { key_expr: value_expr for value in collection if condition } #推导式

```

# 4.操控数据
## 4.1 操控内存数据
### 4.1.1操控基本类型数据
#### (1)基本数值运算与常见的数学函数
**python**
加减乘除，取余乘方

```python
>>> 5 + 4  # 加法
9
>>> 4.3 - 2 # 减法
2.3
>>> 3 * 7  # 乘法
21
>>> 2 / 4  # 除法，得到一个浮点数
0.5
>>> 2 // 4 # 除法，得到一个整数
0
>>> 17 % 3 # 取余
2
>>> 2 ** 5 # 乘方
32
```
数学函数:绝对值，保留指定位数四舍五入后的小数，最大值，最小值，向上取整，向下取整，指对数函数
| 函数          | 功能                                                         |
| ------------- | ------------------------------------------------------------ |
| abs(x)        | 绝对值                                                       |
| round(x [,n]) | 返回浮点数 x 的四舍五入值，如给出 n 值，则代表舍入到小数点后的位数。 |
| math. ceil(x) | 返回数字的上入整数，如math.ceil(4.1) 返回 5                  |
| math.floor(x) | 返回数字的下舍整数，如math.floor(4.9)返回 4                  |
| math. exp(x)  | 返回e的x次幂(ex),如math.exp(1) 返回2.718281828459045         |
| math.log(x)   | 默认以e为底，如math.log(math.e)返回1.0,math.log(100,10)返回2.0 |


#### （2）类型转换
##### 隐式类型转换
基本所有语言的隐式类型转换都遵守这一个**避免数据丢失的规则**：`在数值运算时，较低数据类型（整数）就会转换为较高数据类型（浮点数）以避免数据丢失`
##### 显式类型转换
**python**

`标记格式`的函数为python常用函数
| 函数                  | 功能                                                    |
| --------------------- | ------------------------------------------------------- |
| **int**(x [,base])    | 将x转换为一个整数                                       |
| **float**(x)          | 将x转换到一个浮点数                                     |
| **str**(x)            | 将对象 x 转换为字符串                                   |
| complex(real [,imag]) | 创建一个复数                                            |
| **tuple**(s)          | 将序列 s 转换为一个元组                                 |
| **list**(s)           | 将序列 s 转换为一个列表                                 |
| **set**(s)            | 转换为可变集合                                          |
| dict(d)               | 创建一个字典。d 必须是一个 (key, value)元组序列。       |
| frozenset(s)          | 转换为不可变集合                                        |
| chr(x)                | 将一个整数转换为一个字符                                |
| ord(x)                | 将一个字符转换为它的整数值                              |
| repr(x)               | 将对象 x 转换为表达式字符串                             |
| **eval**(str)         | eval() 函数用来执行一个字符串表达式，并返回表达式的值。 |
| hex(x)                | 将一个整数转换为一个十六进制字符串                      |
| oct(x)                | 将一个整数转换为一个八进制字符串                        |

说明：eval

```python
>>>x = 7
>>> eval( '3 * x' )
21
>>> eval('pow(2,2)')
4
>>> eval('2 + 2')
4
>>> n=81
>>> eval("n + 4")
85
```


#### (3)字符串运算
字符串也是一个不可变序列。(无更新删除操作)
>所有序列的操作，都包括：索引访问，区间截取，元素搜索，子集搜索,合并拼接,更新删除。当然不同序列也有自己不同的操作。

 - 索引访问、区间截取

```python
str1 = "hello hello"
var = str1[1] # 索引访问
var = str1[1:3] # 区间截取
```

 - 元素/子集搜索
```python
if "he" not in str1:
	print("not in")
```
 - 合并拼接
```python
str2= "hello hello" + str1
str2 = str1*n # 把str1重复n次后返回重复后的字符串
```
 - 特殊操作

| 方法                                     | 功能                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| str.count(str1, beg= 0,end=len(string))  | 返回 str1 在 string 里面出现的次数，如果 beg 或者 end 指定则返回指定范围内 str 出现的次数 |
| str.lower()                              | 转换字符串中所有大写字符为小写.                              |
| str.title()                              | 返回"标题化"的字符串,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle()) |
| str.upper()                              | 转换字符串中的小写字母为大写                                 |
| str.replace(old, new [, max])            | 把 将字符串中的 old 替换成 new,如果 max 指定，则替换不超过 max 次。 |
| str.split(str="", num=string.count(str)) | 以 str 为分隔符截取字符串，如果 num 有指定值，则仅截取 num+1 个子字符串 |
| str.join(seq)                            | 以指定字符串作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串 |
| str.strip([chars])                       | 去除字符串左右两端的空格                                     |


### 4.1.2操控数据存储的结构 
>所有序列的操作，都包括：索引访问，区间截取，元素搜索，子集搜索,合并拼接。当然不同序列也有自己不同的操作。

python中所有序列包括：字符串、列表、元祖、集合、字典
#### (1)共有操作

| 方法          | 功能                                   | 适用           |
| ------------- | -------------------------------------- | -------------- |
| len(seq)      | 返回序列长度                           | 所有seq        |
| max(seq)      | 返回给定参数的最大值，参数可以为序列。 | str,list,tuple |
| min(seq)      | 返回给定参数的最小值，参数可以为序列。 | str,list,tuple |
| seq.count(x)  | 统计x在seq中出现次数                   | str,list,tuple |
| seq.update()  | 合并                                   | dict，set      |
| seq.add()     | 添加新元素                             | set            |
| seq.append()  | 添加新元素                             | list           |
| seq.remove(x) | 删除x                                  | list,set       |


#### (2)列表运算
 - 删除更新
```python
list1 = ['Google', 'Runoob', 1997, 2000]
list1[1] = "Baidu" # 元素更新
del list1[index] #删除指定索引元素
list1.remove(value) #删除指定元素
list1.pop() # 删除最后一个元素，并返回该元素的值
```
 - 索引访问、区间截取

```python
var = list1[1] # 索引访问
var = list1[1:3] # 区间截取
```

 - 元素搜索
```python
#元素搜索
if "he" not in list1:
	print("not in")
```
 - 合并拼接
```python
list2= [1,2,3] + list1
list1 += [36, 49, 64, 81, 100]
list2 = list1*n #把list1重复n次后返回重复拼接的列表
```
 - 特殊操作

| 方法            | 功能                           |
| --------------- | ------------------------------ |
| list.count(obj) | 统计某个元素在列表中出现的次数 |
| list.reverse()  | 反向列表中元素                 |
| list.copy()     | 复制列表                       |


#### (3)元祖运算
 - 索引访问、区间截取

```python
tup1 = ('Google', 'Runoob', 1997, 2000)
var = tup1[1] # 索引访问
var = tup1[1:3] # 区间截取
```

 - 元素搜索
```python
#元素搜索
if "he" not in list1:
	print("not in")

```
 - 合并拼接
```python
tup1= (1,2,3) + tup1
tup1 += (36, 49, 64, 81, 100)
tup2 = tup1*n
```
#### (4)集合运算
集合无索引，因此无索引访问
 - 删除更新
```python
set1 = {'apple', 'orange', 'banana'}
set1.remove(value) #删除指定元素
```

 - 元素搜索
```python
#元素搜索
if "he" not in set1:
	print("not in")

```
 - 合并拼接
```python
set2.update(set1)
```
 - 特殊操作

| 方法   | 功能                                                       |
| ------ | ---------------------------------------------------------- |
| a - b  | 返回集合a与集合b的差集(集合a中包含而集合b中不包含的元素)   |
| a \| b | 返回集合a和集合b的并集                                     |
| a & b  | 返回集合a和集合b的交集                                     |
| a ^ b  | 返回不同时包含于a和b的元素集合(返回集合a和集合b的交集的反) |
#### (5)字典运算
 - 删除更新
```python
tinydict = {'Age': 7, 'Name': 'First'}
del tinydict['Name'] # 删除键 'Name'
del tinydict         # 删除字典
tinydict['Age'] = 8               # 更新 Age
tinydict['School'] = "菜鸟教程"  # 添加信息
```
 - 键访问

```python
var = tinydict['Age']
```

 - 元素搜索
```python
#键/值搜索
if "he" not in tinydict.keys()/tinydict.values():
	print("not in")
```
 - 合并拼接
```python
dict2.update(dict1)
```
- 特殊操作

| 方法                        | 功能                                                      |
| --------------------------- | --------------------------------------------------------- |
| dict.keys()                 | 返回所有的键                                              |
| dict.values()               | 返回所有的值                                              |
| dict.items()                | 以列表返回返回所有的键值对                                |
| dict.get(key, default=None) | 返回指定键的值，如果键不在字典中返回 default 设置的默认值 |

#### (6)常用操作：去重，排序，计数
以list为数据源  

 1. 去重

```python
list1 = list(set(list1))
```
 2. 排序

```python
# 列表排序 reverse=True从大到小排序
list1 = sorted(list1, key=lambda e: e, reverse=True)
#字典排序 根据值排序
dict1 = sorted(dict1.items(), key=lambda e: e[1], reverse=True) 
```
 3. 计数

```python
cnt_dict = {}
for e in list1:cnt_dict[e] = cnt_dict.get(e,0) + 1
```
### 4.1.3操控数据的逻辑实现 
#### (1)条件表达式
逻辑运算符/操作符  

`or` `and` `<`  `<=` `>`  `>=`  `==`  `!=	` 

#### (2)if语句/三元表达式

```python
if condition_1:
    statement_block_1
elif condition_2:
    statement_block_2
else:
    statement_block_3

var = "真结果" if condition else "假结果"
```
#### (3)for循环

```python
for i in range(num):print(i) #[0,num)
for i in range(start,stop,step):print(i) #[start,stop) start = start + step
for i,e in enumerate(list):print(i,e)
for e in list1:print(e)

```

#### (4)while循环

```python
while 判断条件(condition)：
    执行语句(statements)……
```

### 4.1.4操纵方法的封装（自定义封装与引用封装）

#### (1)函数
函数的入参：类型约束，默认参数，不定长参数
> 加了一个星号 * 的参数会以元组(tuple)的形式导入，存放所有未命名的变量参数。
> 加了两个星号 ** 的参数会以字典的形式导入。

返回值：如何返回多个值

```python
def func(arg1:str = "默认字符串",*vartuple,**var_args_dict)：
	for var in vartuple:
      print (var)
    for k,v in var_args_dict:
    	print(k,v)
	return 1,2
```

#### (2)类
类的属性，方法，继承，访问范围，多构造函数

```python
class Site(object):#继承自object类
    def __init__(self, name, url):
        self.name = name       # public
        self.__url = url   # private
 	@classmethod
    def myinit(self, name):#自定义构造函数
    	# 利用@classmethod装饰器实现 Python 中构造函数重载
    	# 记得返回self
        return self(name, "myurl")
    def who(self):
        print('name  : ', self.name)
        print('url : ', self.__url)
 
    def __foo(self):          # 私有方法
        print('这是私有方法')
 
    def foo(self):            # 公共方法
        print('这是公共方法')
        self.__foo()
```
任意类的方法(可重载)
| 方法          | 描述                       |
| ------------- | -------------------------- |
| \_\_init__    | 构造函数，在生成对象时调用 |
| \_\_del__     | 析构函数，释放对象时使用   |
| \_\_repr__    | 打印，转换                 |
| \_\_setitem__ | 按照索引赋值               |
| \_\_getitem__ | 按照索引获取值             |
| \_\_len__:    | 获得长度                   |
| \_\_cmp__     | 比较运算                   |
| \_\_call__    | 函数调用                   |
| \_\_add__     | 加运算                     |
| \_\_sub__     | 减运算                     |
| \_\_mul__     | 乘运算                     |
| \_\_truediv__ | 除运算                     |
| \_\_mod__     | 求余运算                   |
| \_\_pow__     | 乘方                       |

抽象类，抽象方法

```python
from abc import ABCMeta,abstractmethod #导入 abstract class
class Discriminator(object):
    
    __metaclass__ = ABCMeta #指定这是一个抽象类
    
    
    def __init__(self,config:Config) -> None:
        self.dtype = DType.ABD
        self.config = config
        pass
    
    @abstractmethod  #抽象方法
    def judge_sqli(self,parameter:str)->IsSqliJudgeRes:
        '''
        parameter:判断
        '''
        pass
```

枚举类

```python
from enum import Enum
class DType(Enum):
    ABD = "Abstract Discriminator" #抽象类鉴别器
    REG = "Regular Discriminator" #规则鉴别器
    DLD = "Deep Learning Discriminator" #深度学习鉴别器
```

##  4.2 操纵外存数据




### (1)写内存数据到外存
写到外存文本文件:

```python
# 覆盖写
filename = 'write_data.txt'
with open(filename,'w',encoding="utf-8") as f: # 如果filename不存在会自动创建， 'w'表示写数据，写之前会清空文件中的原有数据！
    f.write("I am Meringue.\n")
    f.write("I am now studying in NJTECH.\n")
# 追加写
with open(filename,'a',encoding="utf-8") as f: # 'a'表示append,即在原来文件内容后继续写数据（不清楚原有数据）
    f.write("I major in Machine learning and Computer vision.\n")
```
写二进制文件

>简言之就是用struct.pack将要变成字节的数据打包然后以字节的形式写入到二进制文件,对于大于255的数字可以将‘B’换成‘H’或者‘L’，可以百度一下struct的用法

```python
import struct
 
list_dec = [1, 2, 3, 4, 53, 100, 220, 244, 255]
with open('hexBin.bin', 'wb')as fp:
    for x in list_dec:
        a = struct.pack('B', x)
        fp.write(a)

```
### (2)读外存文件到内存
读文本文件：

```python
with open(configPath, 'r',encoding="utf-8") as f:
        lines = f.readlines()
#去除换行符，去除空格，以":="分割
 for line in lines:
     line = line.replace("\n","")
     line = line.strip()
     line = line.split(":=")
```

