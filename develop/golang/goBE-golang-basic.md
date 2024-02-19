---
title: 'golang基础教程'
date: 2023-04-15 08:15:16
tags: [go]
published: true
hideInList: true
feature: 
isTop: false
---


<meta name="referrer" content="no-referrer"/>



# golang基础教程

第一部分为：golang的基础语法：数据与方法，数据结构与算法、外部库的管理与使用、应用级编程。

第二部分为：golang的特性

第三部分为：golang的应用：golang与数据库的交互；golang的web请求；

参考资料：

[8小时转职Golang工程师](https://www.yuque.com/aceld/mo95lb/dsk886)

[Go语言101 (go101.org)](https://gfw.go101.org/)

[Introduction · Go入门指南 (studygolang.com)](https://books.studygolang.com/the-way-to-go_ZH_CN/)

[Go 程序员面试笔试宝典 | Go 程序员面试笔试宝典 (golang.design)](https://golang.design/go-questions/)

# 一、golang简介

## go语言的优缺点

### 优势

![image-20230316002007968](https://gitee.com/yushen611/img/raw/master/image-20230316002007968.png)

<img src="https://gitee.com/yushen611/img/raw/master/image-20230316002105627.png" alt="image-20230316002105627" style="zoom:50%;" />

### 适合方向

![image-20230316002044290](https://gitee.com/yushen611/img/raw/master/image-20230316002044290.png)

### 可能缺点

![image-20230316002140305](https://gitee.com/yushen611/img/raw/master/image-20230316002140305.png)

注：后引入了泛化类型

## go的安装

### 下载安装与检测环境变量

GOLANG官网:[https://golang.google.cn/](https://link.zhihu.com/?target=https%3A//golang.google.cn/)

命令行输入`go version`，查看是否安装成功

Go开发相关的环境变量用`go env`命令查看

![image-20230315232405998](https://gitee.com/yushen611/img/raw/master/image-20230315232405998.png)

### 配置环境变量：GOPROXY

从字面意思就能看出，GOPROXY表示的是go的代理设置，之所以有这个环境变量，是因为go这种语言不像C语言，在C语言中，如果我们想要使用别人的第三方代码，一般有两种途径：
1、将第三方代码源码合并到自己的工程文件中，再合并编译。
2、将第三方代码编译生成的共享库***.so或*.dll** 文件放到工程目录下，然后通过条件编译来使用。

而在go语言中，类似于java，可以在编程时，引入第三方代码的库地址，比如git仓库，然后在编译的时候，IDE会自动的拉取第三方库文件到当前工程。

这样做虽然很方便，但是带来了一个问题：网速和限制，因为一些第三方代码库是在国外服务器上的，因为一些限制，我们不能很顺利的使用和下载这些仓库，这样就会导致下载缓慢或者失败，所以这个时候就需要一个 代理来实现下载，这个代理就是中间商，可以跨过限制来访问。

默认GoPROXY配置是：`GOPROXY=https://proxy.golang.org,direct`，
由于国内访问不到 `https://proxy.golang.org` 所以我们需要换一个`PROXY`，这里推荐使用`https://goproxy.io` 或 `https://goproxy.cn` 或者 `https://mirrors.aliyun.com/goproxy/`。



可以执行下面的命令修改`GOPROXY`：

`go env -w GOPROXY=https://goproxy.cn,direct`

### 配置环境变量：GO111MODULE

GO111MODULE 有三个值：off, on和auto（默认值）。

* GO111MODULE=off，go命令行将不会支持module功能，寻找依赖包的方式将会沿用旧版本那种通过vendor目录或者GOPATH模式来查找。
* GO111MODULE=on，go命令行会使用modules，而一点也不会去GOPATH目录下查找。
* GO111MODULE=auto，默认值，go命令行将会根据当前目录来决定是否启用module功能。这种情况下可以分为两种情形：
      当前目录在GOPATH/src之外且该目录包含go.mod文件
      当前文件在包含go.mod文件的目录下面。

执行以下命令开启go mod管理

`go env -w GO111MODULE=on`



### 查看环境变量：GOPATH 和 GOROOT

> https://blog.csdn.net/qq_38151401/article/details/105729884

不同于其他语言，**go中没有项目的说法**，只有包, 其中有两个重要的路径，GOROOT 和 GOPATH

#### GOROOT 

**GOROOT**：GOROOT就是Go的安装目录，（类似于java的JDK）

GOROOT是Go的安装路径。<u>GOROOT在绝大多数情况下都不需要修改</u>

![image-20230315230929017](https://gitee.com/yushen611/img/raw/master/image-20230315230929017.png)

#### GOPATH

**GOPATH**：GOPATH是我们的工作空间,保存go项目代码和第三方依赖包
**GOPATH**可以设置多个，其中，第一个将会是默认的包目录，使用 go get 下载的包都会在第一个path中的src目录下，使用 go install时，在哪个GOPATH中找到了这个包，就会在哪个GOPATH下的bin目录生成可执行文件

GOPATH是开发时的工作目录。用于：

1. 保存编译后的二进制文件。
2. `go get`和`go install`命令会下载go代码到GOPATH。
3. import包时的搜索路径

使用GOPATH时，GO会在以下目录中搜索包：

1. `GOROOT/src`：该目录保存了Go标准库代码。
2. `GOPATH/src`：该目录保存了应用自身的代码和第三方依赖的代码。



假设程序中引入了如下的包：

```go
import "Go-Player/src/chapter17/models"
```

第一步：Go会先去**GOROOT的scr目录**中查找，很显然它不是标准库的包，没找到。
第二步：继续在**GOPATH的src目录**去找，准确说是**GOPATH/src/Go-Player/src/chapter17/models**这个目录。如果该目录不存在，会报错找不到package。在使用GOPATH管理项目时，需要按照GO寻找package的规范来合理地保存和组织Go代码。

**注意！！！**

> Go1.14版本之后，都推荐使用`go mod`模式来管理依赖了，**也不再强制我们把代码必须写在`GOPATH`下面的`src`目录了，你可以在你电脑的任意位置编写go代码。**





## go的hello world

编写一个文件hello.go

### **编写最简hello world代码**

```go
package main

import "fmt"

func main() {
      /* 简单的程序 万能的hello world */
      fmt.Println("Hello Go")
}
```

**第一行**：**package main** 程序的第一行声明了名为main的package。<u>Go语言的代码是通过package来组织的</u>，package的概念和其他语言里的package，module概念类似，**是一个逻辑的，包含了相同功能代码的集合**。**一个package会包含一个或多个.go源代码文件。每一个源文件都是以package开头。**比如我们的例子里是package main。这行声明语句表示该文件是属于哪一个package。同时需要注意，一个程序的`main`入口函数必须不带任何输入参数和返回结果

需要注意，<u>package main是一个比较特殊的package</u>。**main package是Go程序的入口**。准确说，Go程序的入口是<u>名为main的package中的main方法</u>(即例子中的main方法)。

**第二行**： import package声明语句后紧跟着是import语句。**import语句会引入其他package到当前文件中，这样就可以在当前文件使用其他package中的变量，常量，类型，方法等**。Go的import和Java的import，C++的include类似。Go标准库已经提供了100多个package，fmt这个package包含接受输入，格式化输出的各种函数。Println是其中的一个常用函数，可以格式化地输出一段文本。

**第三行**： ==func main 第三行声明了一个函数==，函数名为main。在Go语言中使用func关键字来声明一个函数。格式为:

```go
func 函数名(参数名1 参数类型1, 参数名2 参数类型2, ... ) (返回类型1, 返回类型2, ...)
```

比如：

```go
func sayHello(name string, age int) string
```

声明了一个sayHello方法。接收两个参数：string类型的name和int类型的age。并返回一个string类型的值。前面也提到了，在main这个package里，main函数也是一个特殊的函数，这是整个程序的入口(其实C系语言差不多都是这样)。

**第四行**： fmt.Println(...)可以将字符串输出到控制台，并在最后自动增加换行字符 \n。 使用 fmt.Print("hello, world\n") 可以得到相同的结果。 Print 和 Println 这两个函数也支持使用变量，如：fmt.Println(arr)。如果没有特别指定，它们会以默认的打印格式将 变量 arr 输出到控制台。

> 其实也可以不用调用fmt包直接print("hello world") 或者 prntln("hello world") , 因为print是go的一个标识符了)

### 编写多包hello world

```go
package main //程序的包名

/*
import "fmt"
import "time"
*/
import (
	"fmt"
	"time"
)

// main函数
func main() { //函数的{  一定是 和函数名在同一行的，否则编译错误
	//golang中的表达式，加";", 和不加 都可以，建议是不加
	fmt.Println(" hello Go!")

	time.Sleep(1 * time.Second)
}

```



### go语法特色总结

从hello world可以看出go的一些特点。

1. main函数必须在main包里，main包里的某一个go文件必须包含main函数
2. import包的方式有两种，一种是多行import,如`import pkg1 `，另一种是`import (pkg1,pkg2,...)`
3. **函数的定义关键词是func,返回值类型直接写在入参右括号的旁边，可以有多个返回值。函数体的第一个大括号必须跟函数名在同一行。**
4. 加不加分号都可以，但是**推荐不加分号**

由于Hello world程序比较简单，其他特点也包括：

1. Go语言不需要显示声明方法或变量的作用范围。即不用显式声明public或private。其实<u>Go语言是通过变量或方法名的首字母是大写还是小写来确定作用范围的</u>。**大写字母开头的为public，小写字母开头的为private**。
2. Go语言**声明变量和方法参数时，名字在前，类型在后**。比如<u>var name string</u>。这和C系语言，Java语言都不同。

## go的常用命令

### 编译与运行

1. go build 编译：编译成可执行文件

```go
go build hello.go
```

用于测试编译包，在项目目录下生成可执行文件（有main包）。linux生成可执行文件，./可以直接运行；win生成.exe文件

2. go run编译加运行：编译后直接运行

```go
go run hello.go
```

go run子命令只是一种方便的方式来运行简单的Go程序。
对于正式的项目，最好使用go build或者go install子命令构建可执行程序文件来运行Go程序。

3. go install :主要用来生成库和工具。

可以直接在某一文件夹下使用`go build`或者`go install`

主要用来生成库和工具。一是编译包文件（无main包），将编译后的包文件放到 pkg 目录下（`$GOPATH/pkg`）。二是编译生成可执行文件（有main包），将可执行文件放到 bin 目录（`$GOPATH/bin`）。

> go build VS go install
>
> - go build 不能生成包文件, go install 可以生成包文件
> - go build 生成可执行文件在当前目录下， go install 生成可执行文件在bin目录下（`$GOPATH/bin`）
> - 相同点：都能生成可执行文件

### 查看环境变量

```go env
go env
```

查看GOPATH，GOROOT,GOPROIXY环境变量都可以使用该命令查看。

### 格式化代码

运行go fmt进行格式化。

```go
go fmt
```



## go的关键字与标识符简介

### go的25个关键字

关键字是一些特殊的用来帮助编译器理解和解析源代码的单词。

截至目前（Go 1.20），**Go中共有25个关键字**。

break     default      func    interface  selectcase      defer        go      map        structchan      else         goto    package    switchconst     fallthrough  if      range      typecontinue  for          import  return     var



**go的25个关键字可以分为4组：声明组，组合组，流程控制组，特殊组**

const、func、import、package、type和var用来声明各种代码元素。

chan、interface、map和struct用做
	一些组合类型的字面表示中。

break、case、continue、default、 else、fallthrough、for、 goto、if、range、 return、select和switch用在流程控制语句中。
	详见基本流程控制语法（第12章）。

defer和go也可以看作是流程控制关键字，
	但它们有一些特殊的作用。详见协程和延迟函数调用

### 标识符

**标识符是用于命名的**，即是指Go语言对各种变量、方法、函数等命名时使用的字符序列，标识符由若干个字母、下划线`_`、和数字组成，且第一个字符必须是字母。通俗的讲就是凡可以自己定义的名称都可以叫做标识符。

**空标识符**`_`是用于接受多返回值时，占位的

**标识符的访问权限**：Go语言中的变量、函数、常量名称的首字母也可以大写，如果首字母大写，则表示它可以被其它的包访问（类似于 [Java](http://c.biancheng.net/java/) 中的 public）；如果首字母小写，则表示它只能在本包中使用 (类似于 Java 中 private）。

**go语言的预定标识符**：预定义标识符一共有 36 个，**主要包含Go语言中的基础数据类型和内置函数**，这些预定义标识符也不可以当做标识符来使用。

| append | bool    | byte    | cap     | close  | complex | complex64 | complex128 | uint16  |
| ------ | ------- | ------- | ------- | ------ | ------- | --------- | ---------- | ------- |
| copy   | false   | float32 | float64 | imag   | int     | int8      | int16      | uint32  |
| int32  | int64   | iota    | len     | make   | new     | nil       | panic      | uint64  |
| print  | println | real    | recover | string | true    | uint      | uint8      | uintptr |



# 二、数据与方法

主要包括：数据的类型，数据的定义，方法的定义，逻辑与控制

## 数据的类型

### 数据的基本类型

类型（type）可以被看作是值（value）的模板，值可以被看作是类型的实例。本段将介绍内置（或称为预声明的）基本类型和它们字面量的表示形式。本段不介绍组合类型。

#### **Go支持的内置基本类型**

* 一种内置布尔类型：bool。

* 11种内置整数类型：int8、uint8、int16、uint16、int32、uint32、int64、uint64、int、uint和uintptr。

* 两种内置浮点数类型：float32和float64。

* 两种内置复数类型：complex64和complex128。

* 一种内置字符串类型：string。

我们可以不用引入任何代码包而直接使用这些内置基本类型。

除了bool和string类型，<u>其它的15种内置基本类型都称为数值类型</u>（整型、浮点数型和复数型）。

**Go中有两种内置类型别名**（type alias）：

* **byte**是uint8的内置别名。我们可以将byte和uint8看作是同一个类型。

* **rune**是int32的内置别名。我们可以将rune和int32看作是同一个类型。

#### **内置基本类型的尺寸**

<u>以u开头的整数类型称为无符号整数类型</u>。无符号整数类型的值都是非负的。一个数值类型名称中的数字表示每个这个类型的值将在内存中占有多少二进制位（以后简称位）。二进制位常称为比特（bit）。比如，一个uint8的值将占有8位。我们称uint8类型的值的尺寸是8位。因此，最大的uint8值是255（28-1），而最大的int8值是127（27-1），最小的int8值是-128（-27）。



任一个类型的所有值的尺寸都是相同的，所以一个值的尺寸也常称为它的<u>类型的尺寸</u>。

更多的时候，我们使用<u>字节（byte）</u>做为值尺寸的度量单位。一个字节相当于8个比特。所以uint32类型的尺寸为4，即每个uint32值占用4个字节。

<u>uintptr、int以及uint类型</u>的值的尺寸依赖于具体编译器实现。通常地，在64位的架构上，int和uint类型的值是64位的；在32位的架构上，它们是32位的。编译器必须保证uintptr类型的值的尺寸能够存下任意一个内存地址。

一个<u>complex64</u>复数值的实部和虚部都是float32类型的值。一个<u>complex128</u>复数值的实部和虚部都是float64类型的值。

从逻辑上说，一个<u>字符串</u>值表示一段文本。在内存中，一个字符串存储为一个字节（byte）序列。此字节序列体现了此字符串所表示的文本的UTF-8编码形式。

#### 内置基本类型的零值

每种类型都有一个零值。一个类型的零值可以看作是此类型的默认值。

* 一个布尔类型的零值表示真假中的假。
* 数值类型的零值都是零（但是不同类型的零在内存中占用的空间可能不同）。
* 一个字符串类型的零值是一个空字符串。

#### 基本类型的字面量表示形式

一个值的字面形式称为一个字面量，它表示此值在代码中文字体现形式（和内存中的表现形式相对应）。一个值可能会有很多种字面量形式。

参考：

> [Go编程入门 - 基本类型和它们的字面量表示 - 《Go语言101 v1.16.a-1》 - 书栈网 · BookStack](https://www.bookstack.cn/read/Golang101-v1.16.a-1/basic-types-and-value-literals.html)
>
> [详解 Go 中的 rune 类型 - 技术颜良 - 博客园 (cnblogs.com)](https://www.cnblogs.com/cheyunhua/p/16007219.html)



* **布尔值的字面量形式**：false和true

* **整数类型值的字面量形式**

  <u>整数类型值有四种字面量形式</u>：十进制形式（decimal）、八进制形式（octal）、十六进制形式（hex）和二进制形式（binary）

  比如，下面的三个字面量均表示十进制的15：

  ```go
  0xF // 十六进制表示（必须使用0x或者0X开头）
  0XF
  
  017 // 八进制表示（必须使用0、0o或者0O开头）
  0o17
  0O17
  
  0b1111 // 二进制表示（必须使用0b或者0B开头）
  0B1111
  
  15  // 十进制表示（必须不能用0开头）
  ```

* **浮点数类型值的字面量形式**

  一个浮点数的完整字面量形式包含一个十进制整数部分、一个小数点、一个十进制小数部分和一个整数指数部分。 常常地，某些部分可以根据情况省略掉。一些例子（`xEn`表示`x`乘以`10n`的意思，而`xE-n`表示`x`除以`10n`的意思）：

  ```go
  1.23
  01.23 // == 1.23
  .23
  1.
  // 一个e或者E随后的数值是指数值（底数为10）。
  // 指数值必须为一个可以带符号的十进制整数字面量。
  1.23e2  // == 123.0
  123E2   // == 12300.0
  123.E+2 // == 12300.0
  1e-1    // == 0.1
  .1e0    // == 0.1
  0010e-2 // == 0.1
  0e+5    // == 0.0
  ```

* **rune的字面量形式**

  **rune的概念介绍**

  rune是int32的别名，==它是用来区分字符值和整数值（重点）==。使用单引号定义 ，返回采用 UTF-8 编码的 Unicode 码点。Go 语言通过 `rune` 处理中文，支持国际化多语言。

  Go 语言把字符分 `byte` 和 `rune` 两种类型处理。`byte` 是类型 `unit8` 的别名，用于存放占 1 字节的 ASCII 字符，如英文字符，返回的是字符原始字节。`rune` 是类型 `int32` 的别名，用于存放多字节字符，如占 3 字节的中文字符，返回的是字符 Unicode 码点值。如下图所示：

  ```go
  s := "Go语言编程"
  // byte
  fmt.Println([]byte(s)) // 输出：[71 111 232 175 173 232 168 128 231 188 150 231 168 139]
  // rune
  fmt.Println([]rune(s)) // 输出：[71 111 35821 35328 32534 31243]
  ```

  ![图片](https://gitee.com/yushen611/img/raw/master/640)

  > 在我看来，`rune` 类型只是一种名称叫法，表示用来处理长度大于 1 字节（ 8 位）、不超过 4 字节（ 32 位）的字符类型。但万变不离其宗，我们使用函数时，无论传入参数的是原始字符串还是 `rune`，最终都是对字节进行处理。看似陌生的事物，沉下心了解到其本质以后，才发现原来并不陌生，缺少的只是正视它的勇气！

  **rune的表示(Unicode码点值的表示)**

  在Go中，一个rune值表示一个Unicode码点。 一般说来，我们可以将一个Unicode码点看作是一个Unicode字符。 但是，我们也应该知道，有些Unicode字符由多个Unicode码点组成。 每个英文或中文Unicode字符值含有一个Unicode码点。

  一个rune字面量由若干包在一对单引号中的字符组成。 包在单引号中的字符序列表示一个Unicode码点值。 rune字面量形式有几个变种，其中最常用的一种变种是<u>将一个rune值对应的Unicode字符直接包在一对单引号</u>中。比如：

  ```go
  'a' // 一个英文字符
  'π'
  '众' // 一个中文字符
  ```

  下面这些rune字面量形式的变种和`'a'`是等价的 （字符`a`的Unicode值是97）。(事实上，在日常编程中，这四种rune字面量形式的变种很少用来表示rune值。 它们多用做字符串的双引号字面量形式中的转义字符)

  ```go
  '\141'   // 141是97的八进制，表示一个byte值
  '\x61'   // 61是97的十六进制，表示一个byte值
  '\u0061' //0061是97的十六进制，表示一个rune值(Unicode码点值)
  '\U00000061'//00000061是97的十六进制，表示一个rune值(Unicode码点值)
  ```

  > 注意：`\`之后必须跟随三个八进制数字字符（0-7）表示一个byte值， `\x`之后必须跟随两个十六进制数字字符（0-9，a-f和A-F）表示一个byte值， `\u`之后必须跟随四个十六进制数字字符表示一个rune值（此rune值的高四位都为0）， `\U`之后必须跟随八个十六进制数字字符表示一个rune值。 这些八进制和十六进制的数字字符序列表示的整数必须是一个合法的Unicode码点值，否则编译将失败。

  转义字符。如果一个rune字面量中被单引号包起来的部分含有两个字符， 并且第一个字符是`\`，第二个字符不是`x`、 `u`和`U`，那么这两个字符将被转义为一个特殊字符。 目前支持的转义组合为：

  ```go
  \a   (rune值：0x07) 铃声字符
  \b   (rune值：0x08) 退格字符（backspace）
  \f   (rune值：0x0C) 换页符（form feed）
  \n   (rune值：0x0A) 换行符（line feed or newline）
  \r   (rune值：0x0D) 回车符（carriage return）
  \t   (rune值：0x09) 水平制表符（horizontal tab）
  \v   (rune值：0x0b) 竖直制表符（vertical tab）
  \\   (rune值：0x5c) 一个反斜杠（backslash）
  \'   (rune值：0x27) 一个单引号（single quote）
  ```

  rune类型的零值常用 `'\000'`、`'\x00'`或`'\u0000'`等来表示。

* **字符串值的字面量形式**

  在Go中，字符串值是**UTF-8**编码的， 甚至所有的Go源代码都必须是UTF-8编码的。

  这里有一个坑，关于**utf-8编码**。我们来看下面这个例子：

  ```go
  str := "hello 世界"
  fmt.Println(len(str))
  ```

  按照我们的设想，它返回的应该是8，但是实际上我们这么操作会得到12。原因很简单，因为在utf-8编码当中，一个**汉字需要3个字节**编码。那如果我们想要得到字符串本身的长度，而不是字符串占据的字节数，应该怎么办呢？这个时候，我们需要用到一个新的结构叫做rune，它表示单个Unicode字符。

  所以我们可以将string转化成rune数组，之后再来<u>计算长度</u>，得到的结果就准确了。

  ```go
  str := "hello 世界"
  fmt.Println(len([]rune(str)))
  ```

  同样对于<u>截取字符串</u>也可以用

  ```go
  s := "Go语言编程"
  // 转成 rune 数组，需要几个字符，取几个字符
  fmt.Println(string([]rune(s)[:4])) // 输出：Go语言    
  ```

  

  Go字符串的字面量形式有两种。 一种是**解释型字面表示**（interpreted string literal，双引号风格）。 另一种是**直白字面表示**（raw string literal，反引号风格）。 下面的两个字符串表示形式是等价的：

  ```go
  // 解释形式
  "Hello\nworld!\n\"你好世界\""
  
  // 直白形式
  `Hello
  world!
  "你好世界"`
  ```

  

### 类型的显式类型转换

#### 类型不确定值

​	在Go中，有些值的类型是不确定的。换句话说，有些值的类型有很多可能性。 这些值称为类型不确定值。对于**大多数类型不确定值来说，它们各自都有一个默认类型**， 除了预声明的`nil`。`nil`是没有默认类型的。 与类型不确定值相对应的概念称为类型确定值。

一个字面（常）量的默认类型取决于它为何种字面量形式：

- 一个字符串字面量的默认类型是预声明的`string`类型。
- 一个布尔字面量的默认类型是预声明的`bool`类型。
- 一个整数型字面量的默认类型是预声明的`int`类型。
- 一个rune字面量的默认类型是预声明的`rune`（亦即`int32`）类型。
- 一个浮点数字面量的默认类型是预声明的`float64`类型。
- 如果一个字面量含有虚部字面量，则此字面量的默认类型是预声明的`complex128`类型。

#### 显式类型转换

和很多语言一样，Go也支持类型转换。 一个显式类型转换的形式为`T(v)`，其表示将一个值`v`转换为类型`T`。 编译器将`T(v)`的转换结果视为一个类型为`T`的类型确定值。 当然，对于一个特定的类型`T`，`T(v)`并非对任意的值`v`都合法。

1. `v`[可以表示为](https://www.bookstack.cn/read/Golang101-v1.16.a-1/basic-types-and-value-literals.html#representability)`T`类型的一个值。 转换结果为一个类型为`T`的类型确定常量值。

2. `v`的默认类型是一个整数类型（`int`或者`rune`） 并且`T`是一个字符串类型。

   > 对于2：转换`T(v)`将`v`看作是一个Unicode码点。 转换结果为一个类型为`T`的字符串常量。 此字符串常量只包含一个Unicode码点，并且可以看作是此Unicode码点的UTF-8表示形式。 对于不在合法的Unicode码点取值范围内的整数`v`， 转换结果等同于字符串字面量`"\uFFFD"`（亦即`"\xef\xbf\xbd"`）。 `0xFFFD`是Unicode标准中的（非法码点的）替换字符值。 （但是请注意，今后的Go版本可能[只允许rune或者byte整数被转换为字符串](https://github.com/golang/go/issues/3939)。 从Go官方工具链1.15版本开始，`go vet`命令会对从非rune和非byte整数到字符串的转换做出警告。）

事实上，第二种情形并不要求`v`必须是一个常量。 如果`v`是一个常量，则转换结果也是一个常量。 如果`v`不是一个常量，则转换结果也不是一个常量。

一些合法的转换例子：

```go
// 结果为complex128类型的1.0+0.0i。虚部被舍入了。
complex128(1 + -1e-1000i)
// 结果为float32类型的0.5。这里也舍入了。
float32(0.49999999)
// 只要目标类型不是整数类型，舍入都是允许的。
float32(17000000000000000)
float32(123)
uint(1.0)
int8(-123)
int16(6+0i)
complex128(789)
string(65)          // "A"
string('A')         // "A"
string('\u68ee')    // "森"
string(-1)          // "\uFFFD"
string(0xFFFD)      // "\uFFFD"
string(0x2FFFFFFFF) // "\uFFFD"
```

一些非法的转换：

```go
int(1.23)     // 1.23不能被表示为int类型值。
uint8(-1)     // -1不能被表示为uint8类型值。
float64(1+2i) // 1+2i不能被表示为float64类型值。
// -1e+1000不能被表示为float64类型值。不允许溢出。
float64(-1e1000)
// 0x10000000000000000做为int值将溢出。
int(0x10000000000000000)
// 字面量65.0的默认类型是float64（不是一个整数类型）。
string(65.0)
// 66+0i的默认类型是complex128（不是一个整数类型）。
string(66+0i)
```

注意，有时一个显式转换形式必须被写成`(T)(v)`以免发生歧义。 这种情况多发生在`T`不为一个标识符的时候。

### 数据运算符

**五个基本二元算术运算符**：加减乘除取余

```go
+ - * / %
```

**六种位运算符**（也属于算术运算）: 位与 位或 异或 清位 左移位 右移位

```go
& | ^ &^ << >>
```

Go也支持**三个一元算术运算符**：

| 字面形式 | 名称           | 解释                                                         |
| :------- | :------------- | :----------------------------------------------------------- |
| +        | 取正数         | `+n`等价于`0 + n`.                                           |
| -        | 取负数         | `-n`等价于`0 - n`.                                           |
| ^        | 位反（或位补） | `^n`等价于`m ^ n`，其中`m`和`n`同类型并且它的二进制表示中所有比特位均为1。 比如如果`n`的类型为`int8`，则`m`的值为`-1`；如果`n`的类型为`uint8`，则`m`的值为`255`。 |

注意：

- 在很多其它流行语言中，位反运算符是用`~`表示的。
- 和一些其它流行语言一样，加号运算符`+`也可用做字符串衔接运算符（见下）。
- 和C及C++语言一样，`*`除了可以当作乘号运算符，它也可以用做指针解引用运算符； `&`除了可以当作位与运算符，它也可以用做取地址运算符。 后面的[指针](https://www.bookstack.cn/read/Golang101-v1.16.a-1/pointer.html)一文将详解内存地址和指针类型。
- 和Java不一样，Go支持无符号数，所以Go不需要无符号右移运算符`>>>`。
- Go不支持幂运算符， 我们必须使用`math`标准库包中的`Pow`函数来进行幂运算。 下一篇文章将详解[包和包引入](https://www.bookstack.cn/read/Golang101-v1.16.a-1/packages-and-imports.html)。
- 清位运算符`&^`是Go中特有的一个运算符。 `m &^ n`等价于`m & (^n)`。

**`op=`运算符**

对于一个二元算数运算符`op`，语句`x = x op y`可以被简写为`x op= y`。 在这个简写的语句中，`x`只会被估值一次。

```go
var a, b int8 = 3, 5
a += b
println(a) // 8
a *= a
println(a) // 64
a /= b
println(a) // 12
a %= b
println(a) // 2
b <<= uint(a)
println(b) // 20
```

**自增和自减操作符**

和很多其它流行语言一样，Go也支持自增（`++`）和自减（`--`）操作符。 不过和其它语言不一样的是，**自增（`aNumber++`）和自减（`aNumber--`）操作操作没有返回值**， 所以它们不能当做[表达式](https://www.bookstack.cn/read/Golang101-v1.16.a-1/expressions-and-statements.html)来使用。 另一个显著区别是，在Go中，自增（`++`）和自减（`--`）**操作符只能后置，不能前置**。

```go
package main
func main() {
    a, b, c := 12, 1.2, 1+2i
    a++ // ok. <=> a += 1 <=> a = a + 1
    b-- // ok. <=> b -= 1 <=> b = b - 1
    c++ // ok.
    // 下面这些行编译不通过。
    /*
    _ = a++
    _ = b--
    _ = c++
    ++a
    --b
    ++c
    */
}
```

**字符串衔接运算符**

| 字面形式 | 名称       | 对两个操作数的要求                   |
| :------- | :--------- | :----------------------------------- |
| +        | 字符串衔接 | 两个操作数必须为同一类型的字符串值。 |

`+=`运算符也适用于字符串衔接。

```go
println("Go" + "lang") // Golang
var a = "Go"
a += "lang"
println(a) // Golang
```

**布尔（又称逻辑）运算符**

| 字面形式 | 名称           | 对操作值的要求                             |
| :------- | :------------- | :----------------------------------------- |
| &&       | 布尔与（二元） | 两个操作值的类型必须为同一布尔类型。       |
| \|\|     | 布尔或（二元） |                                            |
| !        | 布尔否（一元） | 唯一的一个操作值的类型必须为一个布尔类型。 |

**比较运算符**

```go
== != > >= < <= 
```

1. 对于`==`与`!=`:

* 如果两个操作数都为类型确定的，则它们的类型必须一样，或者其中一个操作数可以隐式转换为另一个操作数的类型。 两者的类型必须都为可比较类型（将在以后的文章中介绍）。
* 如果只有一个操作数是类型确定的，则另一个类型不确定操作数必须可以隐式转换到类型确定操作数的类型。
* 如果两个操作数都是类型不确定的，则它们必须同时为两个类型不确定布尔值、两个类型不确定字符串值或者另个类型不确定数字值。

2. 对于其他`> >= < <=`：两个操作值的类型必须相同并且它们的类型必须为整数类型、浮点数类型或者字符串类型

注意：

> 以后，如果我们说两个值可以比较，我们的意思是说这两个值可以用`==`或者`!=`运算符来比较。 我们将在以后的文章中，我们将了解到某些类型的值是不能比较的。
>
> 注意，并非所有的实数在内存中都可以被精确地表示，所以比较两个浮点数或者复数的结果并不是很可靠。 在编程中，我们常常比较两个浮点数的差值是否小于一个阙值来检查两个浮点数是否相等。

**展开运算符**

在 Go 语言中，展开运算符（...）主要**用于函数的参数传递和数组/切片的操作**。

对于函数的参数传递，展开运算符可以将一个可迭代对象中的元素，逐个传递给函数作为参数。这种方式可以帮助我们简化代码，也提高了代码的灵活性和可读性。例如：

```go
func myFunc(args ...int) {
    for _, arg := range args {
        fmt.Println(arg)
    }
}

myFunc(1, 2, 3) // 输出：1 2 3

mySlice := []int{4, 5, 6}
myFunc(mySlice...) // 输出：4 5 6
```

在这个例子中，我们定义了一个函数 myFunc，它可以接收任意数量的整数类型参数。我们可以使用展开运算符，将一个可迭代对象中的元素逐个传递给 myFunc 函数，分别作为不同的参数进行处理。在第一个示例中，我们以普通的方式传递了三个不同的整数参数。而在第二个示例中，我们用展开运算符将一个整数切片中的元素逐个传递给了 myFunc 函数。

此外，在 Go 语言中，展开运算符还可以用于数组/切片的操作。例如：

```go
a := []int{1, 2, 3}
b := []int{4, 5, 6}
c := []int{7, 8, 9}

d := append(a, b...) // 将 b 中的元素展开后添加到 a 中
fmt.Println(d) // 输出：[1 2 3 4 5 6]

e := append(a[:1], c...) // 将 c 中的元素展开后添加到 a[:1] 中
fmt.Println(e) // 输出：[1 7 8 9]
```

在这个例子中，我们使用了展开运算符，在数组/切片的操作中匀出了极大的力量。我们可以将一个切片中的元素展开放到另一个切片中，也可以通过将切片中的某个片段展开，将其插入到其他的切片中去。

综上所述，展开运算符在 Go 语言中有着非常广泛的应用场景，可以帮助我们简化代码，提高代码的可读性和灵活性。

### 指针

变量是一种使用方便的占位符，用于引用计算机内存地址。

Go 语言的取地址符是 &，放到一个变量前使用就会返回相应变量的内存地址。

引用传递是指在调用函数时将实际参数的地址传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。

例子

```go
/* 定义交换值函数*/
func swap(x *int, y *int) {
   var temp int
   temp = *x    /* 保持 x 地址上的值 */
   *x = *y      /* 将 y 值赋给 x */
   *y = temp    /* 将 temp 值赋给 y */
}
```

除了特殊的数据结构(`slice`或者`map`)外，默认都是值传递。

### 自定义数据类型名称

`type`是一个关键字，用于声明类型的名称

```go
// 一些类型定义声明
type status bool     // status和bool是两个不同的类型
type MyString string // MyString和string是两个不同的类型
type Id uint64       // Id和uint64是两个不同的类型
type real float32    // real和float32是两个不同的类型// 一些类型别名声明
type boolean = bool // boolean和bool表示同一个类型
type Text = string  // Text和string表示同一个类型
type U8 = uint8     // U8、uint8和 byte表示同一个类型
type char = rune    // char、rune和int32表示同一个类型

```



## 变量的定义

### 单变量的定义

方式一：声明一个变量 默认的值是0

```go
var a int
```

方法二：声明一个变量，初始化一个值

```go
var b int = 100
```

方法三：在初始化的时候，可以省去数据类型，通过值自动匹配当前的变量的数据类型

```go
var c = 100
```

方法四：(**常用的方法**:==冒等==) 省去var关键字，直接自动匹配

```go
e := 100
```

### 多变量的定义

方式五：初始化多个变量，写在一行

```go
var xx, yy int = 100, 200
var kk, ll = 100, "Aceld"
```

方式六：初始化多个变量，写在一个小括号内

```go
var (
    vv int  = 100
    jj bool = true
)
```

### 局部变量与全局变量的说明

除了冒等`:=`只能声明函数体内的局部变量外，其余的都是既可以声明局部变量，又能声明全局变量。



## 常量的定义

### 单常量与多常量定义

```go
const a string = "abc"
const b = "abc"
const d, e, c = 1, false, "str" //多重赋值
```

注意，常量可以直接声明在包中，也可以声明在函数体中。 声明在函数体中的常量称为**局部常量**（local constant），直接声明在包中的常量称为**包级常量**（package-level constant）。 包级常量也常常被称为**全局常量**。

### 枚举常量的定义

**手工枚举量定义**：适用于枚举量不多时

```go
const (
    Unknown = 0
    Female = 1
    Male = 2
)
```

或者

```go
const (
    CCVisa            = "Visa"
    CCMasterCard      = "MasterCard"
    CCAmericanExpress = "American Express"
)
```

**自动枚举量定义**：适用于枚举量多且每个枚举量无值的意义的情景

使用`iota`标识符，可以**自增长**

> 1. ==可以在const() 添加一个关键字 iota， 每行的iota都会累加1, 第一行的iota的默认值是0==
>
> 2. `iota`只能与const在一起用

例子1  

```go
const (
	a = iota	 //iota = 0, a = 0
	b 		  //iota = 1, b = 1
	c          //iota = 2, c = 2
)
```

例子2

```go
const (
	a = iota*10	 //iota = 0, a = 0
	b 		  //iota = 1, b = 10
	c          //iota = 2, c = 20
)
```

例子3

```go
const (
	a,d = iota+1,iota+2 //iota = 0, a = iota+1 = 1,d = iota+2 = 2
	b,e 		  //iota = 1, b = 2,e = 3
	c,f          //iota = 2, c = 3,f = 4
    g = iota*3  //iota = 3,g = 9
    h           //iota = 4,h = 12
)
```

 

## 函数的定义



### 简单介绍与注意事项

**简单介绍**

每一个程序都包含很多的函数：函数是基本的代码块。

Go是编译型语言，所以函数编写的顺序是无关紧要的；鉴于可读性的需求，最好把 `main()` 函数写在文件的前面，其他函数按照一定逻辑顺序进行编写（例如函数被调用的顺序）。

当函数执行到代码块最后一行（`}` 之前）或者 `return` 语句的时候会退出，其中 `return` 语句可以带有零个或多个参数；这些参数将作为返回值供调用者使用。简单的 `return` 语句也可以用来结束 for 死循环，或者结束一个协程（goroutine）。

**Go 里面有三种类型的函数**：

- 普通的带有名字的函数
- 匿名函数或者lambda函数
- 方法（Methods）

**除了main()、init()函数外**，其它所有类型的函数都可以有参数与返回值。<u>函数参数、返回值以及它们的类型</u>被统称为**函数签名**。

如果需要申明一个在外部定义的函数，你只需要给出函数名与函数签名，不需要给出函数体：

```go
func flushICache(begin, end uintptr) // implemented externally
```



**注意事项**

1. golang的函数不支持默认参数值

> 这个问题相当麻烦，根据[golang-nuts/google groups](https://groups.google.com/g/golang-nuts/c/-5MCaivW0qQ?pli=1)中的这篇文章，golang现在与将来都不会支持参数默认值。Go始终在使得自己变得尽可能的简单，而增加这种额外的支持会使parser变得更复杂。

2. 函数不能声明在一个函数体内

> 注意，在Go中，所有函数都必须直接声明在包级代码块中。 或者说，任何一个函数都不能被声明在另一个函数体内。 虽然匿名函数（将在下面的某节中介绍）可以定义在函数体内，但匿名函数定义不属于函数声明。

3. 函数的访问权限

> 函数名大写表示该函数为Public可以被其它package调用，小写为private，不可以被其它包调用。

4. 函数的语法注意

> ```go
> //正确的
> func g() {
> }
> //错误的
> func g()
> {
> }
> ```

5. 函数重载是不被允许的。这将导致一个编译错误

```go
funcName redeclared in this book, previous declaration at lineno
```

> Go 语言不支持这项特性的主要原因是函数重载需要进行多余的类型匹配影响性能；没有重载意味着只是一个简单的函数调度。所以你需要给不同的函数使用不同的名字

### 函数的参数

函数定义时，它的形参一般是有名字的，不过我们也可以定义没有形参名的函数，只有相应的形参类型，就像这样：`func f(int, int, float64)`。

没有参数的函数通常被称为 **niladic** 函数（niladic function），就像 `main.main()`。

#### **按值传递 VS 按引用传递**

Go <u>默认使用按值传递来传递参数</u>，也就是传递参数的副本。函数接收参数副本之后，在使用变量的过程中可能对副本的值进行更改，但不会影响到原来的变量，比如 `Function(arg1)`。

如果你希望函数可以直接修改参数的值，而不是对参数的副本进行操作，你需要将参数的地址（变量名前面添加&符号，比如 &variable）传递给函数，这就是按引用传递，比如 `Function(&arg1)`，此时传递给函数的是一个指针。如果传递给函数的是一个指针，指针的值（一个地址）会被复制，但指针的值所指向的地址上的值不会被复制；我们可以通过这个指针的值来修改这个值所指向的地址上的值。（**译者注：指针也是变量类型，有自己的地址和值，通常指针的值指向一个变量的地址。所以，按引用传递也是按值传递。**）

*几乎在任何情况下，传递指针（一个32位或者64位的值）的消耗都比传递副本来得少*。

在函数调用时，像切片（slice）、字典（map）、接口（interface）、通道（channel）这样的引用类型都是默认使用引用传递（即使没有显式的指出指针）。

参考：[函数参数与返回值 · Go入门指南 (studygolang.com)](https://books.studygolang.com/the-way-to-go_ZH_CN/06.2.html)

#### 传递变长参数

如果函数的最后一个参数是采用 `...type` 的形式，那么这个函数就可以处理一个变长的参数，这个长度可以为 0，这样的函数称为变参函数。变长函数声明和普通函数声明类似，只不过最后一个参数必须为变长参数。 **一个变长参数在函数体内将被视为一个切片。**

```go
func myFunc(a, b, arg ...int) {}
```

- 一个函数的最后一个参数可以是一个变长参数；
- 一个函数可以最多有一个变长参数；
- 一个变长参数的类型总为一个切片类型。

例子

```go
// Sum返回所有输入实参的和。
func Sum(values ...int64) (sum int64) {
	// values的类型为[]int64。
	sum = 0
	for _, v := range values {
		sum += v
	}
	return
}

// Concat是一个低效的字符串拼接函数。
func Concat(sep string, tokens ...string) string {
	// tokens的类型为[]string。
	r := ""
	for i, t := range tokens {
		if i != 0 {
			r += sep
		}
		r += t
	}
	return r
}
```

从上面的两个变长参数函数声明可以看出，如果一个变长参数的类型部分为`...T`，则此变长参数的类型实际为`[]T`。

**变长参数类型不同的解决方案**

但是如果变长参数的类型并不是都相同的呢？使用 5 个参数来进行传递并不是很明智的选择，有 2 种方案可以解决这个问题：

1. 使用结构（详见第 10 章）：

   定义一个结构类型，假设它叫 `Options`，用以存储所有可能的参数：

   ```go
    type Options struct {
        par1 type1,
        par2 type2,
        ...
    }
   ```

   函数 F1 可以使用正常的参数 a 和 b，以及一个没有任何初始化的 Options 结构： `F1(a, b, Options {})`。如果需要对选项进行初始化，则可以使用 `F1(a, b, Options {par1:val1, par2:val2})`。

2. 使用空接口：

   如果一个变长参数的类型没有被指定，则可以使用默认的空接口 `interface{}`，这样就可以接受任何类型的参数（详见第 11.9 节）。该方案不仅可以用于长度未知的参数，还可以用于任何不确定类型的参数。一般而言我们会使用一个 for-range 循环以及 switch 结构对每个参数的类型进行判断：

   ```go
    func typecheck(..,..,values … interface{}) {
        for _, value := range values {
            switch v := value.(type) {
                case int: …
                case float: …
                case string: …
                case bool: …
                default: …
            }
        }
    }
   ```

### 函数的返回值

我们通过 `return` 关键字返回一组值。事实上，任何一个有返回值（单个或多个）的函数都必须以 `return` 或 `panic`（参考 [第 13 章](https://books.studygolang.com/the-way-to-go_ZH_CN/13.0.html)）结尾。

在函数块里面，`return` 之后的语句都不会执行。如果一个函数需要返回值，那么这个函数里面的**每一个代码分支（code-path）都要有 `return` 语句**。

> 如果有一个代码分支里没有，那么就会编译失败

1. 单返回值

```go
//单返回值，匿名返回
func  testfunc(a string,b int) int{
    return 1
}
res1 = testfunc("test",1)
```
2. 多返回值，匿名返回

```go
//多返回值，匿名返回
func  testfunc(a string,b int) int,string{
    return 1,"2"
}
res1,res2 = testfunc("test",1)
res1,_ = testfunc("test",1)//匿名接受
```
3. 多返回值，有名形参返回

**尽量使用命名返回值：会使代码更清晰、更简短，同时更加容易读懂。**

```go
//多返回值，有名返回
func  testfunc(a string,b int) (r1 int,r2 string){
    r1 = 1
    r2 = "2"
    return //直接返回r1,r2
}
res1,res2 = testfunc("test",1)
```



### 函数作为数据类型

1. 函数作为一种数据类型，进行声明变量

```go
func  testfunc(a string,b int) (r1 int,r2 string){
    r1 = 1
    r2 = "2"
    return //直接返回r1,r2
}

//方法1
f1:=testfunc

//方法2
var f2 func(string,int) (int,int)=testfunc

```

2. 函数作为一种数据类型，作为函数的形参

```go
//函数作为参数传
func testfunc(f1 func(int,int) int,a1 int) int {
	return f1(a1,a1)+a1
}
```

这里可以采用别名的方式简化：

```go
//取个别名传
type myFunc func(int,int) int

func testfunc(f3 myFunc,a4 int) int {
	return f3(a4,a4)+a4
}
```



### 匿名函数

Go支持匿名函数。定义一个匿名函数和声明一个函数类似，但是一个匿名函数的定义中不包含函数名称部分。 注意匿名函数定义不是一个函数声明。

**一个匿名函数在定义后可以被立即调用**，只用在定义完后**加个小括号**即可直接调用。比如：

```go
package main
func main() {
    // 这个匿名函数没有输入参数，但有两个返回结果。
    x, y := func() (int, int) {
        println("This fucntion has no parameters.")
        return 3, 4
    }() // 一对小括号表示立即调用此函数。不需传递实参。
    
    // 下面这些匿名函数没有返回结果。
    func(a, b int) {
        println("a*a + b*b =", a*a + b*b) // a*a + b*b = 25
    }(x, y) // 立即调用并传递两个实参。
    
    func(x int) {
        // 形参x遮挡了外层声明的变量x。
        println("x*x + y*y =", x*x + y*y) // x*x + y*y = 32
    }(y) // 将实参y传递给形参x。
    
    func() {
        println("x*x + y*y =", x*x + y*y) // x*x + y*y = 25
    }() // 不需传递实参。
}
```

**一个匿名函数可以被赋值给某个函数类型的值**，从而我们不必在定义完此匿名函数后立即调用它，而是可以在以后合适的时候再调用它。

```go
compareNum := func (n int) int {
		return (n - a) 
}//匿名函数的赋值

res := compareNum(guessNum)//匿名函数的使用
```

### 闭包（匿名函数 + 引用的变量）

闭包的定义：

> 闭包（closure）是一个函数以及其捆绑的周边环境状态（lexical environment，词法环境）的引用的组合。换而言之，闭包让开发者可以从内部函数访问外部函数的作用域。

简单来说，**闭包是(匿名)函数与函数引用函数外的变量们构成的集合**

**例子**

```go
package main

import "fmt"

//闭包是(匿名)函数与函数引用函数外的变量们构成的集合

//累加器
func Add() func(int) int {
	var n int = 10
	return func(x int) int {
		n = n + x
		return n
	}
}

//思考:为什么答案不是 11，12，13；而是11，13，16这种累加
func main() {
	f := Add()
	fmt.Println(f(1)) //11
	fmt.Println(f(2)) //13
	fmt.Println(f(3)) //16
}

```



### 内置函数

Go 语言拥有一些不需要进行导入操作就可以使用的内置函数。它们有时可以针对不同的类型进行操作，例如：len、cap 和 append，或必须用于系统级的操作，例如：panic。因此，它们需要直接获得编译器的支持。

以下是一个简单的列表：

| 名称               | 说明                                                         |
| :----------------- | :----------------------------------------------------------- |
| close              | 用于管道通信                                                 |
| len、cap           | len 用于返回某个类型的长度或数量（字符串、数组、切片、map 和管道）；cap 是容量的意思，用于返回某个类型的最大容量（只能用于切片和 map） |
| new、make          | new 和 make 均是用于分配内存：new 用于值类型和用户定义的类型，如自定义结构，make 用于内置引用类型（切片、map 和管道）。它们的用法就像是函数，但是将类型作为参数：new(type)、make(type)。new(T) 分配类型 T 的零值并返回其地址，也就是指向类型 T 的指针（详见第 10.1 节）。它也可以被用于基本类型：`v := new(int)`。make(T) 返回类型 T 的初始化之后的值，因此它比 new 进行更多的工作（详见第 7.2.3/4 节、第 8.1.1 节和第 14.2.1 节）**new() 是一个函数，不要忘记它的括号** |
| copy、append       | 用于复制和连接切片                                           |
| panic、recover     | 两者均用于错误处理机制                                       |
| print、println     | 底层打印函数（详见第 4.2 节），在部署环境中建议使用 fmt 包   |
| complex、real imag | 用于创建和操作复数（详见第 4.5.2.2 节）                      |



### 函数的退出阶段：defer关键字

**defer关键字的作用**

> 关键字 defer 允许我们推迟到函数返回之前（或任意位置执行 `return` 语句之后）一刻才执行某个语句或函数（为什么要在返回之后才执行这些语句？因为 `return` 语句同样可以包含一些操作，而不是单纯地返回某个值）。
>
> 关键字 defer 的用法类似于面向对象编程语言 Java 和 C# 的 `finally` 语句块，它一般用于释放某些已分配的资源。

**多个defer语句的执行顺序**

在函数return之后，才会执行defer关键字后的语句。如果有多个defer语句，会以代码在文档中的顺序把defer语句入**栈**，return后含出栈顺序执行。

![image-20230317150608883](https://gitee.com/yushen611/img/raw/master/image-20230317150608883.png)

使用场景

关键字 defer 允许我们进行一些函数执行完成后的收尾工作，例如：

1. 关闭文件流

```go
// open a file  
defer file.Close()
```

2. 解锁一个加锁的资源 

```go
mu.Lock()  
defer mu.Unlock()
```

3. 打印最终报告

```go
printHeader()  
defer printFooter()
```

4. 关闭数据库链接

```go
// open a database connection  
defer disconnectFromDB()
```

#### 例子1: 理解defer的顺序

**重点：defer 函数定义的顺序 与 实际执的行顺序是相反的，也就是最先声明的最后才执行。**

case1

``` go
func main() {
	defer fmt.Println("The third line.")
	defer fmt.Println("The second line.")
	fmt.Println("The first line.")
}
```

输出：

> The first line.
> The second line.
> The third line.

case2

```go
func main() {
	a := 2
	defer fmt.Println("a:", a)
	a = 1
	fmt.Println("a:", a)

	b := 3
	defer fmt.Println("b:", b)
	b = 2
	fmt.Println("b:", b)
}
```

输出:

```go
a: 1
b: 2
b: 3
a: 2
```

case3:

```go
func main() {
	defer fmt.Println("9")
	fmt.Println("0")
	defer fmt.Println("8")
	fmt.Println("1")
	if false {
		defer fmt.Println("not reachable")
	}
	defer func() {
		defer fmt.Println("7")
		fmt.Println("3")
		defer func() {
			fmt.Println("5")
			fmt.Println("6")
		}()
		fmt.Println("4")
	}()
	fmt.Println("2")
	return
	defer fmt.Println("not reachable")
}
```

输出：

> 0
> 1
> 2
> 3
> 4
> 5
> 6
> 7
> 8
> 9

#### 例子2：理解defer为什么能修改return的返回值

**重点：先return后defer,return的值在defer时可以修改**

```golang
func Triple(n int) (r int) {
	defer func() {
		r += n // 修改返回值
	}()

	return n + n // <=> r = n + n; return
}

func main() {
	fmt.Println(Triple(5)) // 15
}
```

输出

> 15

#### 例子3：理解defer什么时候执行，以及 协程和延迟调用的实参的估值时刻

**case1**
**思考1：为什么是先打印a，后打印b，不是说defer是在return后才调用吗**
实际上，<u>defer是在离自己最近的外层函数return时调用</u>
**思考2：defer作用于一个匿名函数与作用于一个普通函数的区别**
**一个延迟调用的实参是在此延迟调用被推入延迟调用队列时被估值的。 这些被估值的结果将在以后此延迟调用被执行的时候使用**。
**一个匿名函数体内的表达式是在此函数被执行的时候才会被逐渐估值的**，不管此函数是被普通调用还是延迟/协程调用。

```go
func main() {
	func() {
		for i := 0; i < 3; i++ {
            //defer非匿名函数函数
			defer fmt.Println("a:", i)
		}
	}()
	fmt.Println()
	func() {
		for i := 0; i < 3; i++ {
            //defer 匿名函数函数（由于引用了外部变量i，因此这个匿名函数与这个外部变量构成了闭包）
			defer func() {
				fmt.Println("b:", i)
			}()
		}
	}()
}
```

输出：

```go
a: 2
a: 1
a: 0

b: 3
b: 3
b: 3
```

**case2**
对第二个循环进行修改，使之与第一个循环打印一样的

我们可以对第二个循环略加修改（使用两种方法），使得它和第一个循环打印出相同的结果。

**方法1：给匿名函数加一个入参，把外部引用变成内部引用，破坏闭包**

破坏闭包定义：闭包 = 匿名函数 + 引用外部函数的参数；如果没有引用外部参数，那么就称闭包被破坏了

```go
func main() {
	fmt.Println("case2")

	func() {
		for i := 0; i < 3; i++ {
			defer fmt.Println("a:", i)
		}
	}()
	fmt.Println()
	func() {
		//修改部分如下
		for i := 0; i < 3; i++ {
			defer func(i int) {
				// 此i为形参i，非实参循环变量i。
				fmt.Println("b:", i)
			}(i) //把i传入匿名函数，破坏闭包
		}
	}()
}
```

输出：

> a: 2
> a: 1
> a: 0
>
> b: 2
> b: 1
> b: 0

**方法2：不破坏闭包，但每次defer引用变量的地址都不同**

```go
func main() {
	fmt.Println("case2")

	func() {
		for i := 0; i < 3; i++ {
			defer fmt.Println("a:", i)
		}
	}()
	fmt.Println()
	func() {
		//修改部分如下
		for i := 0; i < 3; i++ {
			i := i // 在下面的调用中，左i遮挡了右i。// 外部引用变量转换为内部
			       // <=> var i = i
			defer func() {
				// 此i为上面的左i，非循环变量i。
				fmt.Println("b:", i)
			}()
		}
	}()
}
```

这段代码输出2, 1, 0是因为在循环体内，每次defer函数都捕获了当前循环的i的值，而不是循环变量i本身。

在每次循环迭代中，首先将循环变量i的值赋给了一个新的i变量，而不是直接使用循环变量i。这是因为在defer函数中，捕获的是变量的值而不是变量本身，所以每次defer函数实际上捕获的是新的i变量。

当循环结束时，i的值为3，而每个defer函数中捕获的i的值分别为2, 1, 0。所以最终输出的结果是2, 1, 0。

输出：

> a: 2
> a: 1
> a: 0
>
> b: 2
> b: 1
> b: 0

#### 例子4：理解defer与 panic函数和recover函数的用法

Go不支持异常抛出和捕获，而是推荐使用返回值显式返回错误。 不过，Go支持一套和异常抛出/捕获类似的机制。此机制称为恐慌/恢复（panic/recover）机制。

**我们可以调用内置函数`panic`来产生一个恐慌以使当前协程进入恐慌状况。**

进入恐慌状况是另一种使当前函数调用开始返回的途径。 **一旦一个函数调用产生一个恐慌，此函数调用将立即进入它的退出阶段**。

**通过在一个延迟函数调用之中调用内置函数`recover`，当前协程中的一个恐慌可以被消除，从而使得当前协程重新进入正常状况**。

如果**一个协程在恐慌状况下退出，它将使整个程序崩溃。**



内置函数`panic`和`recover`的声明原型如下：

```go
func panic(v interface{})
func recover() interface{}
```

接口（interface）类型和接口值将在以后的文章[接口](https://gfw.go101.org/article/interface.html)中详解。 目前，我们可以暂时将空接口类型`interface{}`视为很多其它语言中的`any`或者`Object`类型。 换句话说，在一个`panic`函数调用中，我们可以传任何实参值。



**case:如何产生一个恐慌和如何消除一个恐慌**

**重点：一个recover函数的返回值为其所恢复的恐慌在产生时被一个panic函数调用所消费的参数。(有种MQ的感觉)**

```go
package main

import "fmt"

func main() {
	defer func() {
		fmt.Println("正常退出")
	}()
	fmt.Println("嗨！")
	defer func() {
		v := recover()
		fmt.Println("恐慌被恢复了：", v)
	}()
	panic("拜拜！") // 产生一个恐慌
	fmt.Println("执行不到这里")
}
```

输出：

> ```
> 嗨！
> 恐慌被恢复了： 拜拜！
> 正常退出
> ```

一般说来，**恐慌用来表示正常情况下不应该发生的逻辑错误**。 如果这样的一个错误在运行时刻发生了，则它肯定是由于某个bug引起的。
**另一方面，非逻辑错误是现实中难以避免的错误，它们不应该导致恐慌**。 我们**必须正确地对待和处理非逻辑错误**。



## 基本流程控制语法

Go语言中有**三种基本的流程控制代码块**：

- `if-else`条件分支代码块；
- `for`循环代码块；
- `switch-case`多条件分支代码块。

Go中另外**还有几种和特定种类的类型相关的流程控制代码块**：

- [容器类型](https://www.bookstack.cn/read/Golang101-v1.16.a-1/container.html#iteration)相关的`for-range`循环代码块。
- [接口类型](https://www.bookstack.cn/read/Golang101-v1.16.a-1/interface.html#type-switch)相关的`type-switch`多条件分支代码块。
- [通道类型](https://www.bookstack.cn/read/Golang101-v1.16.a-1/channel.html#select)相关的`select-case`多分支代码块。

和很多其它流行语言一样，Go也支持`break`、`continue`和`goto`等跳转语句。 另外，Go还支持一个特有的`fallthrough`跳转语句。

Go所支持的六种流程控制代码块中，除了`if-else`条件分支代码块，其它五种称为可跳出代码块。 我们可以在一个可跳出代码块中使用`break`语句以跳出此代码块。

我们可以在`for`和`for-range`两种循环代码块中使用`continue`语句提前结束一个循环步。 除了这两种循环代码块，其它四种代码块称为分支代码块。

请注意，上面所提及的每种流程控制的一个分支都属于一条语句。这样的语句常常会包含很多子语句。

上面所提及的流程控制语句都属于狭义上的流程控制语句。 下一篇文章中将要介绍的[协程、延迟函数调用、以及恐慌和恢复](https://www.bookstack.cn/read/Golang101-v1.16.a-1/control-flows-more.html)，以及今后要介绍的[并发同步技术](https://www.bookstack.cn/read/Golang101-v1.16.a-1/concurrent-synchronization-overview.html)属于广义上的流程控制语句。

本文余下的部分将只解释三种基本的流程控制语句和各种代码跳转语句。其它上面提及的语句将在后面其它文章中逐渐介绍。

### `if-else`条件分支控制代码块

```go
if InitSimpleStatement; Condition {
    // do something
} else {
    // do something
}
```

在一个`if-else`条件分支控制代码块中，

- `InitSimpleStatement`部分是可选的，如果它没被省略掉，则它必须为一条[简单语句](https://www.bookstack.cn/read/Golang101-v1.16.a-1/expressions-and-statements.html#simple-statements)。 如果它被省略掉，它可以被视为一条空语句（简单语句的一种）。 在实际编程中，`InitSimpleStatement`常常为一条变量短声明语句。
- `Condition`必须为一个结果为布尔值的[表达式](https://www.bookstack.cn/read/Golang101-v1.16.a-1/expressions-and-statements.html#expressions)（它被称为条件表达式）。 `Condition`部分可以用一对小括号括起来，但大多数情况下不需要。

注意，我们**不能用一对小括号**将`InitSimpleStatement`和`Condition`两部分括在一起。

在执行一个`if-else`条件分支控制代码块中，如果`InitSimpleStatement`这条语句没有被省略，则此条语句将被率先执行。 如果`InitSimpleStatement`被省略掉，则其后跟随的分号`;`也可一块儿被省略。

例子

```go
//使用InitSimpleStatement
if n := rand.Int(); n%2 == 0 {
    fmt.Println(n, "是一个偶数。")
} else {
    fmt.Println(n, "是一个奇数。")
}
//不使用InitSimpleStatement
n := rand.Int() % 2 // 此n不是上面声明的n
if n % 2 == 0 {
    fmt.Println("一个偶数。")
}
if ; n % 2 != 0 {
    fmt.Println("一个奇数。")
}

if condition {
    // do something
}else if condition1 {
    // do something1
}else if condition2 {
    // do something2
}
```

### `for`循环代码块

```go
for InitSimpleStatement; Condition; PostSimpleStatement {
    // do something
}
```

在一个`for`循环代码块中，

- `InitSimpleStatement`（初始化语句）和`PostSimpleStatement`（步尾语句）两个部分必须均为简单语句，并且`PostSimpleStatement`不能为一个变量短声明语句。
- `Condition`必须为一个结果为布尔值的表达式（它被称为条件表达式）。

**这三个部分都是可选的**。和很多其它流行语言不同，在Go中上述三部分**不能用小括号括在一起**。

```go
for i := 0; i < 10; i++ {
    fmt.Println(i)
}
```

**只有条件表达式的for循环：用于代替while循环**

在一个`for`循环流程控制中，如果`InitSimpleStatement`和`PostSimpleStatement`两部分同时被省略（可将它们视为空语句），则和它们相邻的两个**分号也可被省略**。 这样的形式被称为只有条件表达式的`for`循环。只有条件表达式的`for`循环和很多其它语言中的`while`循环类似。

```go
var i = 0
//可以省略分号，也可以不省略
for ; i < 10; {
    fmt.Println(i)
    i++
}
//这样就等于while循环了
for i < 20 {
    fmt.Println(i)
    i++
}
```



### `switch-case`流程控制代码块

```go
switch InitSimpleStatement; CompareOperand0 {
case CompareOperandList1:
    // do something
case CompareOperandList2:
    // do something
...
case CompareOperandListN:
    // do something
default:
    // do something
}
```

在一个`switch-case`流程控制代码块中，

- `InitSimpleStatement`部分必须为一条简单语句，它是可选的。
- `CompareOperand0`部分必须为一个表达式（如果它没被省略的话，见下）。 此表达式的估值结果总是被视为一个类型确定值。如果它是一个类型不确定值，则它被视为类型为它的默认类型的类型确定值。 因为这个原因，此表达式不能为类型不确定的`nil`值。 `CompareOperand0`常被称为switch表达式。
- 每个`CompareOperandListX`部分（`X`表示`1`到`N`）必须为一个用（英文）逗号分隔开来的表达式列表。 其中每个表达式都必须能和`CompareOperand0`表达式进行比较。 每个这样的表达式常被称为case表达式。 如果其中case表达式是一个类型不确定值，则它必须能够自动隐式转化为对应的switch表达式的类型，否则编译将失败。

注意：

1. 每个`switch-case`流程控制代码块中最多只能有一个`default`分支（默认分支）。

2. `switch-case`代码块属于可跳出流程控制。 `break`可以使用在一个`switch-case`流程控制的任何分支代码块之中以提前跳出此`switch-case`流程控制。

3. 当一个`switch-case`流程控制被执行到的时候，其中的简单语句`InitSimpleStatement`将率先被执行。 随后switch表达式`CompareOperand0`将被估值（仅一次）。上面已经提到，此估值结果一定为一个类型确定值。 然后此结果值将从上到下从左到右和各个`CompareOperandListX`表达式列表中的各个case表达式逐个依次比较（使用`==`运算符）。 一旦发现某个表达式和`CompareOperand0`相等，比较过程停止并且此表达式对应的分支代码块将得到执行。 如果没有任何一个表达式和`CompareOperand0`相等，则`default`默认分支将得到执行（如果此分支存在的话）。
4. Go中另外一个和其它语言的显著不同点是`default`分支不必一定为最后一个分支

一个`switch-case`流程控制的例子：

**和很多其它语言不一样，程序不会自动从一个分支代码块跳到下一个分支代码块去执行**。

```go
rand.Seed(time.Now().UnixNano())
switch n := rand.Intn(100); n%9 {
    case 0:
    fmt.Println(n, "is a multiple of 9.")
    // 和很多其它语言不一样，程序不会自动从一个
    // 分支代码块跳到下一个分支代码块去执行。
    // 所以，这里不需要一个break语句。
    case 1, 2, 3:
    fmt.Println(n, "mod 9 is 1, 2 or 3.")
    break // 这里的break语句可有可无的，效果
    // 是一样的。执行不会跳到下一个分支。
    case 4, 5, 6:
    fmt.Println(n, "mod 9 is 4, 5 or 6.")
    // case 6, 7, 8:
    // 上一行可能编译不过，因为6和上一个case中的
    // 6重复了。是否能编译通过取决于具体编译器实现。
    default:
    fmt.Println(n, "mod 9 is 7 or 8.")
}
```

如何让执行从一个`case`分支代码块的结尾跳入下一个分支代码块？Go提供了一个`fallthrough`关键字来完成这个任务。

在下面的例子中，所有的分支代码块都将得到执行（从上到下）。

```go
rand.Seed(time.Now().UnixNano())
switch n := rand.Intn(100) % 5; n {
case 0, 1, 2, 3, 4:
    fmt.Println("n =", n)
    fallthrough // 跳到下个代码块
case 5, 6, 7, 8:
    // 一个新声明的n，它只在当前分支代码快内可见。
    n := 99
    fmt.Println("n =", n) // 99
    fallthrough
default:
    // 下一行中的n和第一个分支中的n是同一个变量。
    // 它们均为switch表达式"n"。
    fmt.Println("n =", n)
}
```

注意：

- 一条`fallthrough`语句必须为一个分支代码块中的最后一条语句。
- 一条`fallthrough`语句不能出现在一个`switch-case`流程控制中的最后一个分支代码块中。





# 三、数据结构与算法

## 包引入与包管理

### 包引入

#### 包引入的方式

不同的引入语法

```go
//单条引入
import "fmt"
import "math/rand"

// 一条包引入语句引入了三个代码包。
import (
    "fmt"
    "math/rand"
    "time"
)
```

事实上，一个引入声明语句的完整形式为：

```go
import importAliasName "path/.../package"
```

注：如果不是内置的包，而是**项目自定义的包**(*自定义的包又叫做模块，模块（module）为的若干代码包的集合*)，path为当前项目的根目录

> 注意，如果项目使用了**go mod进行依赖管理**，那么此时path还是根目录，但是**根目录名称为**go mod init anyName.com中的**anyName.com**。导包时如

**取别名**的例子

```go
import rand "math/rand" // <=> import "math/rand"
```

**如果想调用包的函数而不写包名**：

```go
import (
    . "fmt"
    . "time"
)
```

如果只想执行某个包的init函数而不调用该包的其他变量或方法，可以**匿名引入**：

```go
import (
    _ "math/rand" // okay: 匿名引入
)
```





#### 包引入时的执行顺序：包的init函数

由于包引入时，会涉及到引入过程在干什么，因此需要重点了解一下包的init函数。

**初始化顺序**

golang程序初始化先于main函数执行，由runtime进行初始化，初始化顺序如下：

1. 初始化导入的包(导入的每个包也按如下顺序执行。参考下图)
2. 初始化包作用域的变量
3. 执行包的init函数；

![image-20230317155426309](https://gitee.com/yushen611/img/raw/master/image-20230317155426309.png)

**init函数的主要作用：**

- 初始化不能采用初始化表达式初始化的变量。

- 程序运行前的注册。

- 实现sync.Once功能。

  

**init函数的主要特点：**

- init函数先于main函数自动执行，不能被其他函数调用；
- **init函数没有输入参数、返回值**；
- 每个包可以有多个init函数；
- **包的每个源文件也可以有多个init函数**，这点比较特殊；
- 同一个包的init执行顺序，golang没有明确定义，编程时要注意程序不要依赖这个执行顺序。(同一个包不同源文件的init函数执行顺序，golang spec没做说明，根据实验来看，执行顺序是源文件名称的字典序。
- 不同包的init函数按照包导入的依赖关系决定执行顺序。

一个文件多个init函数的例子

```go
package main
import "fmt"
func init() {
   fmt.Println("init 1")//输出顺序1
}
func init() {
   fmt.Println("init 2")//输出顺序2
}
func main() {
   fmt.Println("main")//输出顺序3
}
```



### 包管理

#### 老的包管理：GOPATH

几乎所有的包管理工具在Go 1.11版本之前都绕不开GOPATH这个环境变量。GOPATH主要用来放置项目依赖包的源代码，GOPATH不区分项目，代码中任何import的路径均从GOPATH为根目录开始；

**GOPATH目录**下一共包含了三个子目录，分别是：

- bin：存储所编译生成的二进制文件。
- pkg：存储预编译的目标文件，以加快程序的后续编译速度。
- src：存储所有`.go`文件或源代码。在编写 Go 应用程序，程序包和库时，一般会以`$GOPATH/src/github.com/foo/bar`的路径进行存放。

```bash
go
├── bin
├── pkg
└── src
    ├── github.com
    ├── golang.org
    ├── google.golang.org
    ├── gopkg.in
    ....
```

因此在使用 GOPATH 模式下，我们需要将应用代码存放在固定的`$GOPATH/src`目录下，并且如果执行`go get`来拉取外部依赖会自动下载并安装到`$GOPATH/src/`目录下。



但现在GOPATH已经不够用了。

**缺点**：

* 不区分依赖项版本
* 依赖项列表无法数据化



#### 新的包管理：Go Modules

我们接下来用Go Modules的方式创建一个项目, 建议为了与GOPATH分开,不要将项目创建在`GOPATH/src`下

##### go mod命令

| 命令            | 作用                             |
| --------------- | -------------------------------- |
| **go mod init** | 生成 go.mod 文件                 |
| go mod download | 下载 go.mod 文件中指明的所有依赖 |
| **go mod tidy** | 整理现有的依赖                   |
| go mod graph    | 查看现有的依赖结构               |
| go mod edit     | 编辑 go.mod 文件                 |
| go mod vendor   | 导出项目所有的依赖到vendor目录   |
| go mod verify   | 校验一个模块是否被篡改过         |
| go mod why      | 查看为什么需要依赖某模块         |

**使用go mod管理项目的流程**

**(1)设置环境变量GO111MODULE为开**(如果已经设置，可忽略)

` $ go env -w GO111MODULE=on`

**(2)进入项目根目录，生成go.mod文件**

`go mod init <域名:如ywy.com>/<项目名:如test_module>`

注：包名的规范一般就是`自己的域名`与`项目/功能名`的组合

此时就会生成一个go.mod文件

```go
module ywy.com/test_module

go 1.20
```

**(3)导入自定义项目包**

假设项目结构如下

![image-20230318143813507](https://gitee.com/yushen611/img/raw/master/image-20230318143813507.png)

我想调用自己写的 ToolKit.go。ToolKit.go大致内容如今

```go
//for all kind of tools
package Toolkit

import (
	"net/http"
	"strings"
)


func Left(str string, cnt int) string {
	l := strings.Count(str, "")
	if cnt >= l {
		cnt = l - 1
	} else if l < 0 {
		l = 0
	}
	return str[0:cnt]
}
...

```

如果我想在main.go中调用Toolkit包，只有一种写法是正确的：

```go
package main

import (
	"testing"
	"ywy.com/test_module/ToolKit"
)

```

**(4)导入外部第三方包**

在[Go Packages](https://pkg.go.dev/)网站可以查看所有的第三方包

使用 `go get`下载包,如下载modbus包

```go
go get github.com/goburrow/modbus
```

在项目中执行go get命令可以下载依赖包，并且还可以指定下载的版本。

*   运行go get -u将会升级到最新的次要版本或者修订版本(x.y.z, z是修订版本号， y是次要版本号)
*   运行go get -u=patch将会升级到最新的修订版本
*   运行go get package[@version](https://github.com/version "@version")将会升级到指定的版本号version
    如果下载所有依赖可以使用go mod download命令。



> 使用go get获取的包.如果GO111MODULE 如果为off，则放在 $GOPATH/src/$ 目录下。如果GO111MODULE 如果为on，则放在 $GOPATH/pkg/$ 目录下。
>
> 使用go mod下载的依赖包放在 $GOPATH/pkg/mod/ 目录下，所有项目共享

**(5)修改版本，更新依赖**

修改包的版本号直接去go.mod文件修改,然后`go mod download`

此时会将依赖全部下载至 GOPATH 下的pkg/mod文件夹中

**(6)如果想把项目依赖转移到本地项目文件夹**

```go
go mod vendor
```

执行此命令,会将刚才下载至 GOPATH 下的依赖转移至该项目根目录下的 vendor(自动新建) 文件夹下，此时我们就可以使用这些依赖了。然而实际不导入也是完全ok的。导入了反而更麻烦。

在协作中使用 GOMODULE时要注意的是, 在项目管理中,如使用git,请将 vendor 文件夹放入白名单,不然项目中带上包体积会很大。

git设置白名单方式为在git托管的项目根目录新建 .gitignore 文件

设置忽略即可。但是 go.mod 和 go.sum 不要忽略，另一人clone项目后在本地进行依赖更新(同上方依赖更新)即可。

**补充1：使用go mod命令下载一个外部依赖步骤**

1. 在你的Go项目中初始化一个新的模块：

```bash
go mod init [module name]
```

1. 执行以下命令来下载依赖项并更新go.mod文件：

```bash
go get [module path]
```

其中，[module path]是你想要下载的依赖项的路径。

例如，如果你想下载github.com/gorilla/mux这个包，可以执行以下命令：

```
go get github.com/gorilla/mux
```

**执行完这个命令后，go.mod文件会被更新，同时下载的依赖项会被放置在你的GOPATH或者go module缓存中**。你可以通过运行以下命令来查看所有已经下载的依赖项：

```
go list -m all
```

这将会列出你当前项目所依赖的所有模块。

需要注意的是，如果你使用的是Go 1.16或更高版本，go get命令将自动使用go mod来管理依赖项。否则，你需要在执行go get命令前设置环境变量GO111MODULE为on来启用go mod模式。

## 字符串

#### 遍历

```go
for _,ch := range(s){
    num := ch - 'a'
    hashmap[num]++
}
```

#### 遍历中拼接字符串需要强转

```go
for _,ch := range(letters) {
    newpath := path + string(ch) //ch需要强转成string
    dfs(digits,newpath,res)
}
```



#### 截取

```go
string[start:end]
```

取头不取尾

## 切片

**容器**, 它是可以包含大量条目（item）的数据结构, 例如数组、切片和 map。从这看到 Go 明显受到 Python 的影响。有时候，我们可以认为字符串类型和通道类型也属于容器类型。 但是，此篇文章只谈及数组、切片和映射类型。

go中，所谓数组，就是静态数组。所谓切片，就是动态数组。

**由于不同容量的数组在go中被认为是不同的数据类型**，因此基本**不使用静态数组，而是直接使用切片**。



### 定义与初始化:make()

**声明切片的格式**是： 

```go
var identifier []type
```

切片不需要说明长度。

区分切片和数组的创建

```go
// 创建有 3 个元素的整型数组
myArray := [3]int{10, 20, 30}

// 创建长度和容量都是 3 的整型切片
mySlice := []int{10, 20, 30}
```



**切片初始化**

一个切片在未初始化之前默认为 nil，长度为 0。

```go
//1. 直接初始化切片，[]表示是切片类型，{1,2,3}初始化值依次是1,2,3.其cap=len=3
s :=[] int {1,2,3 }
q := []*TreeNode{root} //初始化了一个包含单个节点 root 的指向 TreeNode 指针类型的切片

//2. 初始化切片s,是数组arr的引用
var arr []int
s := arr[:]
s := arr[startIndex:endIndex] //将arr中从下标startIndex到endIndex-1 下的元素创建为一个新的切片
s := arr[startIndex:] //缺省endIndex时将表示一直到arr的最后一个元素
s := arr[:endIndex] //缺省startIndex时将表示从arr的第一个元素开始
s1 := s[startIndex:endIndex]//通过切片s初始化切片s1

//3. 通过内置函数make()初始化切片s,也可以指定容量，len 是数组的长度并且也是切片的初始长度。其中capacity为可选参数。
s :=make([]int,len,cap)
//make只指定长度
var slice1 []type = make([]type, len)
slice1 := make([]type, len) //type = int , len = 3时，slice1:[0,0,0]
//make还指定容量
make([]T, length, capacity)//type = int , len = 3,capacity = 5时，slice1:[0,0,0, , ]


```

二维初始化：

```go
//静态二维数组的初始化
var a [3][4]int //这里只能用常量

//动态二维切片的初始化 初始化一个n行m列的二维int切片
matrix := make([][]int, n)
for i := range matrix {
    matrix[i] = make([]int, m)
}

//二维初始化
var matrix [][]int
ret := [][]int{}

```



### 切片截取:slice[:]

和python的截取一样

```go
/* 创建切片 */
numbers := []int{0,1,2,3,4,5,6,7,8}   
/* 打印子切片从索引1(包含) 到索引4(不包含)*/
fmt.Println("numbers[1:4] ==", numbers[1:4])

/* 默认下限为 0*/
fmt.Println("numbers[:3] ==", numbers[:3])

/* 默认上限为 len(s)*/
fmt.Println("numbers[4:] ==", numbers[4:])


```

### 长度与容量：len() 和 cap() 函数

切片是可索引的，并且可以由 len() 方法获取长度。

切片提供了计算容量的方法 cap() 可以测量切片最长可以达到多少。

### 添加与拷贝：append() 和 copy() 函数

**apeend()**

切⽚的扩容机制，append的时候，如果⻓度增加后超过容量，则将容量增加2倍

```go
var numbers []int

/* 向切片添加一个元素 */
numbers = append(numbers, 1)

/* 同时添加多个元素 */
numbers = append(numbers, 2,3,4)

// 两个切片合并
num1 := []int{1, 2}
num2 := []int{3, 4}
numbers = append(num1, num2...) //...是展开运算符
```

**copy()**

```go
/* 拷贝 numbers 的内容到 numbers1 */
copy(numbers1,numbers)

//更推荐这种方式
levelq = []int{}//清空后
levelq = append(levelq, childq...) //再添加
```



### 传参:(slice []int)

动态数组在传参上是**引⽤传递(修改传递的值)**，⽽且不同元素⻓度的动态数组他们的形参是⼀致。

```go
myNum := make([]int, 1e6)
// 将 myNum 传递到函数 foo()
slice = foo(myNum)
// 函数 foo() 接收一个整型切片，并返回这个切片
func foo(slice []int) []int {
...
return slice
}
```

**接收切片的指针**,**复制slice副本(path)**，**给slice指针添加元素**

```go
//接收切片的指针,复制slice副本
func dfs(n int,k int,curNum int,path []int,res * [][] int){
    if(len(path)==k){
        
        copypath := []int{}
        copypath = append(copypath,path...)//go语言 path都是引用传参，需要拷贝一个副本再复制，拷贝的方法推荐用append
        *res = append(*res,copypath) // res是指针，需要用*res来调用
        return
    }
    for i:=curNum;i<=n;i++ {
        path = append(path,i)
        dfs(n,k,i+1,path,res)
        path = path[:len(path)-1]
    }

}
func combine(n int, k int) [][]int {
    var path [] int
    var res [][] int
    dfs(n,k,1,path,&res) //&取指针
    return res
}
```



### 遍历

```go
for ix, value := range slice1 {
    ...
}
```

### slice去重

在Go语言中，可以使用 map 实现 slice 去重。

1. 创建一个空的`map`，用于存放去重后的元素。
2. 遍历`slice`中的元素，将元素作为`map`的键，值为`true`（可以是任意值，只要占用空间小）。
3. 将`map`中的键转换为`slice`返回。

```java
package main

import "fmt"

func main() {
    nums := []int{1, 2, 3, 2, 4, 5, 4}
    
    result := make([]int, 0)
    temp := make(map[int]bool)
    for _, v := range nums {
        if !temp[v] {
            result = append(result, v)
            temp[v] = true
        }
    }

    fmt.Println(result) // [1 2 3 4 5]
}
```

注意，在上面的代码中，value 值使用了空结构体 struct{}{}，而不是 nil。这是因为在 Go 语言中，map 中的 key 才是重要的，而 value 是无关紧要的。如果直接使用 nil 作为 value，会占用不必要的空间。因此，使用一个空结构体作为 value，既能够实现去重，又能够避免占用不必要的空间。

`if !temp[v]` 表示如果 `temp` 列表中的索引 `v` 对应的值为 `false`，则执行条件语句。

### 排序

go分别提供了sort.Float64s() sort.Strings() sort.Ints() 对不同类型的数组进行排序

```go
sort.Ints(nums)
```







## 映射

map就是存储key,value键值对的数据结构

### 声明方式:make()

```go
//第一种声明
var test1 map[string]string
//在使用map前，需要先make，make的作用就是给map分配数据空间
test1 = make(map[string]string, 10) 
test1["one"] = "php"
test1["two"] = "golang"
test1["three"] = "java"
fmt.Println(test1) //map[two:golang three:java one:php]


//第二种声明
test2 := make(map[string]string)
test2["one"] = "php"
test2["two"] = "golang"
test2["three"] = "java"
fmt.Println(test2) //map[one:php two:golang three:java]

//第三种声明
test3 := map[string]string{
    "one" : "php",
    "two" : "golang",
    "three" : "java",
}
fmt.Println(test3) //map[one:php two:golang three:java]
```

嵌套声明的举例

```go
//例子1
language := make(map[string]map[string]string)
language["php"] = make(map[string]string, 2)
language["php"]["id"] = "1"
language["php"]["desc"] = "php是世界上最美的语言"
language["golang"] = make(map[string]string, 2)
language["golang"]["id"] = "2"
language["golang"]["desc"] = "golang抗并发非常good"
fmt.Println(language) //map[php:map[id:1 desc:php是世界上最美的语言] golang:map[id:2 desc:golang抗并发非常good]]

//例子2
mp1 := make(map[int][]int)
mp2 := make(map[int]*[]int)
```

### 增加与修改

```go
m[k] = e
```

### 删除

`delete(m, k)`

```go
delete(language, "php")  //删除了php子元素
```

### 查找

```go
val, ok := language["php"]  //查找是否有php这个子元素
if ok {
     fmt.Printf("%v", val)
} else {
     fmt.Printf("no");
}

ok := language["php"] //也可以只返回一个ok
```

### 遍历

```go
for key, value := range map1 {
    ...
}
```

### 排序

```go
barVal = map[string]int{"alpha": 34, "bravo": 56, "charlie": 23,
                        "delta": 87, "echo": 56, "foxtrot": 12,
                        "golf": 34, "hotel": 16, "indio": 87,
                        "juliet": 65, "kili": 43, "lima": 98}
fmt.Println("unsorted:")
for k, v := range barVal {
    fmt.Printf("Key: %v, Value: %v / ", k, v)
}
keys := make([]string, len(barVal))
i := 0
for k, _ := range barVal {
    keys[i] = k
    i++
}
sort.Strings(keys)
fmt.Println()
fmt.Println("sorted:")
for _, k := range keys {
    fmt.Printf("Key: %v, Value: %v / ", k, barVal[k])
}
```



### 键值对反转

```go
var (
    barVal = map[string]int{"alpha": 34, "bravo": 56, "charlie": 23,
                            "delta": 87, "echo": 56, "foxtrot": 12,
                            "golf": 34, "hotel": 16, "indio": 87,
                            "juliet": 65, "kili": 43, "lima": 98}
)

func main() {
    invMap := make(map[int]string, len(barVal))
    for k, v := range barVal {
        invMap[v] = k
    }
    fmt.Println("inverted:")
    for k, v := range invMap {
        fmt.Printf("Key: %v, Value: %v / ", k, v)
    }
}
```



## 结构体及其方法-面向对象的特征

### 封装（struct）

类名、属性名、⽅法名 ⾸字⺟⼤写表示对外(其他包)可以访问，否则只能够在本包内访问

#### 属性的定义

```go
//如果类名首字母大写，表示其他包也能够访问
type Hero struct {
	//如果说类的属性首字母大写, 表示该属性是对外能够访问的，否则的话只能够类的内部访问
	Name  string
	Ad    int
	level int
}
```

#### 方法的定义

需要加`*`

```go
/* 不加*是错误写法，因为是值传递，所以无法修改成功
func (this Hero) SetName(newName string) {
	//this 是调用该方法的对象的一个副本（拷贝）
	this.Name = newName
}
*/
func (this *Hero) Show() {
	fmt.Println("Name = ", this.Name)
	fmt.Println("Ad = ", this.Ad)
	fmt.Println("Level = ", this.level)
}

func (this *Hero) GetName() string {
	return this.Name
}

func (this *Hero) SetName(newName string) {
	//this 是调用该方法的对象的一个副本（拷贝）
	this.Name = newName
}
```

#### 对象实例化

大括号的形式进行实例化

```go
//创建一个对象
hero := Hero{Name: "zhang3", Ad: 100}
```

### 继承（struct）

#### 父类的定义

```go
type Human struct {
	name string
	sex  string
}

func (this *Human) Eat() {
	fmt.Println("Human.Eat()...")
}

func (this *Human) Walk() {
	fmt.Println("Human.Walk()...")
}

```

#### 子类的继承

```go
type SuperMan struct {
	Human //SuperMan类继承了Human类的方法

	level int
}

//重定义父类的方法Eat()
func (this *SuperMan) Eat() {
	fmt.Println("SuperMan.Eat()...")
}

//子类的新方法
func (this *SuperMan) Fly() {
	fmt.Println("SuperMan.Fly()...")
}
```

#### 子类的实例化

```go
//定义一个子类对象
s := SuperMan{Human{"li4", "female"}, 88}
//var s SuperMan
//s.name = "li4"
//s.sex = "male"
//s.level = 88

s.Walk() //父类的方法
s.Eat()  //子类的方法
s.Fly()  //子类的方法
```



### 多态(interface+struct)

#### 多态的三要素

* 有⼀个⽗类(有接⼝)；
* 有⼦类(实现了⽗类的全部接⼝⽅法)；
* ⽗类类型的变量(指针) 指向(引⽤) ⼦类的具体数据变量

**(1)有⼀个⽗类(有接⼝)**

接口定义了待实现的方法

```go
//本质是一个指针
type AnimalIF interface {
	Sleep()
	GetColor() string //获取动物的颜色
	GetType() string  //获取动物的种类
}

```

**(2)有⼦类(实现了⽗类的全部接⼝⽅法)**

只要子类实现了某接口的全部方法，就是该接口的子类

子类1

```go
//具体的类
type Cat struct {
	color string //猫的颜色
}

func (this *Cat) Sleep() {
	fmt.Println("Cat is Sleep")
}

func (this *Cat) GetColor() string {
	return this.color
}

func (this *Cat) GetType() string {
	return "Cat"
}
```

子类2

```go
//具体的类
type Dog struct {
	color string
}

func (this *Dog) Sleep() {
	fmt.Println("Dog is Sleep")
}

func (this *Dog) GetColor() string {
	return this.color
}

func (this *Dog) GetType() string {
	return "Dog"
}

func showAnimal(animal AnimalIF) {
	animal.Sleep() //多态
	fmt.Println("color = ", animal.GetColor())
	fmt.Println("kind = ", animal.GetType())
}
```

**(3)⽗类类型的变量(指针) 指向(引⽤) ⼦类的具体数据变量**

利用了多态性质的函数

```go
func showAnimal(animal AnimalIF) {
	animal.Sleep() //多态
	fmt.Println("color = ", animal.GetColor())
	fmt.Println("kind = ", animal.GetType())
}
```

调用该函数

```go
var animal AnimalIF //接口的数据类型， 父类指针
animal = &Cat{"Green"}
animal.Sleep() //调用的就是Cat的Sleep()方法 , 多态的现象

animal = &Dog{"Yellow"}
animal.Sleep() // 调用Dog的Sleep方法，多态的现象

cat := Cat{"Green"}
dog := Dog{"Yellow"}

showAnimal(&cat)
showAnimal(&dog)
```

## 万能通用类interface{}：多态的最好体现

### interface{}类

类似python ,java里的 object类

```go
//interface{}是万能数据类型
func myFunc(arg interface{}) {
	fmt.Println("myFunc is called...")
	fmt.Println(arg)

	//interface{} 改如何区分 此时引用的底层数据类型到底是什么？

	//给 interface{} 提供 “类型断言” 的机制
	value, ok := arg.(string)
	if !ok {
		fmt.Println("arg is not string type")
	} else {
		fmt.Println("arg is string type, value = ", value)

		fmt.Printf("value type is %T\n", value)
	}
}

type Book struct {
	auth string
}

func main() {
	book := Book{"Golang"}
	myFunc(book)
	myFunc(100)
	myFunc("abc")
	myFunc(3.14)
}

```

### 类型断言机制

写法举例：`value, ok := a.(string)`

如果断言失败，那么ok的值将会是false,但是如果断言成功ok的值将会是true,同时value将会得到所期待的正确的值。示例：

```go
value, ok := a.(string)
if !ok {
    fmt.Println("It's not ok for type string")
    return
}
fmt.Println("The value is ", value)
```

## LC常用代码

#### 比较大小

1. **自带的math.Max与math.Min只能接受浮点数**

Go语言中的math包提供了一些数学函数，其中包括Min和Max函数，可以方便地求两个数的最小值和最大值。

Min函数用于求两个数的最小值，其定义如下：

```go
func Min(x, y float64) float64
```

参数x和y为需要比较的两个数，返回值为它们的最小值。

Max函数用于求两个数的最大值，其定义如下：

```go
func Max(x, y float64) float64
```

参数x和y为需要比较的两个数，返回值为它们的最大值。

需要注意的是，Min和Max函数的参数类型必须是float64，**如果传入的参数类型为int等其它类型，需要进行类型转换**。

例如，

```go
func maxDepth(root *TreeNode) int {
    if (root == nil){
        return 0
    }else{
        return int(1.0 + math.Max(float64(maxDepth(root.Left)),float64(maxDepth(root.Right))))
    }
}
```

2. 比较int不如自己写

```go
func max(a int, b int) int{
    if(a>=b){
        return a
    }else{
        return b
    }
}
func min(a int, b int) int{
    if(a<=b){
        return a
    }else{
        return b
    }
}
```



# 四、并发编程

## 概要

> 并发是 golang 的优势之一，使用关键字 go 可以很方便的开启一个**协程**. go 语言中，常常用 **go**、**chan**、**select** 及 **sync 库**完成并发操作，处理**同步**、**异步**、**阻塞**、**非阻塞**任务.



go 语言的并发编程，以下是需要了解的基础知识点，也是本文主要介绍的内容. 可以对照看看这些是否已经可以熟练运用了.

- **阻塞**: 阻塞是进程(也可以是线程、协程)的状态之一（新建、就绪、运行、阻塞、终止). 指的是当数据未准备就绪，这个进程(线程、协程)一直等待，这就是阻塞.

- **非阻塞**: 当数据为准备就绪，该进程(线程、协程)不等待可以继续执行，这就是非阻塞.

- **同步**: 在发起一个调用时，在没有得到结果之前，这个调用就不返回，这个调用过程一直在等待. 这是同步.

- **异步**: 在发起调用后，就立刻返回了，这次调用过程就结束了. 等到有结果了被调用方主动通知调用者结果. 这是异步.

- **go(协程)**: 通过关键字 go 即可创建一个协程.

- **chan :** golang 中用于并发的通道，用于协程的通信.

- - **有缓冲通道**
  - **无缓冲通道**
  - **单向通道**

- **select**: golang 提供的多路复用机制.

- **close()**: golang 的内置函数, 可以关闭一个通道.

- **sync**: golang 标准库之一，提供了锁.

- **定时器**: golang 标准库 time 提供的重要功能, 提供了定时器功能，可用于超时处理.

- - **Timer**
  - **Ticker**

- 

## 协程原理

### 协程的相关概念

#### 概念：主协程

**Go不支持创建系统线程**，所以协程是一个Go程序内部**唯一的并发实现方式**。

每个Go程序启动的时候只有一个对用户可见的协程，我们称之为**主协程**。 一个协程可以开启更多其它新的协程。在Go中，开启一个新的协程是非常简单的。 我们只需在一个函数调用之前使用一个`go`关键字，即可让此函数调用运行在一个新的协程之中。 当此函数调用退出后，这个新的协程也随之结束了。我们可以称此函数调用为一个协程调用（或者为此协程的启动调用）。 一个协程调用的所有返回值（如果存在的话）必须被全部舍弃。

#### 概念：协程的状态

一个活动中的协程可以处于两个状态：**运行状态**和**阻塞状态**。一个协程可以在这两个状态之间切换。

下面的图片显示了**一个协程的生命周期**。

![协程状态](https://gitee.com/yushen611/img/raw/master/goroutine-states.png)



注意，**一个处于睡眠中的（通过调用`time.Sleep`）或者在等待系统调用返回的协程被认为是处于运行状态**，而不是阻塞状态。

当一个新协程被创建的时候，它将自动进入运行状态，**一个协程只能从运行状态而不能从阻塞状态退出**。 如果因为某种原因而导致某个协程一直处于阻塞状态，则此协程将永远不会退出。 除了极个别的应用场景，在编程时我们应该尽量避免出现这样的情形。

#### 概念：协程死锁

**一个处于阻塞状态的协程不会自发结束阻塞状态，它必须被另外一个协程通过某种并发同步方法来被动地结束阻塞状态**。 如果**一个运行中的程序当前所有的协程都出于阻塞状态**，则这些协程将永远阻塞下去，程序将被视为**死锁**了。 当一个程序死锁后，官方标准编译器的处理是让这个程序崩溃。

#### 概念：同步、异步、阻塞、非阻塞组合的四种并发模式

同步、异步、阻塞、非阻塞可以组合成四种并发方式:

- **同步阻塞调用**：得不到结果不返回，协程进入阻塞态等待。
- **同步非阻塞调用**：得不到结果不返回，协程不阻塞一直在CPU运行。
- **异步阻塞调用**：去到别的协程，让别的协程阻塞起来等待结果，自己不阻塞。
- **异步非阻塞调用**：去到别的协程，别的协程一直在运行，直到得出结果。

#### 概念：协程调度

在任一时刻，只能最多有和逻辑CPU数目一样多的协程在同时执行。 我们可以调用[`runtime.NumCPU`](https://golang.google.cn/pkg/runtime/#NumCPU)函数来查询当前程序可利用的逻辑CPU数目。 每个逻辑CPU在同一时刻只能最多执行一个协程。Go运行时（runtime）必须让逻辑CPU频繁地在不同的处于运行状态的协程之间切换，从而每个处于运行状态的协程都有机会得到执行。 这和操作系统执行系统线程的原理是一样的。

下面这张图显示了**一个协程的更详细的生命周期**。在此图中，运行状态被细分成了多个子状态。 一个处于排队子状态的协程等待着进入执行子状态。一个处于执行子状态的协程在被执行一会儿（非常短的时间片）之后将进入排队子状态。

![](https://gitee.com/yushen611/img/raw/master/20230715141752.png)



标准编译器采纳了**一种被称为[M-P-G模型](https://docs.google.com/document/d/1TTj4T2JO42uD5ID9e89oa0sLKhJYD0Y_kqxDv3I3XMw)的算法来实现协程调度**。 其中，**M**表示系统线程，**P**表示逻辑处理器（并非上述的逻辑CPU），**G**表示协程。 大多数的调度工作是通过逻辑处理器（**P**）来完成的。 逻辑处理器像一个监工一样通过将不同的处于运行状态协程（**G**）交给不同的系统线程（**M**）来执行。 一个协程在同一时刻只能在一个系统线程中执行。一个执行中的协程运行片刻后将自发地脱离让出一个系统线程，从而使得其它处于等待子状态的协程得到执行机会。

在运行时刻，我们可以调用[`runtime.GOMAXPROCS`](https://golang.google.cn/pkg/runtime/#GOMAXPROCS)函数来获取和设置逻辑处理器的数量。 对于官方标准编译器，在Go 1.5之前，默认初始逻辑处理器的数量为1；自从Go 1.5之后，默认初始逻辑处理器的数量和逻辑CPU的数量一致。 此新的默认设置在大多数情况下是最佳选择。但是对于某些文件操作十分频繁的程序，设置一个大于`runtime.NumCPU()`的`GOMAXPROCS`值可能是有好处的。

我们也可以通过设置`GOMAXPROCS`环境变量来设置一个Go程序的初始逻辑处理器数量。



### 协程与线程的区别



**最主要区别**：

1. 调度机制：

   * 在Golang中，**协程（goroutine）是由Go运行时的用户态调度器进行调度的，而不是由操作系统进行调度**。Golang的调度器采用的是M:N调度模型，即将协程（goroutine）调度到少量的OS线程（线程数由GOMAXPROCS控制）上执行。这使得**协程的调度开销较小，可以在用户态大规模地创建和切换协程，实现高并发的编程模型**。

   * **而线程是由操作系统进行调度**，每个线程都有自己的栈和上下文切换开销，因此**线程的调度开销相对较高**。

2. 内存占用：

   * 协程的栈空间相对较小，通常只有几KB，而线程的栈空间较大，通常为几MB。因此，同样数量的线程相比协程，会占用更多的内存。

**其余区别**：

1. 创建和销毁：**协程的创建和销毁比线程更轻量化。**协程的创建和销毁可以在用户态进行，不需要操作系统的参与，而线程的创建和销毁需要通过系统调用。
2. 并发性：**协程可以在多个线程中并发执行，每个线程可以拥有多个协程。**而线程是操作系统进行调度的最小执行单元，可以并发执行多个线程，但每个线程只能执行一个任务。
3. 同步与通信：**协程通过通道（channel）来进行协程间的同步与通信，而线程通常使用锁、条件变量等底层的同步机制进行线程间的同步与通信**。
4. 异常处理：**协程可以使用defer和recover来进行异常处理**，而**线程需要使用try-catch语句**来捕获异常。
5. 编程模型：**协程通常使用基于消息传递的编程模型**，即**通过发送和接收消息来进行协程间的通信**。而**线程通常使用共享内存**的编程模型，即通过对共享内存的读写来进行线程间的通信。
6. 调试与排查问题：由于协程是由用户态调度器进行调度，调试和排查问题相对困难一些，需要使用相关的调试工具来进行诊断。而线程通常由操作系统进行调度，可以使用操作系统提供的调试工具进行调试和排查问题。

## channel

golang 提供了通道类型 chan，用于在并发操作时的通信，它本身就是并发安全的. 通过 chan 可以创建无缓冲、缓冲通道，满足不同需求. 写法如下:

```text
make(chan int)
make(chan int, 10)
<- chan
chan <-
```

**无缓冲通道:** 要求接受和发送数据的 goroutine 同时准备好，否则将会阻塞.

**有缓冲通道:** 给予通道一个容量值，只要有值便可以接受数据，有空间便可以发送数据，可以不阻塞的完成.

**单向通道**: 默认情况通道是双向的，可以接受及发送数据. 也可以创建单向通道，只能收或者发数据. 如下是单向接受通道

```text
var ch chan
  <- float64
```



close() 函数用于关闭通道 channel 的，close 之后的 channel 还可以读取数据，close() 函数由以下几点使用要点:

1. 只能关闭双向通道或者发送通道
2. 它应该由发送者使用，而不应该由接受者调用
3. 当通道关闭后，接受者都不再阻塞，
4. 关闭通道后，依然可以从通道中读取值
5. 所有元素读取完后，将返回通道元素的零值，并且读取检测值也是 false

示例:

```text
ch := make(chan int, 1)
ch <- 3
close(ch) // 关闭ch
v, ok := <- ch // 3,true
v2,ok := <- ch // 0,false
```

## select

**select:** 可以监听 channel 上的输入/输出操作, 类似于 select、epoll、poll 使得通道支持多路复用. select 是专门通道 channel 设计的. 它可以结合通道实现**超时处理、判断缓冲通道是否阻塞、退出信号量处理**，如下:

```go
var c1, c2 <-chan interface{}
var c3 chan<- interface{}
select {
    case <- c1:         //监听channel的读事件
        // Do something
    case <- c2:         //读事件
        // Do something
    case c3<- struct{}{}:   //监控channel的写事件
        // Do something
}
```



## sync标准库的锁

并发编程中，为了确保并发安全，可以使用锁机制. golang 提供了标准库 sync ，它实现了并发需要的各种锁. 包括:

- **Mutex**: 互斥锁，有俩个方法 **Lock()** 和 **Unlock()**, 它只能同时被一个 goroutine 锁定，其它锁再次尝试锁定将被阻塞，直到解锁.
- **RWMutex**: 读写锁，有四个方法，**Lock()**写锁定、**Unlock()**写解锁、**RLock()**读锁定、**RUnlock()**读解锁，读锁定和写锁定只能同时存在一个. 只能有一个协程处于写锁定状态，但是可以有多个协程处于读锁定状态. 即写的时候不可读，读的时候不可写. 只能同时有一个写操作确保数据一致性. 而可以多个协程同时读数据，确保读操作的并发性能.

此外在 go 的并发编程中，还会常用到 sync 的以下内容:

- **sync.Map**: 并发安全的字典 map
- **sync.WaitGroup**: 用来等待一组协程的结束，常常用来阻塞主线程.
- **sync.Once**: 用于控制函数只能被使用一次，
- **sync.Cond**: 条件同步变量. 可以通过 Wait()方法阻塞协程，通过 Signal()、Broadcast() 方法唤醒协程.
- **sync.Pool**: 一组临时对象的集合，是并发安全的. 它主要是用于存储分配但还未被使用的值，避免频繁的重新分配内存，减少 gc 的压力.



sync.WaitGroup：用来等待一组子协程的结束，需要设置等待的个数，每个子协程结束后要调用Done()，最后在主协程中Wait()即可

- Add()：添加计数
- Done()：操作结束时调用，计数减去1
- Wait()：主函数调用，等待所有操作结束

## time 标准库定时器

golang 的标准库 time 中提供了定时器功能，并提供通道 channel 变量进行定时通知. time 库中提供了两种定时器:

- **time.Timer:** 定时器 timer 在创建指定时间后，向通道 time.Timer.C 发送数据. 之后需要使用 Reset 设定定时器时间.
- **time.Ticker:** 周期性定时器. 会按照初设定的时间重复计时.

**示例:**

```text
// timer
for {
  <- timer.C
  timer.Reset(time.Second)
  // 重设后才有效
}

// ticker
for {
  <- ticker.C // 周期性有效
}
```

## 思考题目

1. golang 中 select 的多个 case 同时成立，那么选择的是哪一个?

2. golang 中除了使用 sync 锁，还可以如何保证并发安全? atomic 是什么？

3. sync.Map 对键的类型有什么要求么？

4. 如何避免死锁? golang 中如何检测死锁?
