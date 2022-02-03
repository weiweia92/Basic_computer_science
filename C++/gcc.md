## GCC编译器

### 前言

1.GCC编译器支持变异Go、Objective-C、Objective-C++、Fortran、Ada、D和BRIG(HSAIL)等程序。

2.VSCode是通过调用GCC编译器来实现C/C++的编译工作的。

### 编译过程

#### 1.Pre-processing
```
g++ -E test.cpp -o test.i 
```

#### 2.Compiling
```
# -S 编译选项告诉g++在为C++代码产生了汇编文件后停止编译，g++产生的汇编文件的缺省扩展名是.s
g++ -S test.i -o test.s
```

#### 3.汇编 Assembling
```
# -c选项告诉g++仅把源代码编译为机器语言(计算机可以识别的二进制代码)的目标代码
# 缺省时 g++ 建立的目标代码文件有一个.o的扩展名
g++ -c test.s -o test.o
```

#### 4.Linking //bin文件
```
# -o:为将产生的可执行文件用指定的文件名
g++ test.o -o test
```

### g++重要编译参数

#### 1. -g 编译带调试信息的可执行文件
```
g++ -g test.cpp -o test
```

#### 2. -O 优化源代码
```
## 所谓优化，例如省略掉代码中从未使用过的变量、直接将常量表达式用结果值代替等等，这些操作 会缩减目标文件所包含的代码量，提高最终生成的可执行文件的运行效率。
# -O 选项告诉 g++ 对源代码进行基本优化。这些优化在大多数情况下都会使程序执行的更快。 -O2 
选项告诉 g++ 产生尽可能小和尽可能快的代码。如-O2，-O3，-On（n 常为0–3）
# -O 同时减小代码的长度和执行时间，其效果等价于-O1 
# -O0 表示不做优化
# -O1 为默认优化
# -O2 除了完成-O1的优化之外，还进行一些额外的调整工作，如指令调整等。 
# -O3 则包括循环展开和其他一些与处理特性相关的优化工作。
# 选项将使编译的速度比使用 -O 时慢但通常产生的代码执行速度会更快。 
# 使用 -O2优化源代码，并输出可执行文件
g++ -O2 test.cpp
```

#### 3.-l和-L指定库文件 ｜ 指定库文件路径
```
# -l参数(小写)就是用来指定程序要链接的库，-l参数紧接着就是库名 
# 在/lib和/usr/lib和/usr/local/lib里的库直接用-l参数就能链接

# 链接glog库
g++ -lglog test.cpp

# 如果库文件没放在上面三个目录里，需要使用-L参数(大写)指定库文件所在目录 
# -L参数跟着的是库文件所在的目录名

# 链接mytest库，libmytest.so在/home/bing/mytestlibfolder目录下 
g++ -L/home/bing/mytestlibfolder -lmytest test.cpp
```

#### 4. -I 指定头文件搜索目录
```
# -I
# /usr/include目录一般是不用指定的，gcc知道去那里找，但    是如果头文件不在/usr/icnclude 
里我们就要用-I参数指定了，比如头文件放在/myinclude目录里，那编译命令行就要加上-I/myinclude 参数了，如果不加你会得到一个”xxxx.h: No such file or directory”的错误。-I参数可以用相对路径，比如头文件在当前目录，可以用-I.来指定。上面我们提到的–cflags参数就是用来生成-I参数的。

g++ -I/myinclude test.cpp
```

#### 5.-Wall 打印警告信息
```
g++ -Wall test.cpp
```

#### 6.-w 关闭警告信息
```
g++ -w test.cpp
```

#### 7.-std=c++11 设置编译标准
```
g++ -std=c++11 test.cpp
```

#### 8.-o 指定输出文件名
```
# default a.out
g++ test.cpp -o test
```

#### 9.-D 定义宏
```
# 在使用gcc/g++编译的时候定义宏

# 常用场景：
# - DDEBUG 定义 DEBUG 宏，可能文件中有DEBUG宏部分的相关信息，用DDEBUG 来选择开启或者关闭DEBUG
```
示例代码：
```
// -Dname 定义宏name,默认定义内容为字符串“1”
#include <stdio.h> 
int main()
{
    #ifdef DEBUG
printf("DEBUG LOG\n");
    #endif
printf("in\n"); 
}
// 1. 在编译的时候，使用gcc -DDEBUG main.cpp 
// 2. 第七行代码可以被执行
```

### 实战-g++命令行编译

```
# 最初目录结构
.
├── include 
│   └── Swap.h 
├── main.cpp 
└── src
    └── Swap.cpp
2 directories, 3 files
```
#### 1. 直接编译     

最简单的编译并运行
```
# 将 main.cpp src/Swap.cpp 编译为可执行文件 
# -I:指定头文件路径
g++ main.cpp src/Swap.cpp -Iinclude
# 运行a.out
./a.out
```
增加参数编译并运行
```
# 将 main.cpp src/Swap.cpp 编译为可执行文件    附带一堆参数
g++ main.cpp src/Swap.cpp -Iinclude -std=c++11 -O2 -Wall -o b.out 

./b.out
```
#### 2. 生成库文件并编译

链接静态库生成可执行文件
```
## 进入src目录下 
$cd src
# 汇编，生成Swap.o文件
g++ Swap.cpp -c -I../include 
# 生成静态库libSwap.a
ar rs libSwap.a Swap.o
## 回到上级目录 
$cd ..
# 链接，生成可执行文件:staticmain
g++ main.cpp -Iinclude -Lsrc -lSwap -o staticmain
```
链接动态库生成可执行文件   
```
## 进入src目录下 
$cd src
# 生成动态库libSwap.so
g++ Swap.cpp -I../include -fPIC -shared -o libSwap.so 
## 上面命令等价于以下两条命令
# gcc Swap.cpp -I../include -c -fPIC 
# gcc -shared -o libSwap.so Swap.o
## 回到上级目录 
$cd ..
# 链接，生成可执行文件:sharemain
g++ main.cpp -Iinclude -Lsrc -lSwap -o sharemain
```
编译完成后的目录结构    
```
# 最终目录结构
.
├── include 
│   └── Swap.h
├── main.cpp 
├── sharemain 
├── src
│   ├── libSwap.a
│   ├── libSwap.so
│   ├── Swap.cpp
│   └── Swap.o
└── staticmain
2 directories, 8 files
```
#### 3. 运行可执行文件    

运行可执行文件1
```
./staticmain
```
运行可执行文件2
```
LD_LIBRARY_PATH=src ./sharemain
```
## GDB 调试器

### 前言

- GDB(GNU Debugger)是一个用来调试C/C++程序的功能强大的调试器，是Linux系统开发 C/C++最常用的调试器

- 程序员可以使用GDB来跟踪程序中的错误，从而减少程序员的工作量。 

- Linux  开发C/C++ 一定要熟悉 GDB

- VSCode是通过调用GDB调试器来实现C/C++的调试工作的；

>Windows 系统中，常见的集成开发环境（IDE），如 VS、VC等，它们内部已经嵌套了相应的调试器

### GDB主要功能:

- 设置断点(断点可以是条件表达式)

- 使程序在指定的代码行上暂停执行，便于观察 

- 单步执行程序，便于调试

- 查看程序中变量值的变化 

- 动态改变程序的执行环境 

- 分析崩溃程序产生的core文件

#### 4.1 常用调试命令参数

调试开始：执行`gdb [exefilename]` ，进入gdb调试程序，其中exefilename为要调试的可执行文件名

```
## 以下命令后括号内为命令的简化使用，比如run（r），直接输入命令 r 就代表命令run

$(gdb)help(h)   # 查看命令帮助，具体命令查询在gdb中输入help + 命令

$(gdb)run(r)    # 重新开始运行文件（run-text：加载文本文件,run-bin：加载二进制文件）

$(gdb)start     # 单步执行，运行程序，停在第一行执行语句

$(gdb)list(l)   # 查看原代码（list-n,从第n行开始查看代码。list+ 函数名：查看具体函数）

$(gdb)set       # 设置变量的值

$(gdb)next(n)   # 单步调试（逐过程，函数直接执行）

$(gdb)step(s)   # 单步调试（逐语句：跳入自定义函数内部执行）

$(gdb)backtrace(bt) # 查看函数的调用的栈帧和层级关系

$(gdb)frame(f)  # 切换函数的栈帧

$(gdb)info(i)   # 查看函数内部局部变量的数值

$(gdb)finish    # 结束当前函数，返回到函数调用点

$(gdb)continue(c)   # 继续运行

$(gdb)print(p)  # 打印值及地址

$(gdb)quit(q)   # 退出gdb

$(gdb)break+num(b)          # 在第num行设置断点 

$(gdb)info breakpoints      # 查看当前设置的所有断点 

$(gdb)delete breakpoints num(d) # 删除第num个断点 

$(gdb)display               # 追踪查看具体变量值 

$(gdb)undisplay             # 取消追踪观察变量

$(gdb)watch   # 被设置观察点的变量发生修改时，打印显示

$(gdb)i watch               # 显示观察点 

$(gdb)enable breakpoints    # 启用断点 

$(gdb)disable breakpoints   # 禁用断点
```
>**Tips:**
>1. 编译程序时需要加上-g，之后才能用gdb进行调试：gcc -g main.c -o main
>2. 回车键：重复上一命令